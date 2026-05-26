# ESG Custodian — Data Architecture

> A document-first, multi-tenant **SaaS** platform for building ESG business cases.  
> Documents are uploaded into blob storage, distilled into focused context buckets per client, and enriched incrementally with each new upload. The consumption layer pulls directly from typed context — no vector search, no context dilution.

---

## Table of Contents

- [Architecture Overview](#architecture-overview)
- [Context Extraction Pipeline](#context-extraction-pipeline)
- [Context Enrichment Flow](#context-enrichment-flow)
- [Financial Modelling Tiers](#financial-modelling-tiers)
- [Core Data Model](#core-data-model)
- [Tenant Isolation Strategy](#tenant-isolation-strategy)
- [Architectural Decisions](#architectural-decisions)
- [Technology Stack](#technology-stack)

---

## Architecture Overview

```mermaid
flowchart TD
    subgraph ING["① Ingestion"]
        D1[/"ESG & Sustainability Reports"/]
        D2[/"Annual Reports · Regulatory Filings"/]
        D3[/"Financial Statements · Policy Docs"/]
        D4[/"Vendor & Supply Chain Docs"/]
    end

    subgraph BLOB["② Blob Store  ·  Tenant-Partitioned"]
        RAW[("Raw Documents\n/tenant_{id}/raw/{doc_id}")]
        DOCR[("Document Registry\ntenant_id · doc_id · type\ncontext_types_found · upload_ts")]
    end

    subgraph PIPE["③ Context Extraction Pipeline"]
        CLF["Section Classifier\nMaps each section → context type\nor discards irrelevant material"]
        DIST["Content Distiller\n200 pg → sharp ~10 pg extract\nper context type"]
        FINEXT["Financial Extractor\nTables + figures → typed rows\ndeterministic parser · not agentic"]
        ENRICH["Context Enricher\nMerges new extract into existing context\nVersioned · Source-tracked per doc"]
    end

    subgraph CTX["④ Context Store  ·  Per Tenant  ·  Versioned  ·  Cumulative"]
        CP[("Company Profile")]
        BO[("Business Overview")]
        BS[("Business Strategy")]
        IRO[("IRO\nRisks · Impacts · Opportunities")]
        RRF[("Regions &\nReporting Frameworks")]
        FIN[("Financial Data\nExtracted + Manually Keyed")]
    end

    subgraph BCE["⑤ Consumption Layer — Business Case Engine"]
        FETCH["Context Fetcher\nDirect pull by context type\nNo vector search · No dilution"]
        FMOD["Financial Modelling Engine\nTier 1 · Tier 2 · Tier 3"]
        ESGSC["ESG Scoring\nGap vs framework · Materiality"]
        LLM["LLM\nNarrative + structured outputs"]
        BCASE[("Business Case Store\ncase_id · scenarios · outputs\ncontext_version_refs · narrative")]
    end

    subgraph SRV["⑥ Serving Layer"]
        GW["API Gateway  ·  JWT + tenant_id"]
        BCAPI["Business Case API"]
        DOCAPI["Document API"]
        DASHAPI["Analytics & Dashboard API"]
    end

    subgraph SAAS["⑦ SaaS Platform"]
        TMS["Tenant Management\nOnboarding · Plan · Settings"]
        METER["Usage Metering + Billing\nDocs processed · Cases run · Tiers used"]
        AUDIT["Audit Log\nImmutable per-tenant event stream"]
    end

    D1 & D2 & D3 & D4 --> RAW
    RAW --> DOCR
    RAW --> CLF
    CLF --> DIST & FINEXT
    DIST --> ENRICH
    FINEXT --> FIN
    ENRICH --> CP & BO & BS & IRO & RRF

    CP & BO & BS & IRO & RRF --> FETCH
    FIN --> FMOD
    FETCH --> LLM & ESGSC
    FMOD --> BCASE
    ESGSC --> BCASE
    LLM --> BCASE

    BCASE --> GW
    GW --> BCAPI & DOCAPI & DASHAPI
    GW --> METER & AUDIT
    TMS --> GW
```

---

## Context Extraction Pipeline

Each document upload triggers the same pipeline. Material irrelevant to any context type is discarded — this is the primary mechanism against context dilution.

```mermaid
flowchart LR
    subgraph DOC["Raw Document  (from Blob)"]
        RAW["200-page PDF · DOCX · XLSX"]
    end

    subgraph CLASSIFY["① Classify Sections"]
        CLF["Section Classifier\nReads doc structure + headings\nAssigns each section to a\ncontext type or marks discard"]
    end

    subgraph DISTIL["② Distil per Context Type"]
        CP_E["Company Profile\nextract"]
        BO_E["Business Overview\nextract"]
        BS_E["Business Strategy\nextract"]
        IRO_E["IRO extract\nRisks · Impacts · Opportunities"]
        RRF_E["Regions &\nFrameworks extract"]
        FIN_E["Financial tables\n+ figures"]
        DISC["⊘  Discarded\nBoilerplate · legal disclaimers\nRepetitive disclosures\nUnrelated content"]
    end

    subgraph ENRICH["③ Enrich Existing Context"]
        E1["Merge → Company Profile"]
        E2["Merge → Business Overview"]
        E3["Merge → Business Strategy"]
        E4["Merge → IRO"]
        E5["Merge → Regions & Frameworks"]
        E6["Append → Financial Data\n(typed rows · source-tagged)"]
    end

    RAW --> CLF
    CLF --> CP_E & BO_E & BS_E & IRO_E & RRF_E & FIN_E & DISC
    CP_E --> E1
    BO_E --> E2
    BS_E --> E3
    IRO_E --> E4
    RRF_E --> E5
    FIN_E --> E6
```

---

## Context Enrichment Flow

Context grows richer with every upload. Each version is preserved for audit and reproducibility — a business case always records which context version it was generated from.

```mermaid
sequenceDiagram
    participant DOC as New Document Upload
    participant PIPE as Extraction Pipeline
    participant CTX as Context Store
    participant VER as Version History

    DOC->>PIPE: Upload triggers extraction
    PIPE->>PIPE: Classify sections → context types
    PIPE->>PIPE: Distil relevant content per type
    PIPE->>PIPE: Extract financial rows (deterministic)

    loop For each context type found in document
        PIPE->>CTX: Fetch current context + version number
        CTX-->>PIPE: Existing enriched content (e.g. Company Profile v3)
        PIPE->>PIPE: Merge new extract into existing content\n(synthesise, deduplicate, fill gaps)
        PIPE->>VER: Archive current content as v3 snapshot
        PIPE->>CTX: Write enriched content as v4
        PIPE->>CTX: Append source_doc_id to provenance list
    end

    PIPE->>CTX: Append financial rows with source_doc_id tag
    PIPE->>DOC: Mark document status = processed
```

> **Context version reference on business cases**  
> Every generated business case records the exact version of each context bucket used (e.g. `company_profile_v4`, `iro_v2`). Re-running the same case months later — after more documents have been uploaded — will use newer context and produce a fresh output. The prior output is preserved with its version snapshot.

---

## Financial Modelling Tiers

Financial numbers flow from the Financial Data store. They may be extracted from documents or manually keyed by the user. The modelling engine treats both identically once in the store.

```mermaid
flowchart LR
    subgraph T1["Tier 1 — Deterministic  (always run)"]
        T1A["NPV · IRR"]
        T1B["Payback Period"]
        T1C["TCO · ROI"]
        T1D["Carbon Avoidance Cost\ntonne CO₂e avoided per £ spent"]
    end

    subgraph T2["Tier 2 — Scenario Analysis  (on request)"]
        T2A["Base · Bull · Bear\nScenario Comparison"]
        T2B["Single-variable Sensitivity Sweep"]
        T2C["Break-even Analysis\ncarbon price · energy cost"]
        T2D["Tornado Chart Data\nranked variable impact"]
    end

    subgraph T3["Tier 3 — Stochastic  (on request)"]
        T3A["Monte Carlo Simulation\n10 000 iterations"]
        T3B["Outcome Probability Distribution\nNPV · IRR"]
        T3C["Value at Risk — VaR 95%\non ESG investment"]
        T3D["Multi-period DCF\nwith stochastic rate path"]
    end

    T1 -->|"+ parameter ranges"| T2
    T2 -->|"+ probability distributions\n+ correlation matrix"| T3
```

### Financial Input Sources

| Input | Source |
|---|---|
| CAPEX · OPEX · Revenue impact | Extracted from financial statements **or** manually keyed |
| Carbon price · Energy cost | Extracted from reports, keyed, or pulled from Reference Data (market curves) |
| Discount rate · Horizon years | Manually keyed (business case–specific) |
| Sensitivity ranges (Tier 2) | Manually keyed per scenario |
| Probability distributions (Tier 3) | Manually defined or inferred from historical variance in extracted data |

---

## Core Data Model

```mermaid
erDiagram
    TENANT {
        uuid tenant_id PK
        string name
        string industry
        string plan
        timestamp created_at
    }

    DOCUMENT {
        uuid doc_id PK
        uuid tenant_id FK
        string type
        string[] context_types_found
        string processing_status
        timestamp upload_ts
    }

    TENANT_CONTEXT {
        uuid context_id PK
        uuid tenant_id FK
        string context_type
        text enriched_content
        int version
        uuid[] source_doc_ids
        timestamp updated_at
    }

    CONTEXT_VERSION {
        uuid version_id PK
        uuid context_id FK
        uuid tenant_id FK
        string context_type
        text content
        int version
        uuid triggering_doc_id FK
        timestamp created_at
    }

    FINANCIAL_DATA {
        uuid entry_id PK
        uuid tenant_id FK
        uuid source_doc_id FK
        string metric_name
        decimal value
        string unit
        string period
        string input_method
        timestamp created_at
    }

    BUSINESS_CASE {
        uuid case_id PK
        uuid tenant_id FK
        string project_name
        jsonb context_versions_used
        jsonb financial_outputs
        jsonb esg_scores
        text narrative
        int version
        timestamp created_at
    }

    SCENARIO {
        uuid scenario_id PK
        uuid case_id FK
        uuid tenant_id FK
        string name
        int tier
        jsonb parameter_overrides
        decimal npv
        decimal irr
        decimal payback_years
        jsonb sensitivity_results
        timestamp created_at
    }

    SIMULATION_RUN {
        uuid run_id PK
        uuid scenario_id FK
        int num_iterations
        jsonb parameter_distributions
        jsonb correlation_matrix
        decimal p10_npv
        decimal p50_npv
        decimal p90_npv
        decimal var_95_npv
        timestamp run_at
    }

    TENANT ||--o{ DOCUMENT : "uploads"
    TENANT ||--o{ TENANT_CONTEXT : "has"
    TENANT ||--o{ FINANCIAL_DATA : "has"
    TENANT ||--o{ BUSINESS_CASE : "generates"
    TENANT_CONTEXT ||--o{ CONTEXT_VERSION : "versioned as"
    DOCUMENT ||--o{ FINANCIAL_DATA : "source for"
    DOCUMENT }o--o{ CONTEXT_VERSION : "triggers"
    BUSINESS_CASE ||--o{ SCENARIO : "has"
    SCENARIO ||--o| SIMULATION_RUN : "has (Tier 3 only)"
```

---

## Tenant Isolation Strategy

| Layer | Isolation Mechanism |
|---|---|
| **Blob Store** | Key prefix `/tenant_{id}/` — IAM policy enforces prefix-scoped access |
| **Document Registry** | `tenant_id` column; every query appends `WHERE tenant_id = :tid` |
| **Context Store** | Row-Level Security (RLS) on `tenant_id`; service account has no `BYPASSRLS` |
| **Context Versions** | Same RLS; version history is always scoped to tenant |
| **Financial Data** | Same RLS; `source_doc_id` validated against tenant before insert |
| **Business Case Store** | Same RLS; `context_versions_used` references only tenant's own versions |
| **API Gateway** | JWT `tenant_id` claim injected server-side; clients cannot supply or override it |

---

## Architectural Decisions

| Concern | Decision | Rationale |
|---|---|---|
| **No vector store / RAG** | Direct fetch by context type | Context types are predefined — retrieval is deterministic, not probabilistic. Eliminates dilution from loosely related chunks |
| **Distillation at ingest** | 200pg → ~10pg sharp extract per context type | Irrelevant material stripped before it ever enters the context store, keeping LLM prompts tight |
| **Enrichment not append** | New extract merged into existing unified context | Avoids redundancy and contradiction between extracts; produces a single coherent view per context type |
| **Version history** | Every context state archived before overwrite | Business case reproducibility — each case records which context version it used; prior outputs stay valid |
| **Deterministic financial extraction** | Rule-based table parser, not LLM agent | Financial figures feed into arithmetic modelling — extraction must be reliable and auditable |
| **Manual financial keying as fallback** | Accepted alongside extracted data | Handles cases where no document exists; both sources write to the same typed store |
| **Progressive modelling tiers** | Tier 1 always runs; Tier 2 and 3 on request | Keeps simple cases fast and cheap; stochastic compute only when needed |
| **`context_versions_used` on business case** | Snapshot of version refs at generation time | Decouples business case output from future context enrichment; a case reflects the knowledge at the time it was built |

---

## Technology Stack

| Layer | Options |
|---|---|
| **Blob Store** | AWS S3 · Azure Blob Storage |
| **Document Registry & Context Store** | PostgreSQL with Row-Level Security |
| **Financial Data Store** | PostgreSQL (same instance, separate schema) |
| **Section Classifier + Distiller** | LLM (Claude / GPT-4o) with structured output schema |
| **Financial Extractor** | Azure Document Intelligence · AWS Textract · `pdfplumber` (open source) |
| **Context Enricher** | LLM (Claude) — merge prompt with old context + new extract |
| **LLM (Business Case generation)** | Claude (Anthropic) · Azure OpenAI GPT-4o |
| **Financial Modelling Engine** | NumPy / SciPy (Python) — server-side, not LLM |
| **API Gateway** | AWS API Gateway · Azure APIM · Kong |
| **Auth** | Auth0 · AWS Cognito · Azure Entra ID |
| **Billing** | Stripe (metered) · Chargebee |
| **Observability** | Datadog · Grafana + Prometheus |
| **Audit Log** | Append-only store — AWS CloudTrail · Azure Monitor |

---

> **Open question**  
> **ESG frameworks in scope for v1** — GRI + TCFD only, or also SASB, CSRD, EU Taxonomy?  
> This determines the `context_types_found` vocabulary, the Section Classifier's training targets, and the ESG Scoring Engine's gap-analysis logic.
