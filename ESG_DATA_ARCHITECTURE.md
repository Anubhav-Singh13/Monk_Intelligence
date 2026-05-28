# ESG Custodian — Data Architecture

> A document-first, multi-tenant **SaaS** platform for building ESG business cases.  
> Documents are uploaded into blob storage, distilled into focused context buckets per client, enriched incrementally with each new upload, and consumed directly by the business case engine — no vector search, no context dilution.  
> A Metadata Layer governs every stage of the pipeline — from extraction rules to ESG framework mappings to validation logic — making the platform configurable without code changes.

---

## Table of Contents

- [Data Layer Overview](#data-layer-overview)
- [Metadata Layer](#metadata-layer)
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

Seven data layers from raw document to delivered business case, governed at every stage by the Metadata Layer.

```mermaid
flowchart TB
    subgraph META["✦ METADATA LAYER  ·  Platform-Managed  ·  Governs all layers"]
        M1["Context Schema\nExtraction rules per bucket"]
        M2["Framework Mapping\nWhich ESG norms apply where"]
        M3["Document Type Mapping\nDoc type → expected context buckets"]
        M4["Metric Definitions\nStandard defs · units · formulas"]
        M5["Industry Materiality\nMaterial topics by sector"]
        M6["Validation Rules\nPlausibility checks per metric"]
        M7["Tenant Config\nFrameworks · sector · residency"]
        M8["Case Templates\nInputs · scoring · structure per initiative type"]
    end

    subgraph L1["① INGESTION LAYER"]
        I1[/"Uploaded Documents\nPDFs · DOCX · XLSX · CSV"/]
        I2[/"Manual Financial Inputs\nCAPEX · OPEX · Rates · Targets"/]
    end

    subgraph L2["② BLOB STORAGE LAYER  ·  Tenant-Partitioned  ·  Source of Truth"]
        B1[("Raw Documents\n/tenant_{id}/raw/{doc_id}")]
        B2[("Document Registry\ntenant_id · doc_id · type · status")]
    end

    subgraph L3["③ EXTRACTION PIPELINE  ·  Classify → Distil → Validate → Enrich"]
        P1["Section Classifier\n← governed by Context Schema\n+ Document Type Mapping"]
        P2["Content Distiller\n← governed by Context Schema\n(keep / discard rules)"]
        P3["Financial Extractor\nDeterministic · not agentic"]
        P4["Data Quality Gate\n← governed by Validation Rules\n+ Metric Definitions\nConfidence score · flag low-quality"]
        P5["Context Enricher\nMerge into existing context\nVersioned · Serialised per bucket"]
    end

    subgraph L4["④ CONTEXT LAYER  ·  Distilled Knowledge  ·  Per Tenant  ·  Versioned"]
        C1[("Company Profile")]
        C2[("Business Overview")]
        C3[("Business Strategy")]
        C4[("IRO")]
        C5[("ESG Performance\nBaseline\n(time-series rows)")]
        C6[("Materiality\nAssessment")]
        C7[("Operating Regions")]
        C8[("Regulatory\nObligations")]
        C9[("Social Due\nDiligence")]
        C10[("Financial Data\nExtracted + Keyed")]
    end

    subgraph L5["⑤ REFERENCE DATA LAYER  ·  Platform-Managed  ·  Versioned  ·  Read-Only"]
        R1[("Framework Standards\nGRI · TCFD · SASB\nCSRD · EU Taxonomy\nversioned · immutable rows")]
        R2[("Industry Benchmarks\n+ Peer Scores")]
        R3[("Carbon & Energy\nPrice Curves")]
        R4[("Technology\nCost Data")]
    end

    subgraph L6["⑥ CONSUMPTION LAYER  ·  Business Case Engine"]
        E1["Context Fetcher\nDirect pull by context type\n← Case Templates govern\nwhich buckets are fetched"]
        E2["Derived Metrics Engine\n← Metric Definitions govern\ncalculation formulas"]
        E3["Financial Modelling Engine\nTier 1 · Tier 2 · Tier 3"]
        E4["ESG Scoring Engine\n← Framework Mapping +\nIndustry Materiality govern scoring"]
        E5["LLM\nNarrative + citation-grounded\noutputs"]
    end

    subgraph L7["⑦ OUTPUT LAYER"]
        O1[("Business Case Store\ncase_id · scenarios\ncontext_version_refs\nreference_data_snapshot")]
        O2[/"Structured Report\nExecutive Summary · Financial Model\nESG Impact · Framework Alignment"/]
        O3[/"API Response  (JSON)"/]
        O4[/"Export  PDF · XLSX · PPTX"/]
    end

    I1 --> B1
    I2 --> C10
    B1 --> B2
    B1 --> P1
    P1 --> P2 & P3
    P2 --> P4
    P3 --> P4
    P4 -->|"passes quality check"| P5
    P4 -->|"fails quality check"| B2
    P5 --> C1 & C2 & C3 & C4 & C5 & C6 & C7 & C8 & C9
    P3 --> C10

    C1 & C2 & C3 & C4 & C5 & C6 & C7 & C8 & C9 --> E1
    C10 --> E3
    C5 --> E2
    E2 --> E4
    R1 & R2 --> E4
    R3 & R4 --> E3
    R1 --> E5
    E1 --> E5 & E4
    E2 & E3 & E4 & E5 --> O1
    O1 --> O2 & O3 & O4

    META -.->|extraction rules| P1
    META -.->|keep/discard rules| P2
    META -.->|validation rules| P4
    META -.->|framework tags| P5
    META -.->|scoring weights| E4
    META -.->|metric formulas| E2
    META -.->|case structure| E1
    META -.->|tenant settings| L6
```

---

## Metadata Layer

The Metadata Layer is the **intelligence and configuration brain** of the platform. It makes every layer governable without code changes — when ESG frameworks update, you update metadata, not software.

### Metadata Types

```mermaid
flowchart LR
    subgraph PMETA["Platform-Managed Metadata\n(ESG product team — updated on framework revision)"]
        CS["Context Schema\nExtraction rules per context bucket\nIncludes: what to keep\nExcludes: what to discard"]
        FM["Framework Mapping\nWhich frameworks apply to which buckets\nDisclosure requirements per framework\nVersioned · immutable on update"]
        DTM["Document Type Mapping\nDoc type → expected context buckets\nClassifier prior — boosts accuracy"]
        MD["Metric Definitions\nStandard name · definition · units\nCalculation methodology (GHG Protocol etc.)\nDerivation formulas for calculated metrics"]
        IM["Industry Materiality\nMaterial ESG topics by sector (SASB-aligned)\nMateriality weight: High · Medium · Low\nRecommended frameworks per sector"]
        VR["Validation Rules\nPlausibility checks per metric per sector\nRange checks · unit consistency\nCross-field logic · required field checks"]
        CT["Case Templates\nInitiative type → required context buckets\nRequired financial inputs\nScoring dimensions · narrative sections"]
    end

    subgraph TMETA["Tenant-Configured Metadata\n(set at onboarding · editable in settings)"]
        TC["Tenant Configuration\nIndustry / sector classification\nESG frameworks in scope\nReporting year + fiscal convention\nBaseline year · Net zero target year\nReporting currency\nData residency region\nPreferred carbon price scenario"]
    end
```

### What Each Metadata Type Governs

| Metadata | Governs | Without it |
|---|---|---|
| **Context Schema** | Section Classifier (what to extract), Content Distiller (what to keep/discard) | Classifier guesses; irrelevant content enters context store |
| **Framework Mapping** | Context tagging during enrichment, ESG Scoring Engine gap analysis | Scoring is generic; framework alignment is unmeasurable |
| **Document Type Mapping** | Classifier prior — expected buckets per doc type | Every doc treated identically; accuracy drops on standard doc types |
| **Metric Definitions** | Derived Metrics Engine formulas, Data Quality Gate unit checks | Scope 2 location-based vs market-based are indistinguishable; benchmarks can't be compared |
| **Industry Materiality** | ESG Scoring Engine weighting, extraction prioritisation per sector | A bank and a mining company scored identically; materiality is invisible |
| **Validation Rules** | Data Quality Gate — catches bad extractions before context store write | Mis-OCR'd numbers enter context silently; financial models are corrupted |
| **Case Templates** | Context Fetcher (which buckets to pull), LLM prompt structure, scoring dimensions | All business cases structured identically regardless of initiative type |
| **Tenant Configuration** | Blob routing (data residency), LLM endpoint selection, benchmark selection, carbon price default | EU tenant data routes to US; wrong industry benchmarks applied; wrong frameworks scored |

---

## Full Architecture

```mermaid
flowchart TD
    subgraph ING["① Ingestion"]
        D1[/"ESG & Sustainability Reports"/]
        D2[/"Annual Reports · Regulatory Filings"/]
        D3[/"Financial Statements · Policy Docs"/]
        D4[/"Modern Slavery Statements · Supply Chain Reports"/]
        D5[/"Manual Financial Inputs  CAPEX · OPEX · Rates"/]
    end

    subgraph BLOB["② Blob Store  ·  Tenant-Partitioned"]
        RAW[("Raw Documents\n/tenant_{id}/raw/{doc_id}")]
        DOCR[("Document Registry\ntenant_id · doc_id · type\ncontext_types_found · status")]
    end

    subgraph PIPE["③ Extraction Pipeline"]
        CLF["Section Classifier\n← Context Schema + Doc Type Mapping"]
        DIST["Content Distiller\n← Context Schema (keep/discard rules)"]
        FINEXT["Financial Extractor\nDeterministic parser"]
        DQG["Data Quality Gate\n← Validation Rules + Metric Definitions\nConfidence score · flag · block"]
        ENRICH["Context Enricher\nVersioned · Serialised per bucket\nConcurrency-safe"]
    end

    subgraph MLAYER["✦ Metadata Layer"]
        CSCHEMA["Context Schema"]
        FMAP["Framework Mapping\n(versioned · immutable)"]
        DTMAP["Doc Type Mapping"]
        MDEF["Metric Definitions"]
        IMAT["Industry Materiality"]
        VRULES["Validation Rules"]
        TCONF["Tenant Config"]
        CTMPL["Case Templates"]
    end

    subgraph CTX["④ Context Layer  ·  Per Tenant  ·  Versioned  ·  Reviewed"]
        CP[("Company Profile")]
        BO[("Business Overview")]
        BS[("Business Strategy")]
        IRO[("IRO")]
        ESGB[("ESG Performance\nBaseline\ntime-series rows")]
        MAT[("Materiality\nAssessment")]
        OREG[("Operating Regions")]
        ROBLIG[("Regulatory\nObligations")]
        SDD[("Social Due\nDiligence")]
        FIN[("Financial Data\nExtracted + Keyed")]
    end

    subgraph REFD["⑤ Reference Data  ·  Platform  ·  Versioned"]
        RF1[("Framework Standards\nversioned · immutable")]
        RF2[("Industry Benchmarks")]
        RF3[("Carbon & Energy Curves")]
        RF4[("Technology Cost Data")]
    end

    subgraph BCE["⑥ Consumption Layer"]
        FETCH["Context Fetcher\n← Case Templates"]
        DERIVE["Derived Metrics Engine\n← Metric Definitions"]
        FMOD["Financial Modelling\nTier 1 · 2 · 3"]
        ESGSC["ESG Scoring Engine\n← Framework Mapping\n+ Industry Materiality"]
        LLM["LLM  (citation-grounded)\n← Framework Mapping"]
        BCASE[("Business Case Store\ncontext_version_refs\nreference_data_snapshot")]
    end

    subgraph OUT["⑦ Output Layer"]
        RPT[/"Structured Report"/]
        APIR[/"API Response"/]
        EXP[/"PDF · XLSX · PPTX"/]
    end

    subgraph SRV["⑧ Serving Layer"]
        GW["API Gateway\nJWT · tenant_id · user_id · role"]
        BCAPI["Business Case API"]
        DOCAPI["Document API"]
        DASHAPI["Analytics API"]
    end

    subgraph SAAS["⑨ SaaS Platform"]
        TMS["Tenant + User Management\nRBAC · Approval Workflows"]
        METER["Usage Metering + Billing"]
        AUDIT["Audit Log"]
    end

    D1 & D2 & D3 & D4 --> RAW
    D5 --> FIN
    RAW --> DOCR
    RAW --> CLF
    CLF --> DIST & FINEXT
    DIST --> DQG
    FINEXT --> DQG
    DQG -->|"passes"| ENRICH
    DQG -->|"fails — flagged for review"| DOCR
    ENRICH --> CP & BO & BS & IRO & ESGB & MAT & OREG & ROBLIG & SDD

    CSCHEMA & DTMAP -.-> CLF
    CSCHEMA -.-> DIST
    VRULES & MDEF -.-> DQG
    FMAP -.-> ENRICH
    TCONF -.-> GW
    CTMPL -.-> FETCH
    FMAP & IMAT -.-> ESGSC
    MDEF -.-> DERIVE
    CTMPL -.-> LLM

    CP & BO & BS & IRO & ESGB & MAT & OREG & ROBLIG & SDD --> FETCH
    FIN --> FMOD
    ESGB --> DERIVE
    DERIVE --> ESGSC
    RF1 & RF2 --> ESGSC
    RF3 & RF4 --> FMOD
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

Nine typed context buckets per tenant. Each bucket is the living, enriched, reviewed knowledge the platform has built from all uploaded documents.

| # | Context Bucket | What it Contains | Primary Source Documents | Review Required |
|---|---|---|---|---|
| 1 | **Company Profile** | Legal name, sector, size, ownership, subsidiaries, stock listing | Annual Report, Corporate Profile | Optional |
| 2 | **Business Overview** | Products/services, value chain, customers, geographies, business model | Annual Report, Investor Presentation | Optional |
| 3 | **Business Strategy** | Strategic priorities, growth agenda, transformation, ESG commitments | Annual Report (strategy), Board Report | Optional |
| 4 | **IRO** | Climate risks (physical + transition), social risks, governance risks, opportunities, likelihood/impact ratings | TCFD Report, Risk Register | Recommended |
| 5 | **ESG Performance Baseline** | Scope 1/2/3 by year, energy/water/waste metrics, D&I data, targets, baselines — stored as **time-series rows**, not text | Sustainability Report, ESG Disclosure | **Required** |
| 6 | **Materiality Assessment** | Material ESG topics, stakeholder input, double materiality (CSRD), prioritisation matrix | Materiality Report, Sustainability Report | **Required** |
| 7 | **Operating Regions** | Countries/regions of operation, key sites, supply chain geographies, high-risk territories | Annual Report, Supply Chain Report | Optional |
| 8 | **Regulatory Obligations** | Applicable frameworks, compliance deadlines, specific disclosure requirements, jurisdiction rules | Regulatory filings, Sustainability Report | **Required** |
| 9 | **Social Due Diligence** | Modern Slavery Act statements, supplier audit coverage/findings/remediation, human rights policy, CSDDD/UNGPs alignment, grievance mechanisms | MSA Statement, Supply Chain Audit Reports, Human Rights Policy | **Required** |
| — | **Financial Data** | CAPEX/OPEX baseline, energy bills, ESG investment spend + manually keyed initiative inputs | Financial Statements + User Input | **Required** |

---

## Context Extraction Pipeline

```mermaid
flowchart LR
    subgraph DOC["Raw Document  (from Blob)"]
        RAW["200-page PDF · DOCX · XLSX"]
    end

    subgraph CLASSIFY["① Classify\n← Context Schema\n+ Doc Type Mapping"]
        CLF["Section Classifier\nPrior from doc type\nAssigns section → context type\nor marks discard"]
    end

    subgraph DISTIL["② Distil\n← Context Schema keep/discard rules"]
        CP_E["Company Profile extract"]
        BO_E["Business Overview extract"]
        BS_E["Business Strategy extract"]
        IRO_E["IRO extract"]
        ESGB_E["ESG Baseline extract\ntime-tagged metric rows"]
        MAT_E["Materiality extract"]
        ORE_E["Operating Regions extract"]
        ROB_E["Regulatory Obligations extract"]
        SDD_E["Social Due Diligence extract"]
        FIN_E["Financial tables + figures"]
        DISC["⊘ Discarded\nBoilerplate · disclaimers\nIrrelevant content"]
    end

    subgraph QUALITY["③ Data Quality Gate\n← Validation Rules + Metric Definitions"]
        DQG["Confidence score per extraction\nRange checks · unit validation\nCross-field consistency\nFlag low-confidence for review\nBlock invalid values"]
    end

    subgraph ENRICH["④ Enrich Existing Context\nSerialized per bucket — concurrency-safe"]
        E1["Merge → Company Profile"]
        E2["Merge → Business Overview"]
        E3["Merge → Business Strategy"]
        E4["Merge → IRO"]
        E5["Append rows → ESG Baseline\n(by metric + period)"]
        E6["Merge → Materiality"]
        E7["Merge → Operating Regions"]
        E8["Merge → Regulatory Obligations"]
        E9["Merge → Social Due Diligence"]
        E10["Append → Financial Data"]
    end

    RAW --> CLF
    CLF --> CP_E & BO_E & BS_E & IRO_E & ESGB_E & MAT_E & ORE_E & ROB_E & SDD_E & FIN_E & DISC
    CP_E & BO_E & BS_E & IRO_E & ESGB_E & MAT_E & ORE_E & ROB_E & SDD_E & FIN_E --> DQG
    DQG -->|"confidence ≥ threshold"| E1 & E2 & E3 & E4 & E5 & E6 & E7 & E8 & E9 & E10
    DQG -->|"confidence < threshold"| DISC
```

---

## Context Enrichment Flow

Enrichment is serialised per `(tenant_id, context_type)` to prevent race conditions. Every prior version is archived. Context is only promoted to `active` after passing the review gate (where required).

```mermaid
sequenceDiagram
    participant DOC as New Document Upload
    participant QUEUE as Enrichment Queue
    participant PIPE as Extraction Pipeline
    participant DQG as Data Quality Gate
    participant CTX as Context Store
    participant VER as Version History
    participant REV as Review Queue

    DOC->>PIPE: Upload triggers extraction
    PIPE->>PIPE: Classify + Distil + Extract

    PIPE->>DQG: Submit extracts with confidence scores
    DQG-->>PIPE: Pass / Flag / Block per extract

    loop For each context type — serialised via queue
        PIPE->>QUEUE: Enqueue enrichment job (tenant_id, context_type)
        QUEUE->>CTX: Acquire lock on (tenant_id, context_type)
        CTX-->>QUEUE: Lock acquired
        QUEUE->>CTX: Fetch current enriched content + version
        CTX-->>QUEUE: e.g. IRO v3
        QUEUE->>QUEUE: Merge new extract into existing content
        QUEUE->>VER: Archive IRO v3 as snapshot
        QUEUE->>CTX: Write enriched content as IRO v4 (status = pending_review OR active)
        QUEUE->>CTX: Append source_doc_id to provenance
        QUEUE->>CTX: Release lock
    end

    alt Context type requires review
        CTX->>REV: Notify reviewer (tenant user with context_reviewer role)
        REV-->>CTX: Approved → status = active
        REV-->>CTX: Rejected → rollback to v3
    end

    Note over CTX,VER: Business cases always consume<br/>last active (approved) version only.
```

---

## Reference Data Layer

Platform-managed, shared across all tenants, read-only from tenant context. Rows are **immutable** — updates insert new versioned rows with `effective_from` / `effective_to`, so business cases can be reproduced against the framework version that existed when they were generated.

| Reference Dataset | Contents | Used By | Update Cadence |
|---|---|---|---|
| **Framework Standards** | GRI topic standards, TCFD, SASB industry standards, CSRD/ESRS, EU Taxonomy screening criteria, UN SDG mappings — versioned, immutable | ESG Scoring Engine, LLM prompt builder | On framework revision — new rows inserted, old rows closed |
| **Industry Benchmarks** | Sector-average emissions intensity, energy per unit revenue, D&I ratios, ESG scores by GICS/NACE | ESG Scoring Engine (peer gap) | Quarterly |
| **Carbon & Energy Price Curves** | Current + forward carbon price (ETS + voluntary), electricity/gas price by region | Financial Modelling Engine | Monthly |
| **Technology Cost Data** | Benchmark CAPEX/OPEX for solar, EV fleet, LED, heat pumps, CCUS | Financial Modelling Engine (Tier 2/3 defaults) | Quarterly |

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
        T2D["Tornado Chart  ranked variable impact"]
    end

    subgraph T3["Tier 3 — Stochastic  (on request)"]
        T3A["Monte Carlo  10 000 iterations"]
        T3B["Outcome Probability Distribution\nNPV · IRR"]
        T3C["Value at Risk — VaR 95%"]
        T3D["Multi-period DCF  stochastic rate path"]
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
        string sector_code
        string[] frameworks_in_scope
        int baseline_year
        int net_zero_target_year
        string reporting_currency
        string data_residency_region
        string plan
        timestamp created_at
    }

    USER {
        uuid user_id PK
        uuid tenant_id FK
        string email
        string role
        timestamp created_at
    }

    DOCUMENT {
        uuid doc_id PK
        uuid tenant_id FK
        string type
        string[] context_types_found
        string processing_status
        timestamp upload_ts
        uuid uploaded_by FK
    }

    CONTEXT_SCHEMA {
        uuid schema_id PK
        string context_type
        jsonb include_rules
        jsonb exclude_rules
        int version
        date effective_from
        date effective_to
    }

    FRAMEWORK_MAPPING {
        uuid mapping_id PK
        string framework
        string context_type
        jsonb disclosure_requirements
        int version
        date effective_from
        date effective_to
    }

    METRIC_DEFINITION {
        uuid metric_id PK
        string metric_name
        string standard_definition
        string[] accepted_units
        string calculation_methodology
        string[] framework_tags
        string derivation_formula
    }

    VALIDATION_RULE {
        uuid rule_id PK
        string metric_name
        string sector_code
        string rule_type
        jsonb rule_definition
        string severity
    }

    CASE_TEMPLATE {
        uuid template_id PK
        string initiative_type
        string[] required_context_types
        string[] required_financial_inputs
        string[] scoring_dimensions
        jsonb narrative_sections
        int version
    }

    TENANT_CONTEXT {
        uuid context_id PK
        uuid tenant_id FK
        string context_type
        text enriched_content
        string review_status
        uuid reviewed_by FK
        timestamp reviewed_at
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

    ESG_METRIC_TIMESERIES {
        uuid metric_id PK
        uuid tenant_id FK
        string metric_name
        string period
        decimal value
        string unit
        string reporting_boundary
        uuid source_doc_id FK
        decimal extraction_confidence
        boolean reviewed
        timestamp created_at
    }

    DERIVED_ESG_METRIC {
        uuid derived_id PK
        uuid tenant_id FK
        string metric_name
        string numerator_metric
        string denominator_metric
        string period
        decimal value
        string unit
        string formula_version
        timestamp computed_at
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
        date effective_from
        date effective_to
    }

    EXTRACTION_QUALITY_LOG {
        uuid log_id PK
        uuid tenant_id FK
        uuid doc_id FK
        string context_type
        decimal confidence_score
        jsonb flagged_issues
        string review_status
        uuid reviewed_by FK
        timestamp created_at
    }

    BUSINESS_CASE {
        uuid case_id PK
        uuid tenant_id FK
        uuid template_id FK
        uuid created_by FK
        uuid approved_by FK
        string project_name
        jsonb context_versions_used
        jsonb reference_data_snapshot
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

    TENANT ||--o{ USER : "has"
    TENANT ||--o{ DOCUMENT : "uploads"
    TENANT ||--o{ TENANT_CONTEXT : "has"
    TENANT ||--o{ ESG_METRIC_TIMESERIES : "has"
    TENANT ||--o{ DERIVED_ESG_METRIC : "has"
    TENANT ||--o{ FINANCIAL_DATA : "has"
    TENANT ||--o{ BUSINESS_CASE : "generates"
    USER ||--o{ DOCUMENT : "uploads"
    USER ||--o{ BUSINESS_CASE : "creates"
    TENANT_CONTEXT ||--o{ CONTEXT_VERSION : "versioned as"
    DOCUMENT ||--o{ ESG_METRIC_TIMESERIES : "source for"
    DOCUMENT }o--o{ CONTEXT_VERSION : "triggers"
    DOCUMENT ||--o{ EXTRACTION_QUALITY_LOG : "has"
    BUSINESS_CASE ||--o{ SCENARIO : "has"
    BUSINESS_CASE }o--|| CASE_TEMPLATE : "uses"
    SCENARIO ||--o| SIMULATION_RUN : "has (Tier 3)"
    CONTEXT_SCHEMA ||--o{ TENANT_CONTEXT : "governs"
    METRIC_DEFINITION ||--o{ ESG_METRIC_TIMESERIES : "defines"
    METRIC_DEFINITION ||--o{ DERIVED_ESG_METRIC : "defines"
    VALIDATION_RULE ||--o{ EXTRACTION_QUALITY_LOG : "produces"
```

---

## Tenant Isolation Strategy

| Layer | Isolation Mechanism |
|---|---|
| **Blob Store** | Key prefix `/tenant_{id}/` — IAM policy enforces prefix-scoped access. Routed to region-specific bucket per `data_residency_region` |
| **Document Registry** | `tenant_id` on every row; every query appends `WHERE tenant_id = :tid` |
| **Context Store** | PostgreSQL Row-Level Security (RLS) on `tenant_id`; service account has no `BYPASSRLS` |
| **ESG Metrics Time-series** | Same RLS; partitioned by `tenant_id` for query performance |
| **Financial Data** | Same RLS; `source_doc_id` validated against tenant before insert |
| **Business Case Store** | Same RLS; `context_versions_used` and `reference_data_snapshot` reference only tenant's own versions |
| **Metadata Layer** | Platform-managed schemas are read-only to all tenant service accounts; Tenant Config rows are scoped to `tenant_id` |
| **Reference Data** | Separate read-only DB role; no tenant writes; shared safely because it contains no tenant data |
| **LLM API Routing** | `data_residency_region` on Tenant Config routes inference calls to region-appropriate LLM endpoints |
| **API Gateway** | JWT `tenant_id` + `user_id` + `role` claims injected server-side; action-level RBAC enforced per role |

---

## Architectural Decisions

| Concern | Decision | Rationale |
|---|---|---|
| **No vector store / RAG** | Direct fetch by context type | Predefined buckets make retrieval deterministic — no dilution from loosely related chunks |
| **Metadata Layer as config brain** | Platform Config DB, governs every pipeline stage | Framework changes, new sectors, new ESG norms = metadata update, not a code release |
| **ESG baseline as time-series rows** | `ESG_METRIC_TIMESERIES` with `period` field | Trend analysis, trajectory-to-target, and year-on-year comparison are impossible on a flat blob |
| **Serialised enrichment per bucket** | Queue + lock per `(tenant_id, context_type)` | Concurrent uploads without locking cause silent data loss via lost-update race condition |
| **Data Quality Gate** | Confidence scoring + validation rules before context store write | Bad extractions must be blocked or flagged before they corrupt context; silent bad data is the highest-risk failure mode |
| **Derived Metrics Engine** | Separate deterministic calculation layer using Metric Definitions | Emission intensity, water intensity etc. are *calculated*, not extracted — they need a defined, auditable formula path |
| **Reference data immutable + versioned** | `effective_from / effective_to` — updates insert new rows | Business cases can be reproduced against the framework version that existed at generation time |
| **`reference_data_snapshot` on business case** | Snapshot of reference versions used at generation | Decouples case output from future reference data updates alongside context version refs |
| **RBAC within tenant** | `USER` + `role` + approval states on context and cases | ESG business cases are cross-functional; CFO, sustainability, procurement, legal need different permissions |
| **Review gates on context** | Required for high-stakes buckets (ESG Baseline, Materiality, IRO, Regulatory Obligations, Social Due Diligence) | Extraction errors in high-stakes buckets corrupt business cases and regulatory outputs silently |
| **Data residency routing** | `data_residency_region` on Tenant drives blob region + LLM endpoint | EU enterprise clients under CSRD require data to remain in EU; LLM inference of tenant data must not transit to non-compliant regions |

---

## Technology Stack

| Layer | Options |
|---|---|
| **Blob Store** | AWS S3 · Azure Blob Storage — region-pinned per tenant |
| **Document Registry + Context Store + Metadata** | PostgreSQL with Row-Level Security + partitioning |
| **ESG Metrics Time-series** | PostgreSQL (TimescaleDB extension) · InfluxDB for high-volume |
| **Enrichment Queue** | AWS SQS FIFO · Azure Service Bus — FIFO per `(tenant_id, context_type)` |
| **Section Classifier + Distiller** | LLM (Claude / GPT-4o) with structured output — governed by Context Schema metadata |
| **Financial Extractor** | Azure Document Intelligence · AWS Textract · `pdfplumber` |
| **Data Quality Gate** | Python rules engine consuming Validation Rules metadata |
| **Context Enricher** | LLM (Claude) — merge prompt governed by Framework Mapping metadata |
| **Derived Metrics Engine** | Python — deterministic formula execution from Metric Definitions |
| **LLM — Business Case Generation** | Claude (Anthropic) — citation-grounded via structured output schema |
| **Financial Modelling Engine** | NumPy / SciPy (Python) — server-side arithmetic, not LLM |
| **Report / Export Generation** | Puppeteer (PDF) · `openpyxl` (XLSX) · `python-pptx` (PPTX) |
| **API Gateway** | AWS API Gateway · Azure APIM · Kong — RBAC enforced at action level |
| **Auth** | Auth0 · AWS Cognito · Azure Entra ID |
| **Billing** | Stripe (metered) · Chargebee |
| **Observability** | Datadog · Grafana + Prometheus — per-tenant dashboards |
| **Audit Log** | Append-only immutable store — AWS CloudTrail · Azure Monitor |

---

> **Open question**  
> **ESG frameworks in scope for v1** — GRI + TCFD only, or also SASB, CSRD, EU Taxonomy?  
> This determines the initial seed content for the Metadata Layer: Context Schema rules, Framework Mapping entries, Metric Definitions, and Validation Rules will all be written per framework.
