# ESG Custodian — Data Architecture

> A document-first, multi-tenant **SaaS** platform for building ESG business cases.  
> Documents are uploaded into blob storage, distilled into focused context buckets per client, enriched incrementally with each new upload, and consumed directly by the business case engine — no vector search, no context dilution.

---

## Table of Contents

- [Data Layer Overview](#data-layer-overview)
- [Full Architecture](#full-architecture)
- [Context Layer — Distilled Knowledge](#context-layer--distilled-knowledge)
- [Context Extraction Pipeline](#context-extraction-pipeline)
- [Context Enrichment Flow](#context-enrichment-flow)
- [Reference Data Layer](#reference-data-layer)
- [Financial Modelling Tiers](#financial-modelling-tiers)
- [Core Data Model](#core-data-model)
- [Tenant Isolation Strategy](#tenant-isolation-strategy)
- [Architectural Decisions](#architectural-decisions)
- [Technology Stack](#technology-stack)

---

## Data Layer Overview

Six data layers from raw document to delivered business case. Each layer has a single responsibility and a clear data contract with the next.

```mermaid
flowchart TB
    subgraph L1["① INGESTION LAYER\nDocuments + Manual Inputs"]
        I_DOCS[/"Uploaded Documents\nPDFs · DOCX · XLSX · CSV"/]
        I_MAN[/"Manual Financial Inputs\nCAPEX · OPEX · Rates · Targets"/]
    end

    subgraph L2["② BLOB STORAGE LAYER\nRaw Source of Truth  ·  Tenant-Partitioned"]
        B_RAW[("Raw Documents\n/tenant_{id}/raw/{doc_id}")]
        B_REG[("Document Registry\ntenant_id · doc_id · type · status")]
    end

    subgraph L3["③ EXTRACTION PIPELINE\nClassify → Distil → Extract → Enrich"]
        P_CLF["Section Classifier\nMaps sections → context type\nDiscards irrelevant material"]
        P_DIST["Content Distiller\n200 pg → sharp ~10 pg per context type"]
        P_FIN["Financial Extractor\nTables + figures → typed rows\ndeterministic · not agentic"]
        P_ENR["Context Enricher\nMerges extract into existing context\nVersioned · Source-tracked"]
    end

    subgraph L4["④ CONTEXT LAYER\nDistilled Knowledge  ·  Per Tenant  ·  Versioned  ·  Cumulative"]
        C1[("Company Profile")]
        C2[("Business Overview")]
        C3[("Business Strategy")]
        C4[("IRO\nRisks · Impacts · Opportunities")]
        C5[("ESG Performance\nBaseline")]
        C6[("Materiality\nAssessment")]
        C7[("Operating Regions")]
        C8[("Regulatory\nObligations")]
        C9[("Financial Data\nExtracted + Keyed")]
    end

    subgraph L5["⑤ REFERENCE DATA LAYER\nPlatform-Managed  ·  Shared  ·  Read-Only"]
        R1[("ESG Framework Standards\nGRI · TCFD · SASB · CSRD\nEU Taxonomy · UN SDGs")]
        R2[("Industry Benchmarks\nSector ESG metrics · Peer scores")]
        R3[("Carbon & Energy\nPrice Curves")]
        R4[("Technology Cost Data\nSolar · EV Fleet · HVAC etc.")]
    end

    subgraph L6["⑥ CONSUMPTION LAYER\nBusiness Case Engine"]
        E_FETCH["Context Fetcher\nDirect pull by context type\nNo vector search · No dilution"]
        E_FIN["Financial Modelling Engine\nTier 1 · Tier 2 · Tier 3"]
        E_ESG["ESG Scoring Engine\nGap vs framework · Materiality\nPeer benchmarking"]
        E_LLM["LLM\nNarrative + structured outputs"]
    end

    subgraph L7["⑦ OUTPUT LAYER\nDelivered Business Case"]
        O_STORE[("Business Case Store\ncase_id · scenarios · outputs\ncontext_version_refs")]
        O_RPT[/"Structured Report\nExecutive Summary · Financial Model\nESG Impact · Framework Alignment"/]
        O_API[/"API Response\nJSON — consumed by\nclient app or dashboard"/]
        O_EXP[/"Export\nPDF · XLSX · PPTX"/]
    end

    I_DOCS --> B_RAW
    I_MAN --> C9
    B_RAW --> B_REG
    B_RAW --> P_CLF
    P_CLF --> P_DIST & P_FIN
    P_DIST --> P_ENR
    P_FIN --> C9
    P_ENR --> C1 & C2 & C3 & C4 & C5 & C6 & C7 & C8

    C1 & C2 & C3 & C4 & C5 & C6 & C7 & C8 --> E_FETCH
    C9 --> E_FIN
    R1 & R2 --> E_ESG
    R3 & R4 --> E_FIN
    R1 --> E_LLM

    E_FETCH --> E_LLM & E_ESG
    E_FIN --> O_STORE
    E_ESG --> O_STORE
    E_LLM --> O_STORE

    O_STORE --> O_RPT & O_API & O_EXP
```

---

## Full Architecture

The complete platform including the SaaS and operational layers.

```mermaid
flowchart TD
    subgraph ING["① Ingestion"]
        D1[/"ESG & Sustainability Reports"/]
        D2[/"Annual Reports · Regulatory Filings"/]
        D3[/"Financial Statements · Policy Docs"/]
        D4[/"Vendor & Supply Chain Docs"/]
        D5[/"Manual Financial Inputs\nCAPEX · OPEX · Rates · Targets"/]
    end

    subgraph BLOB["② Blob Store  ·  Tenant-Partitioned"]
        RAW[("Raw Documents\n/tenant_{id}/raw/{doc_id}")]
        DOCR[("Document Registry\ntenant_id · doc_id · context_types_found · status")]
    end

    subgraph PIPE["③ Extraction Pipeline"]
        CLF["Section Classifier"]
        DIST["Content Distiller"]
        FINEXT["Financial Extractor"]
        ENRICH["Context Enricher\nVersioned · Source-tracked"]
    end

    subgraph CTX["④ Context Layer  ·  Per Tenant  ·  Versioned"]
        CP[("Company Profile")]
        BO[("Business Overview")]
        BS[("Business Strategy")]
        IRO[("IRO")]
        ESGB[("ESG Performance\nBaseline")]
        MAT[("Materiality\nAssessment")]
        REG[("Operating Regions")]
        REOB[("Regulatory\nObligations")]
        FIN[("Financial Data\nExtracted + Keyed")]
    end

    subgraph REFD["⑤ Reference Data  ·  Platform-Managed  ·  Shared"]
        RF1[("Framework Standards\nGRI · TCFD · SASB\nCSRD · EU Taxonomy")]
        RF2[("Industry Benchmarks\n+ Peer Scores")]
        RF3[("Carbon & Energy\nPrice Curves")]
        RF4[("Technology\nCost Data")]
    end

    subgraph BCE["⑥ Consumption Layer — Business Case Engine"]
        FETCH["Context Fetcher\nDirect pull by context type"]
        FMOD["Financial Modelling Engine\nTier 1 · Tier 2 · Tier 3"]
        ESGSC["ESG Scoring Engine\nGap · Materiality · Benchmarks"]
        LLM["LLM\nNarrative + structured outputs"]
        BCASE[("Business Case Store\ncase_id · scenarios\ncontext_version_refs")]
    end

    subgraph OUT["⑦ Output Layer"]
        RPT[/"Structured Report"/]
        APIR[/"API Response"/]
        EXP[/"PDF · XLSX · PPTX Export"/]
    end

    subgraph SRV["⑧ Serving Layer"]
        GW["API Gateway  ·  JWT + tenant_id"]
        BCAPI["Business Case API"]
        DOCAPI["Document API"]
        DASHAPI["Analytics & Dashboard API"]
    end

    subgraph SAAS["⑨ SaaS Platform"]
        TMS["Tenant Management"]
        METER["Usage Metering + Billing"]
        AUDIT["Audit Log"]
    end

    D1 & D2 & D3 & D4 --> RAW
    D5 --> FIN
    RAW --> DOCR
    RAW --> CLF
    CLF --> DIST & FINEXT
    DIST --> ENRICH
    FINEXT --> FIN
    ENRICH --> CP & BO & BS & IRO & ESGB & MAT & REG & REOB

    CP & BO & BS & IRO & ESGB & MAT & REG & REOB --> FETCH
    FIN --> FMOD
    RF3 & RF4 --> FMOD
    RF1 & RF2 --> ESGSC
    RF1 --> LLM
    FETCH --> LLM & ESGSC
    FMOD & ESGSC & LLM --> BCASE
    BCASE --> RPT & APIR & EXP
    RPT & APIR & EXP --> GW
    GW --> BCAPI & DOCAPI & DASHAPI
    GW --> METER & AUDIT
    TMS --> GW
```

---

## Context Layer — Distilled Knowledge

Eight typed context buckets per tenant. Each bucket is the living, enriched knowledge the platform has built from all documents uploaded so far.

| Context Bucket | What it Contains | Primary Source Documents |
|---|---|---|
| **Company Profile** | Legal name, sector, size, ownership, subsidiaries, stock listing | Annual Report, Corporate Profile, About Us |
| **Business Overview** | Products/services, value chain, customers, geographies served, business model | Annual Report, Investor Presentation, Integrated Report |
| **Business Strategy** | Strategic priorities, growth agenda, transformation programmes, ESG commitments | Annual Report (strategy section), Board Report, Investor Day docs |
| **IRO** | Material climate risks (physical + transition), social risks, governance risks, opportunities | TCFD Report, Risk Register, Integrated Report |
| **ESG Performance Baseline** | Scope 1/2/3 emissions, energy/water/waste metrics, D&I data, existing targets, historical trend | Sustainability Report, ESG Disclosure, TCFD Report |
| **Materiality Assessment** | Material ESG topics, stakeholder input, double materiality (CSRD), prioritisation matrix | Materiality Report, Sustainability Report (materiality section) |
| **Operating Regions** | Countries/regions of operation, key sites, supply chain geographies, local regulatory contexts | Annual Report, Supply Chain Report, Operational Review |
| **Regulatory Obligations** | Applicable reporting frameworks, compliance deadlines, specific disclosure requirements, jurisdiction rules | Regulatory filings, Sustainability Report (framework section), Legal disclosures |
| **Financial Data** | CAPEX/OPEX baseline, revenue, cost structure, energy bills, existing ESG investment spend + manually keyed initiative inputs | Financial Statements, Utility Reports + User Input |

---

## Context Extraction Pipeline

```mermaid
flowchart LR
    subgraph DOC["Raw Document  (from Blob)"]
        RAW["200-page PDF · DOCX · XLSX"]
    end

    subgraph CLASSIFY["① Classify Sections"]
        CLF["Section Classifier\nReads structure + headings\nAssigns each section a context type\nor marks for discard"]
    end

    subgraph DISTIL["② Distil per Context Type"]
        CP_E["Company Profile extract"]
        BO_E["Business Overview extract"]
        BS_E["Business Strategy extract"]
        IRO_E["IRO extract"]
        ESGB_E["ESG Baseline extract\n(metrics + targets)"]
        MAT_E["Materiality extract"]
        REG_E["Operating Regions extract"]
        REOB_E["Regulatory Obligations extract"]
        FIN_E["Financial tables + figures"]
        DISC["⊘ Discarded\nBoilerplate · legal disclaimers\nRepetitive disclosures\nIrrelevant content"]
    end

    subgraph ENRICH["③ Enrich Existing Context"]
        E1["Merge → Company Profile"]
        E2["Merge → Business Overview"]
        E3["Merge → Business Strategy"]
        E4["Merge → IRO"]
        E5["Merge → ESG Baseline"]
        E6["Merge → Materiality"]
        E7["Merge → Operating Regions"]
        E8["Merge → Regulatory Obligations"]
        E9["Append → Financial Data\ntyped rows · source-tagged"]
    end

    RAW --> CLF
    CLF --> CP_E & BO_E & BS_E & IRO_E & ESGB_E & MAT_E & REG_E & REOB_E & FIN_E & DISC
    CP_E --> E1
    BO_E --> E2
    BS_E --> E3
    IRO_E --> E4
    ESGB_E --> E5
    MAT_E --> E6
    REG_E --> E7
    REOB_E --> E8
    FIN_E --> E9
```

---

## Context Enrichment Flow

Every upload enriches — not replaces — the existing context. Prior versions are archived so every business case output remains reproducible.

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
        PIPE->>CTX: Fetch current enriched content + version
        CTX-->>PIPE: e.g. ESG Performance Baseline v2
        PIPE->>PIPE: Merge new extract into existing\n(synthesise · deduplicate · fill gaps)
        PIPE->>VER: Archive current content as v2 snapshot
        PIPE->>CTX: Write enriched content as v3
        PIPE->>CTX: Append source_doc_id to provenance list
    end

    PIPE->>CTX: Append financial rows with source_doc_id
    PIPE->>DOC: Mark document status = processed

    Note over CTX,VER: Context grows richer with each upload.<br/>Every prior version preserved for audit &<br/>business case reproducibility.
```

---

## Reference Data Layer

Platform-managed, shared across all tenants, read-only from tenant context. This layer provides the external yardsticks that make ESG scoring and financial modelling meaningful.

| Reference Dataset | Contents | Used By | Update Cadence |
|---|---|---|---|
| **ESG Framework Standards** | GRI topic standards, TCFD recommendations, SASB industry standards, CSRD requirements, EU Taxonomy technical screening criteria, UN SDG mapping | ESG Scoring Engine, LLM (framework alignment prompts) | On framework revision |
| **Industry Benchmarks** | Sector-average emissions intensity, energy per unit revenue, D&I ratios, ESG scores by SIC/NACE code | ESG Scoring Engine (peer gap analysis) | Quarterly |
| **Carbon & Energy Price Curves** | Current + forward carbon price (ETS + voluntary), electricity/gas price curves by region | Financial Modelling Engine (NPV, carbon avoidance cost) | Monthly |
| **Technology Cost Data** | Benchmark CAPEX/OPEX for solar, EV fleet, LED, heat pumps, CCUS, etc. | Financial Modelling Engine (Tier 2/3 scenario defaults) | Quarterly |

---

## Financial Modelling Tiers

```mermaid
flowchart LR
    subgraph T1["Tier 1 — Deterministic  (always run)"]
        T1A["NPV · IRR"]
        T1B["Payback Period"]
        T1C["TCO · ROI"]
        T1D["Carbon Avoidance Cost\ntonne CO₂e avoided per £ spent"]
    end

    subgraph T2["Tier 2 — Scenario Analysis  (on request)"]
        T2A["Base · Bull · Bear Comparison"]
        T2B["Single-variable Sensitivity Sweep"]
        T2C["Break-even Analysis\ncarbon price · energy cost"]
        T2D["Tornado Chart Data\nranked variable impact"]
    end

    subgraph T3["Tier 3 — Stochastic  (on request)"]
        T3A["Monte Carlo Simulation\n10 000 iterations"]
        T3B["Outcome Probability Distribution\nNPV · IRR"]
        T3C["Value at Risk — VaR 95%"]
        T3D["Multi-period DCF\nstochastic rate path"]
    end

    T1 -->|"+ parameter ranges"| T2
    T2 -->|"+ probability distributions\n+ correlation matrix"| T3
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

    REFERENCE_DATA {
        uuid ref_id PK
        string dataset
        string framework
        string industry
        string metric
        decimal value
        string unit
        int year
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
| **Document Registry** | `tenant_id` on every row; every query appends `WHERE tenant_id = :tid` |
| **Context Store** | PostgreSQL Row-Level Security (RLS) on `tenant_id` |
| **Context Versions** | Same RLS; version history always scoped to tenant |
| **Financial Data** | Same RLS; `source_doc_id` validated against tenant before insert |
| **Business Case Store** | Same RLS; `context_versions_used` references only the tenant's own versions |
| **Reference Data** | Separate read-only schema and DB role; no tenant writes permitted |
| **API Gateway** | JWT `tenant_id` claim injected server-side; clients cannot supply or override it |

---

## Architectural Decisions

| Concern | Decision | Rationale |
|---|---|---|
| **No vector store / RAG** | Direct fetch by context type | Context buckets are predefined — retrieval is deterministic. Eliminates prompt dilution from loosely related chunks |
| **Distillation at ingest** | 200 pg → ~10 pg sharp extract per context type | Irrelevant material stripped before entering the context store — the LLM prompt stays tight |
| **Enrichment not append** | New extract synthesised into unified existing context | Prevents redundancy and contradiction across documents; one coherent view per bucket |
| **Version history** | Every context state archived before overwrite | Business case reproducibility — each case records which context version it used |
| **Reference Data as separate layer** | Platform-managed, shared, read-only | Benchmarks and framework standards are non-sensitive, cross-tenant, and have their own update cadence — they don't belong in tenant context |
| **Deterministic financial extraction** | Rule-based parser, not LLM agent | Financial figures feed arithmetic modelling — extraction must be reliable and auditable |
| **Manual financial input as peer of extracted** | Both write to same `financial_data` typed store | Handles absence of documents; downstream modelling engine treats both identically |
| **`context_versions_used` on business case** | Snapshot of version refs at generation time | Decouples case output from future enrichment; a case reflects knowledge at the time it was built |
| **Output Layer as explicit layer** | Report, API response, and export as distinct outputs | Business case outputs are consumed in three different ways — structured report, live API, and downloadable artefact |

---

## Technology Stack

| Layer | Options |
|---|---|
| **Blob Store** | AWS S3 · Azure Blob Storage |
| **Document Registry & Context Store** | PostgreSQL with Row-Level Security |
| **Financial Data Store** | PostgreSQL (same instance, separate schema) |
| **Section Classifier + Distiller** | LLM (Claude / GPT-4o) with structured output schema |
| **Financial Extractor** | Azure Document Intelligence · AWS Textract · `pdfplumber` |
| **Context Enricher** | LLM (Claude) — merge prompt with old context + new extract |
| **Reference Data Store** | PostgreSQL read-only schema · seeded from Bloomberg ESG, CDP, MSCI |
| **LLM — Business Case Generation** | Claude (Anthropic) · Azure OpenAI GPT-4o |
| **Financial Modelling Engine** | NumPy / SciPy (Python) — server-side, not LLM |
| **Report / Export Generation** | Puppeteer (PDF) · `openpyxl` (XLSX) · `python-pptx` (PPTX) |
| **API Gateway** | AWS API Gateway · Azure APIM · Kong |
| **Auth** | Auth0 · AWS Cognito · Azure Entra ID |
| **Billing** | Stripe (metered) · Chargebee |
| **Observability** | Datadog · Grafana + Prometheus |
| **Audit Log** | Append-only — AWS CloudTrail · Azure Monitor |

---

> **Open question**
> **ESG frameworks in scope for v1** — GRI + TCFD only, or also SASB, CSRD, EU Taxonomy?  
> This determines the Section Classifier's training targets, the Reference Data seed content, and the ESG Scoring Engine's gap-analysis logic.
