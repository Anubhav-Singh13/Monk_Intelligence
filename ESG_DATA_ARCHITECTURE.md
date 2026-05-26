# ESG Custodian — Data Architecture

> A document-first, multi-tenant **SaaS** platform for building ESG business cases.  
> Combines unstructured document retrieval (RAG) with a tiered financial modelling engine — from deterministic NPV/IRR through to Monte Carlo stochastic simulation.

---

## Table of Contents

- [Architecture Overview](#architecture-overview)
- [Financial Modelling Tiers](#financial-modelling-tiers)
- [Data Flow — Document Ingestion](#data-flow--document-ingestion)
- [Data Flow — Business Case Creation](#data-flow--business-case-creation)
- [Core Data Model](#core-data-model)
- [Tenant Isolation Strategy](#tenant-isolation-strategy)
- [Architectural Decisions](#architectural-decisions)
- [Technology Stack](#technology-stack)

---

## Architecture Overview

Eight layers across the SaaS platform. The tenant boundary is enforced at every layer — storage, query, and API.

```mermaid
flowchart TD
    subgraph ING["① Ingestion Layer"]
        D1[/"ESG & Sustainability Reports"/]
        D2[/"Regulatory Filings\nGRI · TCFD · SASB · CSRD · EU Taxonomy"/]
        D3[/"Internal Policy & Vendor Docs"/]
        D4[/"Financial Spreadsheets & Manual KPI Entry"/]
        D5[/"External Feeds — Bloomberg · MSCI · CDP"/]
    end

    subgraph RAW["② Raw Storage  ·  Tenant-Partitioned"]
        BLOB[("Blob Store\nS3 / Azure Blob\n/tenant_{id}/raw/\n/tenant_{id}/processed/")]
        DOCREG[("Document Registry\ntenant_id · doc_id\nframework_tags · status")]
    end

    subgraph PIPE["③ Processing Pipeline"]
        PARSER["Doc Parser\nOCR + Table Detection"]
        CHUNKER["Semantic Chunker"]
        EMBEDDER["Embedder\ntext-embedding model"]
        EXTRACTOR["Financial Extractor\nTable rows → structured metrics"]
    end

    subgraph KL["④ Knowledge Layer  ·  Tenant-Isolated"]
        VECTOR[("Vector Store\nOne namespace per tenant\nPinecone / Qdrant / pgvector")]
        STRUCT[("Structured Store\nRow-level security on tenant_id\nPostgreSQL / Snowflake")]
        REF[("Reference Data\nShared · Read-only\nBenchmarks · Carbon curves · Frameworks")]
    end

    subgraph BCE["⑤ Business Case Engine"]
        RRET["Context Retriever\nRAG — semantic search\nfiltered by tenant + framework + ESG pillar"]
        FMOD["Financial Modelling Engine\nTier 1 · Tier 2 · Tier 3\n(see Financial Modelling Tiers)"]
        ESGSC["ESG Scoring Engine\nPillar scores · Gap vs framework\nMateriality matrix · Trend vs benchmark"]
        BCASE[("Business Case Store\ntenant_id · case_id\nscenarios · simulation runs\nchunk citations · narrative")]
    end

    subgraph SRV["⑥ Serving Layer"]
        GW["API Gateway\nJWT — tenant_id injected into every request"]
        BCAPI["Business Case API"]
        DOCAPI["Document API"]
        METAPI["Metrics API"]
        DASHAPI["Analytics & Dashboard API"]
    end

    subgraph SAAS["⑦ SaaS Platform Layer"]
        TMS["Tenant Management Service\nOnboarding · Plan · Settings"]
        METER["Usage Metering\nDocs processed · Cases run · API calls\nSimulation compute units"]
        BILLING["Billing Integration\nPlan enforcement · Overage alerts"]
        AUDIT["Audit Log\nImmutable per-tenant event stream"]
    end

    subgraph OPS["⑧ Ops & Observability"]
        MON["Monitoring & Alerting\nPer-tenant error rates · Latency · Queue depth"]
        ADMIN["Admin Console\nTenant health · Support tooling"]
    end

    D1 & D2 & D3 --> BLOB
    D4 & D5 --> STRUCT
    BLOB --> DOCREG
    BLOB --> PARSER
    PARSER --> CHUNKER
    CHUNKER --> EMBEDDER
    CHUNKER --> EXTRACTOR
    EMBEDDER --> VECTOR
    EXTRACTOR --> STRUCT
    VECTOR --> RRET
    STRUCT --> FMOD
    STRUCT --> ESGSC
    REF --> ESGSC
    REF --> FMOD
    RRET & FMOD & ESGSC --> BCASE
    BCASE --> GW
    GW --> BCAPI & DOCAPI & METAPI & DASHAPI
    GW --> METER
    METER --> BILLING
    GW --> AUDIT
    TMS --> GW
    METER & AUDIT --> MON
    MON --> ADMIN
```

---

## Financial Modelling Tiers

The engine supports three tiers invoked progressively — a business case always has at least one Tier 1 scenario; Tiers 2 and 3 are additive.

```mermaid
flowchart LR
    subgraph T1["Tier 1 — Deterministic  (always run)"]
        T1A["NPV · IRR"]
        T1B["Payback Period"]
        T1C["TCO · ROI"]
        T1D["Carbon Avoidance Cost\n(tonne CO₂e avoided / $ spent)"]
    end

    subgraph T2["Tier 2 — Scenario Analysis  (on request)"]
        T2A["Base · Bull · Bear\nScenario Comparison"]
        T2B["Single-variable\nSensitivity Sweep"]
        T2C["Break-even Analysis\n(carbon price / energy cost)"]
        T2D["Tornado Chart Data\nranked variable impact"]
    end

    subgraph T3["Tier 3 — Stochastic  (on request)"]
        T3A["Monte Carlo Simulation\n10 000 iterations"]
        T3B["Outcome Probability\nDistribution (NPV · IRR)"]
        T3C["Value at Risk — VaR 95%\non ESG investment"]
        T3D["Multi-period DCF\nwith stochastic rate path"]
    end

    T1 -->|"scenario_inputs\n+ parameter ranges"| T2
    T2 -->|"parameter distributions\n+ correlation matrix"| T3
```

### Input Parameters per Tier

| Parameter | Tier 1 | Tier 2 | Tier 3 |
|---|:---:|:---:|:---:|
| CAPEX / OPEX (point estimate) | ✓ | ✓ | ✓ |
| Revenue / cost impact | ✓ | ✓ | ✓ |
| Discount rate | ✓ | ✓ | ✓ |
| Carbon price | ✓ | ✓ | ✓ |
| Named scenario overrides (base / bull / bear) | | ✓ | ✓ |
| Variable sweep range + step | | ✓ | ✓ |
| Probability distributions per variable | | | ✓ |
| Correlation matrix across variables | | | ✓ |
| Number of Monte Carlo iterations | | | ✓ |

---

## Data Flow — Document Ingestion

```mermaid
sequenceDiagram
    actor User
    participant GW as API Gateway
    participant DS as Document Service
    participant BLOB as Blob Store
    participant PIPE as Processing Pipeline
    participant VEC as Vector Store
    participant DB as Structured Store
    participant METER as Usage Metering

    User->>GW: POST /documents  (JWT with tenant_id claim)
    GW->>DS: Validated request + tenant_id
    DS->>BLOB: Write to /tenant_{id}/raw/{doc_id}
    DS->>DB: INSERT document registry row (status = queued)
    DS-->>User: 202 Accepted  { doc_id }

    BLOB-->>PIPE: Storage event trigger
    PIPE->>PIPE: OCR + table detection
    PIPE->>PIPE: Semantic chunking (tenant_id in every chunk metadata)
    PIPE->>PIPE: Embed each chunk
    PIPE->>VEC: Upsert chunks into tenant namespace
    PIPE->>PIPE: Extract financial rows from tables
    PIPE->>DB: INSERT esg_metrics + financial_inputs rows
    PIPE->>DB: UPDATE document status = ready
    PIPE->>BLOB: Write processed text to /tenant_{id}/processed/
    PIPE->>METER: Emit event  { tenant_id, pages_processed, chunks_embedded }
```

---

## Data Flow — Business Case Creation

```mermaid
sequenceDiagram
    actor User
    participant GW as API Gateway
    participant BCE as Business Case Engine
    participant VEC as Vector Store
    participant DB as Structured Store
    participant LLM as LLM
    participant METER as Usage Metering

    User->>GW: POST /business-cases  (scenario inputs + modelling tier)
    GW->>BCE: Run(tenant_id, query, scenario_inputs, tier)

    BCE->>VEC: Semantic search filtered by tenant_id + framework_tag + esg_pillar
    VEC-->>BCE: Ranked chunks with doc_id + page citations

    BCE->>DB: SELECT esg_metrics, financial_inputs WHERE tenant_id = ?
    BCE->>DB: SELECT reference_data (benchmarks, carbon price curve)
    DB-->>BCE: Structured data

    alt Tier 1 — Deterministic
        BCE->>BCE: Compute NPV · IRR · Payback · TCO · Carbon avoidance cost
    else Tier 2 — Scenario Analysis
        BCE->>BCE: Run base/bull/bear scenarios + sensitivity sweep + break-even
    else Tier 3 — Stochastic
        BCE->>BCE: Sample parameter distributions (10 000 iterations)
        BCE->>BCE: Compute outcome distribution · VaR 95% · confidence intervals
    end

    BCE->>BCE: Score ESG pillars · gap vs framework · vs benchmark

    BCE->>LLM: Prompt = system + retrieved chunks + computed outputs + task
    LLM-->>BCE: Narrative + structured section outputs

    BCE->>DB: INSERT business_cases row (outputs + supporting_chunk_ids)
    BCE->>DB: INSERT scenario rows + simulation_run row (if Tier 3)
    BCE->>METER: Emit event  { tenant_id, tier, simulation_iterations }
    BCE-->>User: Business case (financial model + ESG scores + narrative + citations)
```

---

## Core Data Model

```mermaid
erDiagram
    TENANT {
        uuid tenant_id PK
        string name
        string industry
        string plan
        string status
        timestamp created_at
    }

    DOCUMENT {
        uuid doc_id PK
        uuid tenant_id FK
        string type
        string[] framework_tags
        string esg_pillar
        string source_system
        int fiscal_year
        string processing_status
        timestamp upload_ts
    }

    CHUNK {
        uuid chunk_id PK
        uuid doc_id FK
        uuid tenant_id FK
        text content
        vector embedding
        string esg_pillar
        string data_type
        int page_number
        string section_heading
    }

    ESG_METRIC {
        uuid metric_id PK
        uuid tenant_id FK
        uuid source_doc_id FK
        string category
        string sub_category
        decimal value
        string unit
        int fiscal_year
        string framework_tag
    }

    FINANCIAL_INPUT {
        uuid input_id PK
        uuid tenant_id FK
        uuid project_id FK
        decimal capex
        decimal opex
        decimal revenue_impact
        decimal discount_rate
        int horizon_years
        decimal carbon_price_usd_per_tonne
        decimal energy_cost_per_kwh
    }

    BUSINESS_CASE {
        uuid case_id PK
        uuid tenant_id FK
        string project_name
        jsonb base_inputs
        jsonb esg_scores
        uuid[] supporting_chunk_ids
        text narrative
        int version
        timestamp created_at
    }

    SCENARIO {
        uuid scenario_id PK
        uuid case_id FK
        uuid tenant_id FK
        string name
        string type
        int tier
        jsonb parameter_overrides
        decimal npv
        decimal irr
        decimal payback_years
        decimal tco
        decimal carbon_avoidance_cost
        jsonb sensitivity_results
        timestamp created_at
    }

    SIMULATION_RUN {
        uuid run_id PK
        uuid scenario_id FK
        uuid tenant_id FK
        int num_iterations
        jsonb parameter_distributions
        jsonb correlation_matrix
        jsonb npv_distribution
        jsonb irr_distribution
        decimal p10_npv
        decimal p50_npv
        decimal p90_npv
        decimal var_95_npv
        timestamp run_at
    }

    REFERENCE_DATA {
        uuid ref_id PK
        string framework
        string category
        string sub_category
        string industry
        decimal benchmark_value
        string unit
        int year
    }

    USAGE_EVENT {
        uuid event_id PK
        uuid tenant_id FK
        string event_type
        jsonb dimensions
        timestamp occurred_at
    }

    TENANT ||--o{ DOCUMENT : "owns"
    TENANT ||--o{ ESG_METRIC : "has"
    TENANT ||--o{ FINANCIAL_INPUT : "defines"
    TENANT ||--o{ BUSINESS_CASE : "generates"
    TENANT ||--o{ USAGE_EVENT : "emits"
    DOCUMENT ||--o{ CHUNK : "split into"
    DOCUMENT ||--o{ ESG_METRIC : "source for"
    BUSINESS_CASE ||--o{ SCENARIO : "has"
    SCENARIO ||--o| SIMULATION_RUN : "has (Tier 3 only)"
    CHUNK }o--o{ BUSINESS_CASE : "cited in"
    FINANCIAL_INPUT }o--o{ BUSINESS_CASE : "used by"
```

---

## Tenant Isolation Strategy

Isolation is applied at every layer — a breach at one layer cannot expose another tenant's data.

| Layer | Isolation Mechanism |
|---|---|
| **Blob Store** | Key prefix `/tenant_{id}/` — IAM policies enforce prefix-scoped access per tenant service account |
| **Document Registry** | `tenant_id` column; application always appends `WHERE tenant_id = :tid`; no cross-tenant joins permitted |
| **Vector Store** | Dedicated namespace per tenant; every query includes namespace + metadata filter on `tenant_id` |
| **Structured Store** | PostgreSQL Row-Level Security (RLS) on `tenant_id`; service account has no `BYPASSRLS` privilege |
| **Business Case Store** | Same RLS policy; `supporting_chunk_ids` validated against tenant namespace before persistence |
| **API Gateway** | JWT `tenant_id` claim injected server-side; clients cannot supply or override it |
| **Usage & Audit** | `USAGE_EVENT` rows partitioned by `tenant_id`; billing aggregation runs within partition boundary |
| **Reference Data** | Shared read-only schema in a separate DB role; no tenant writes permitted |

### SaaS Plan Enforcement

```mermaid
flowchart LR
    REQ["Incoming API Request"] --> GW["API Gateway\nJWT validation + tenant_id"]
    GW --> PE["Plan Enforcer\nCheck tenant plan limits"]
    PE -->|"Within limits"| BCE["Business Case Engine"]
    PE -->|"Over limit"| ERR["429 / 402\nUpgrade prompt"]
    BCE --> METER["Usage Metering\nDecrement quota / emit event"]
    METER --> BILLING["Billing Service\nStripe metered billing"]
```

| Limit | Starter | Professional | Enterprise |
|---|:---:|:---:|:---:|
| Documents / month | 50 | 500 | Unlimited |
| Business cases / month | 10 | 100 | Unlimited |
| Financial modelling tier | Tier 1 only | Tier 1 + 2 | Tier 1 + 2 + 3 |
| Monte Carlo iterations | — | — | Up to 100 000 |
| Data retention | 1 year | 3 years | Configurable |

---

## Architectural Decisions

| Concern | Decision | Rationale |
|---|---|---|
| **Document retrieval** | RAG (chunk → embed → vector search) | Documents too large and varied for full-context prompts; semantic retrieval scales across hundreds of reports per tenant |
| **Financial data** | Separate relational store, not vector | Financial modelling requires exact arithmetic and aggregation — SQL is deterministic, LLMs are not |
| **Modelling tiers** | Progressive enrichment (T1 → T2 → T3) on same input set | Avoids recomputing base outputs; Tier 3 runs use Tier 2 scenarios as seed inputs |
| **Simulation storage** | `SIMULATION_RUN` with distribution snapshots, not raw iteration rows | 10 000 rows per run × many cases per tenant is prohibitive; store percentiles + full distribution as JSONB |
| **Citation trail** | `supporting_chunk_ids[]` on every business case | Regulatory-grade auditability; any claim in the narrative traces back to source doc + page |
| **Tenant namespace per vector store** | One namespace per tenant, not one collection | Balances isolation cost with operational overhead; metadata filter adds defence-in-depth |
| **Framework tagging at ingest** | Tags on chunk metadata (GRI, TCFD, SASB…) | Enables filtered retrieval by regulatory context without re-embedding |
| **Usage metering async** | Events emitted post-response, processed by metering service | Keeps API latency unaffected by billing logic; quota enforcement uses cached counter |
| **Plan enforcement at gateway** | Checked before engine invocation | Prevents compute spend on requests that will be denied |

---

## Technology Stack

| Layer | Options |
|---|---|
| **Blob Store** | AWS S3 · Azure Blob Storage |
| **Structured Store** | PostgreSQL (primary) · Snowflake (analytics workloads) |
| **Vector Store** | Pinecone · Qdrant · pgvector (simpler stack, co-located with Postgres) |
| **Processing Pipeline** | AWS Lambda · Azure Functions · Apache Airflow (batch reprocessing) |
| **Doc Parser / OCR** | Azure Document Intelligence · AWS Textract · `pdfplumber` (open source) |
| **Embedder** | OpenAI `text-embedding-3-large` · Azure OpenAI · `sentence-transformers` (self-hosted) |
| **LLM** | Claude (Anthropic) · Azure OpenAI GPT-4o |
| **Monte Carlo Engine** | NumPy / SciPy (Python) · server-side compute, not LLM |
| **API Gateway** | AWS API Gateway · Azure APIM · Kong |
| **Auth** | Auth0 · AWS Cognito · Azure Entra ID |
| **Billing** | Stripe (metered billing) · Chargebee |
| **Observability** | Datadog · Grafana + Prometheus · AWS CloudWatch |
| **Audit Log** | Immutable append-only store — AWS CloudTrail · Azure Monitor · Kafka + S3 |

---

> **Open question before schema design**
> - **ESG frameworks in scope for v1** — GRI + TCFD only, or also SASB, CSRD, EU Taxonomy?  
>   This determines the set of `framework_tags`, the `REFERENCE_DATA` seed content, and which metadata filters are exposed in the retrieval API.
