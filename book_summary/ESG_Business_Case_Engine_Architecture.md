# ESG Business Case Engine — Architecture Document

> **Document version:** 1.0  
> **Status:** Draft  
> **Date:** June 2026  
> **Prepared by:** Architecture Review  
> **Source spec:** ESG Business Case Engine AppSpec v1.0

---

## Table of Contents

1. [Quality Attribute Priorities](#1-quality-attribute-priorities)
2. [C4 Context Diagram](#2-c4-context-diagram)
3. [C4 Container Diagram](#3-c4-container-diagram)
4. [Bounded Context Map & Event Flows](#4-bounded-context-map--event-flows)
5. [Key Assumption Change Flow (Sequence)](#5-key-assumption-change-flow-sequence)
6. [Architecture Decision Records (ADRs)](#6-architecture-decision-records-adrs)
7. [Gaps and Risks in the Spec](#7-gaps-and-risks-in-the-spec)
8. [Recommendations on Open Questions](#8-recommendations-on-open-questions)
9. [Deployment Topology](#9-deployment-topology)
10. [Summary & Immediate Actions](#10-summary--immediate-actions)
11. [Entity-Relationship Diagram](#11-entity-relationship-diagram)

---

## 1. Quality Attribute Priorities

The spec has a compliance-first, correctness-second character. Architecture decisions are optimised in this order:

| # | Attribute | Why it leads |
|---|-----------|-------------|
| 1 | **Auditability / Correctness** | Every number must be traceable to its source; immutable records are a legal requirement |
| 2 | **Security / Multi-tenancy isolation** | RLS breach = data leak across orgs; direct regulatory exposure |
| 3 | **Availability** (99.5% monthly) | CFO-grade tool — downtime during a board cycle is a crisis |
| 4 | **Performance** (< 2s recalc p95) | Core UX contract; cashflow recalc fires on every assumption change |
| 5 | **Maintainability** | 52-week build, 6 bounded contexts, small team — clean boundaries matter more than premature optimisation |

**Deliberately deprioritised:** throughput (200 users is low), global scale, real-time streaming, eventual consistency (the domain demands strong consistency — a financial model must not show stale NPV).

---

## 2. C4 Context Diagram

```mermaid
C4Context
    title ESG Business Case Engine — System Context

    Person(analyst, "ESG Analyst", "Builds and maintains business cases")
    Person(finance, "Finance Director / CFO", "Challenges assumptions, approves budgets")
    Person(sponsor, "Executive Sponsor", "Issues recommendations, monitors KPIs")
    Person(legal, "Legal / Compliance", "Reviews regulatory obligations")
    Person(admin, "System Administrator", "Manages tenants, users, reference data")

    System(esg, "ESG Business Case Engine", "Structured, auditable platform for ESG initiative business cases — from materiality intake to sponsor recommendation and PIR")

    System_Ext(oidc, "OIDC Provider\n(Okta / Azure AD)", "Authentication & group-to-role mapping")
    System_Ext(sendgrid, "SendGrid", "Email notifications and document delivery")
    System_Ext(gsi, "Global Slavery Index\n(CSV quarterly)", "Country risk scores")
    System_Ext(ilo, "ILO / DoL\n(CSV annual)", "Product & commodity risk scores")
    System_Ext(erp, "ERP (SAP/Oracle)\n[v1.2]", "GL actuals import — planned")
    System_Ext(esg_ratings, "ESG Platforms\n(MSCI/Sustainalytics)\n[v1.2]", "EPIS score export — planned")

    Rel(analyst, esg, "Builds business cases, enters assumptions, runs models")
    Rel(finance, esg, "Challenges assumptions, validates model")
    Rel(sponsor, esg, "Issues recommendations, reviews KPI dashboard")
    Rel(legal, esg, "Reviews regulatory mapping, signs off remediation")
    Rel(admin, esg, "Manages tenants, roles, reference data")

    Rel(esg, oidc, "Authenticates users via OIDC/OAuth 2.0")
    Rel(esg, sendgrid, "Sends notifications and export links")
    Rel(gsi, esg, "Quarterly CSV import of country risk scores")
    Rel(ilo, esg, "Annual CSV import of product risk scores")
    Rel(esg, erp, "v1.2: GL actuals import")
    Rel(esg, esg_ratings, "v1.2: EPIS score export")
```

---

## 3. C4 Container Diagram

```mermaid
C4Container
    title ESG Business Case Engine — Container View

    Person(user, "All Users", "Browser, desktop or tablet")

    System_Boundary(esg, "ESG Business Case Engine") {
        Container(web, "Web Application", "React 18 / TypeScript\nTanStack Query · Zustand", "All user-facing screens. Calls API gateway exclusively. Served as static build from CDN.")

        Container(gateway, "API Gateway", "Node.js / Express\nOpenAPI 3.1", "Single entry point. Validates JWT, enforces RBAC, routes to domain services, applies rate limiting. Versioned at /api/v1/.")

        Container(biz, "Business Logic Service", "Node.js / TypeScript\nDDD bounded contexts", "Orchestrates initiative lifecycle, assumption versioning, validation workflow, scenario management, recommendation issuance. One module per bounded context.")

        Container(fin, "Financial Model Engine", "Python / FastAPI", "Stateless computation. Receives assumption payload → returns cashflow, NPV, IRR, ROI, payback, EPIS score. Pure functions, no DB writes. Cached by assumption hash.")

        Container(refdata, "Reference Data Service", "Node.js / TypeScript", "Serves controlled vocabularies: CoA, country risk scores, product risk benchmarks, EPIS weighting profiles, regulatory obligation templates. Admin UI for uploads.")

        Container(notify, "Notification Service", "Node.js + SendGrid", "Template-based email and in-app alerts. Triggered by domain events. Audit-logged per send.")

        Container(export, "Document Export Service", "Node.js\ndocx · pdfmake", "Async generation of Executive Summary (Word/PDF), assumption register, audit log export. Returns download URL.")

        Container(audit_consumer, "Audit Event Consumer", "Node.js worker", "Subscribes to all domain events on the message bus. Writes immutable records to AUDIT_LOG. Idempotent.")

        Container(reval, "Revalidation Trigger Evaluator", "Node.js cron worker", "Nightly job. Evaluates time-based, threshold-based and event-based revalidation triggers. Emits PIRDue and RevalidationRequired events.")

        ContainerDb(pgmain, "Primary Database", "PostgreSQL 16\nRDS Multi-AZ", "All domain data. Row-level security by org_id. Soft delete on all tables. Append-only AUDIT_LOG partition. Temporal tables for assumption versions.")

        ContainerDb(redis, "Cache & Message Bus", "Redis 7\n(Streams + Cache)", "v1.0: Domain event bus (Redis Streams). Reference data cache (TTL 1hr). Assumption hash → cashflow result cache.")

        ContainerDb(s3, "Document Store", "AWS S3", "Generated export files. Pre-signed download URLs (1hr TTL). Lifecycle policy: archive to Glacier after 90 days.")
    }

    System_Ext(cdn, "CloudFront CDN", "Serves static web assets. Enforces HTTPS.")
    System_Ext(oidc, "OIDC Provider\n(Okta / Azure AD)", "JWT issuance and introspection")
    System_Ext(sendgrid, "SendGrid", "Email delivery")

    Rel(user, cdn, "HTTPS — loads React app")
    Rel(cdn, web, "Serves static build")
    Rel(user, gateway, "HTTPS API calls — Bearer JWT")
    Rel(gateway, oidc, "Validates JWT / introspects token")
    Rel(gateway, biz, "Routes domain operations")
    Rel(gateway, refdata, "Routes reference data reads")
    Rel(gateway, fin, "Triggers cashflow recalculation")
    Rel(gateway, export, "Triggers async document generation")
    Rel(biz, pgmain, "Read/write domain data — parameterised SQL")
    Rel(biz, redis, "Publish domain events to Streams")
    Rel(fin, pgmain, "Read assumptions store")
    Rel(fin, redis, "Read/write assumption hash cache")
    Rel(fin, pgmain, "Write cashflow results")
    Rel(refdata, pgmain, "Read reference tables")
    Rel(refdata, redis, "Cache reference data (TTL 1hr)")
    Rel(audit_consumer, redis, "Subscribe to all event streams")
    Rel(audit_consumer, pgmain, "Append to AUDIT_LOG (append-only)")
    Rel(notify, redis, "Subscribe to notification-triggering events")
    Rel(notify, sendgrid, "Send emails")
    Rel(notify, pgmain, "Write in-app notification records")
    Rel(export, pgmain, "Read initiative data for report generation")
    Rel(export, s3, "Write generated documents")
    Rel(reval, pgmain, "Read KPI triggers and PIR schedules")
    Rel(reval, redis, "Publish PIRDue / RevalidationRequired events")
```

---

## 4. Bounded Context Map & Event Flows

```mermaid
flowchart TB
    subgraph Identity["Identity & Tenancy (upstream — no inbound deps)"]
        IAM["OIDC Auth\nRBAC\nOrg/User mgmt\nRLS session var"]
    end

    subgraph RefData["Reference Data (upstream — read-only to all)"]
        RD["CoA · Country Risk\nProduct Risk · EPIS Profiles\nRegulatory Templates"]
    end

    subgraph Intake["Data Intake & Materiality"]
        MI["Materiality questionnaire\nData checklist gen\nData quality scoring\nProxy flagging · Gap log"]
    end

    subgraph Risk["Risk Assessment"]
        RA["Supplier risk scoring\nESG risk E/S/G\nPriority tier assignment\nCorrective actions"]
    end

    subgraph Initiative["Initiative & Assumptions"]
        IA["Initiative lifecycle\nSub-initiatives · Workstreams\nAssumption register\nVersion history"]
    end

    subgraph FinModel["Financial Modelling (isolated compute)"]
        FM["60M cashflow engine\nNPV · IRR · ROI\nEPIS scoring\n3-scenario · Tornado"]
    end

    subgraph Governance["Governance & Validation"]
        GV["2-stage validation\nRegulatory obligation map\nRemediation records\nSponsor recommendation\nKPI · PIR"]
    end

    subgraph Output["Output & Reporting"]
        OUT["Executive Summary export\nAssumption register export\nAudit log export\nDisclosure readiness report"]
    end

    subgraph AuditCtx["Audit & Compliance (downstream — append-only)"]
        AL["AUDIT_LOG\nImmutable event records\n7-year retention"]
    end

    Identity -->|"org_id session var\nJWT + role claims"| Intake
    Identity -->|"org_id session var\nJWT + role claims"| Risk
    Identity -->|"org_id session var\nJWT + role claims"| Initiative
    Identity -->|"org_id session var\nJWT + role claims"| Governance

    RefData -->|"CoA for GL validation"| Initiative
    RefData -->|"Country/product risk scores"| Risk
    RefData -->|"EPIS weighting profiles"| FinModel
    RefData -->|"Regulatory obligation templates"| Governance

    Intake -->|"DataQualityScored event\nChecklist generated"| Initiative
    Risk -->|"SupplierRiskScored event\nCAP closure rate → EPIS C"| FinModel
    Initiative -->|"AssumptionChanged event\nAssumption payload"| FinModel
    FinModel -->|"CashflowCalculated event\nEPIS score result"| Governance
    Governance -->|"ValidationSigned event\nRecommendationIssued event\nPIRDue event"| Output

    Intake -->|all state changes| AuditCtx
    Risk -->|all state changes| AuditCtx
    Initiative -->|all state changes| AuditCtx
    FinModel -->|all state changes| AuditCtx
    Governance -->|all state changes| AuditCtx

    style Identity fill:#dbeafe,stroke:#3b82f6
    style RefData fill:#dcfce7,stroke:#16a34a
    style AuditCtx fill:#fef9c3,stroke:#ca8a04
    style FinModel fill:#fce7f3,stroke:#db2777
```

### Domain Event Catalogue

| Event | Publisher | Consumers | Payload (key fields) |
|-------|-----------|-----------|----------------------|
| `AssumptionChanged` | Initiative & Assumptions | Financial Engine, Audit Consumer | `initiativeId`, `assumptionId`, `old_hash`, `new_hash`, `changedBy` |
| `DataQualityScored` | Data Intake | Initiative & Assumptions, Audit Consumer | `initiativeId`, `sourceId`, `qualityScore`, `proxyFlag` |
| `SupplierRiskScored` | Risk Assessment | Financial Engine, Audit Consumer | `initiativeId`, `supplierId`, `residualRisk`, `priorityTier` |
| `CashflowCalculated` | Financial Engine | Governance, Audit Consumer | `initiativeId`, `scenarioType`, `npv`, `irr`, `roi`, `episScore` |
| `ValidationSigned` | Governance | Notification Service, Audit Consumer | `initiativeId`, `validatorId`, `isIndependent`, `outcome` |
| `RecommendationIssued` | Governance | Notification Service, Audit Consumer | `initiativeId`, `sponsorId`, `versionNumber`, `assumptionVersionSet` |
| `PIRDue` | Revalidation Evaluator | Notification Service, Audit Consumer | `initiativeId`, `dueDate`, `modelledScenarioRef` |
| `RevalidationRequired` | Revalidation Evaluator | Notification Service, Audit Consumer | `initiativeId`, `triggerType`, `triggerDetail` |
| `ExportRequested` | Output & Reporting | Document Export Service | `initiativeId`, `format`, `requestedBy`, `jobId` |
| `ExportCompleted` | Document Export Service | Notification Service | `jobId`, `downloadUrl`, `expiresAt` |

---

## 5. Key Assumption Change Flow (Sequence)

```mermaid
sequenceDiagram
    participant UI as React App
    participant GW as API Gateway
    participant BIZ as Business Logic
    participant DB as PostgreSQL
    participant BUS as Redis Streams
    participant FIN as Financial Engine
    participant CACHE as Redis Cache
    participant AUDIT as Audit Consumer

    UI->>GW: PATCH /initiatives/{id}/assumptions/{id}\n{field, new_value, change_reason}
    GW->>GW: Validate JWT · check role (ESG Analyst)
    GW->>BIZ: updateAssumption(initiativeId, assumptionId, patch)
    BIZ->>DB: INSERT INTO assumption_version\n(old_value, new_value, reason, user_id, ts)
    BIZ->>DB: UPDATE assumption SET current values
    BIZ->>BUS: PUBLISH AssumptionChanged\n{initiativeId, assumptionId, old_hash, new_hash}
    BIZ-->>GW: 200 OK {assumption, versionNumber}
    GW-->>UI: 200 — assumption saved, version N

    Note over UI: UI shows "Recalculating..." skeleton
    UI->>GW: POST /initiatives/{id}/cashflow/recalculate

    GW->>FIN: POST /compute\n{assumptionPayload, discountRate, scenarioType}
    FIN->>CACHE: GET new_hash → cached result?
    alt Cache miss
        FIN->>FIN: Run pure cashflow computation\n(scipy IRR, 60-month NPV/ROI/payback)
        FIN->>CACHE: SET new_hash → result (TTL 1hr)
    end
    FIN-->>GW: {cashflow[], npv, irr, roi, payback, episScore}
    GW->>DB: UPSERT cashflow_entry rows\n+ cashflow_cost_line\n+ cashflow_benefit_line
    GW-->>UI: 200 — updated cashflow + summary metrics

    BUS-->>AUDIT: Consume AssumptionChanged event
    AUDIT->>CACHE: DEL old_hash (evict stale cache entry)
    AUDIT->>DB: INSERT INTO audit_log (append-only)
```

---

## 6. Architecture Decision Records (ADRs)

### ADR-001: Modular Monolith for Business Logic, not Microservices

| | |
|--|--|
| **Status** | Accepted |
| **Date** | June 2026 |

**Decision:** All bounded contexts live inside one deployable Business Logic Service, structured as internal modules with enforced boundaries (no cross-module DB queries; cross-context communication via in-process events or explicit service calls). Separate deployables only for the Financial Engine (Python, compute isolation) and the four workers (audit consumer, notification, export, revalidation evaluator).

**Alternatives considered:**
- Full microservices per bounded context (10 separate deployables)
- Single unstructured monolith with no internal boundaries

**Rationale:** 200 concurrent users does not create independent scaling pressure across bounded contexts. A small team (implied by a 52-week single-team plan) cannot operate 10 separate services with their own CI pipelines, databases, and on-call runbooks. The spec already calls for DDD bounded contexts — that structure is enforced at the module boundary, not the network boundary. The Financial Engine is genuinely separate: it is Python (not Node.js), stateless, and compute-isolated.

**Trade-offs accepted:** Harder to independently deploy bounded contexts if teams grow significantly in v2+. Mitigation: strict no-cross-module-DB rule enforced from day 1 (linting, code review gate) makes future extraction a refactor, not a rewrite.

---

### ADR-002: Redis Streams for v1.0 Message Bus, with Kafka migration path

| | |
|--|--|
| **Status** | Accepted |
| **Date** | June 2026 |

**Decision:** Use Redis Streams for event delivery in v1.0. All consumers are idempotent. Upgrade to Kafka in v2.0 when throughput, long-term replay, or complex fan-out requirements materialise.

**Alternatives considered:** Kafka from day 1; AWS SQS/SNS; synchronous callbacks.

**Rationale:** At 200 users, Redis Streams (already in the stack for caching) is operationally simpler than running a Kafka cluster. The risk is losing event history if Redis is restarted without persistence — mitigated by enabling Redis AOF persistence (`appendfsync everysec`) and using named consumer groups so events are replayable within the retention window.

**Trade-offs accepted:** Redis Streams lacks Kafka's long-term log retention and mature offset management. Acceptable for v1.0; the spec's own upgrade plan acknowledges this. v2.0 migration path: introduce Kafka alongside Redis Streams, migrate consumers one at a time, then decommission Redis Streams.

---

### ADR-003: Assumption Hash Caching in Financial Engine

| | |
|--|--|
| **Status** | Accepted |
| **Date** | June 2026 |

**Decision:** The Financial Engine caches computation results in Redis, keyed by a deterministic SHA-256 hash of `(assumptionPayload + scenarioType + discountRate)`. Cache TTL: 1 hour. The `AssumptionChanged` event must include `old_assumption_hash` so the audit consumer can explicitly evict the stale cache entry.

**Alternatives considered:** No cache (always recompute); database-layer materialised view; application-layer memoisation.

**Rationale:** The 2-second p95 target is achievable via pure computation at this scale, but caching eliminates redundant recomputes when multiple users view the same scenario simultaneously (common during Finance Director challenge sessions). A deterministic hash is safe because the engine has no side effects — same inputs always produce same outputs.

**Trade-offs accepted:** Cache invalidation adds a dependency on the event bus. If the audit consumer fails to evict the old hash, stale results persist until TTL. Mitigation: the Financial Engine also checks an `invalidated_at` timestamp on the cashflow store — if the stored result is older than the latest assumption change, it recomputes regardless of cache.

---

### ADR-004: Single PostgreSQL Cluster, not Polyglot Persistence

| | |
|--|--|
| **Status** | Accepted |
| **Date** | June 2026 |

**Decision:** All data — domain records, audit log, reference data, cashflow entries — lives in one PostgreSQL 16 RDS Multi-AZ cluster. A read replica serves reporting and export queries to isolate them from OLTP writes.

**Alternatives considered:** Time-series DB for cashflow entries; separate audit database; document store for assumptions.

**Rationale:** The domain is highly relational (24+ entities with FK constraints, CHECK constraints, RLS policies, temporal versioning). PostgreSQL's row-level security, generated columns, CHECK constraints, and append-only table semantics handle all compliance requirements natively. Polyglot persistence would split the transactional boundary across systems — `ASSUMPTION_VERSION` and `AUDIT_LOG` dual-writes must be atomic, which requires a single DB. The compliance cost of a split transaction is higher than any performance benefit at this scale.

**Trade-offs accepted:** Audit log partition growth over 7 years. Mitigation: monthly partitioning + annual export to S3 Glacier, as specified. OLTP/reporting isolation via read replica.

---

### ADR-005: Triple-Layer Audit Log Enforcement

| | |
|--|--|
| **Status** | Accepted |
| **Date** | June 2026 |

**Decision:** The audit log is enforced at three independent layers:
1. **Database trigger** on all sensitive tables — fires on every INSERT/UPDATE/DELETE, writes to `AUDIT_LOG` within the same transaction.
2. **Async event consumer** on the message bus — writes structured event records to `AUDIT_LOG` from domain events.
3. **DB role permissions** — the application DB role has no `UPDATE` or `DELETE` permissions on `AUDIT_LOG`. Only the trigger and a dedicated audit-writer role can write.

**Rationale:** Application-layer audit logging can be bypassed by bugs, direct DB access, or future developers unaware of the convention. Triple enforcement ensures the log survives even if two layers fail simultaneously. The redundancy is intentional — duplicate entries are preferable to gaps.

**Trade-offs accepted:** DB triggers add latency on every write (sub-millisecond at 200 users). Duplicate entries possible if both trigger and consumer fire for the same event — mitigated by a `source` column (`trigger` | `event`) and deduplication in the audit log viewer.

---

### ADR-006: `currency_code` Column Added at Schema Inception

| | |
|--|--|
| **Status** | Accepted |
| **Date** | June 2026 |

**Decision:** Add `currency_code VARCHAR(3)` to all monetary fields in the schema at Phase 0. In v1.0 the Financial Engine asserts `currency_code = 'AUD'` and rejects other values. Multi-currency conversion logic is implemented in v1.2.

**Rationale:** Schema changes to add currency to 15+ tables after go-live carry high migration risk (data backfills, API contract changes, client-side formatting). Adding the column costs nothing at Phase 0. The v1.0 constraint prevents accidental multi-currency data entry while deferring the FX conversion work.

**Trade-offs accepted:** Slight schema verbosity in v1.0. No meaningful cost.

---

### ADR-007: OIDC Multi-Provider via Per-Tenant Configuration

| | |
|--|--|
| **Status** | Accepted |
| **Date** | June 2026 |

**Decision:** The API Gateway treats any OIDC-compliant IdP as a valid token issuer. The `ORGANISATION` table stores `oidc_issuer_url` and `oidc_jwks_endpoint` per tenant. The gateway resolves the correct JWKS endpoint from the tenant context on every request, not from a global config.

**Rationale:** Supporting both Okta and Azure AD via a per-tenant config table is 2–3 days of Phase 0 work. Deferring this to v1.2 when the first Azure AD customer arrives requires a breaking change to the authentication middleware and a re-test cycle. The cost of doing it at Phase 0 is low; the cost of retrofitting is high.

**Trade-offs accepted:** Each new OIDC provider requires a JWKS cache entry per tenant (stored in Redis, TTL 24 hours). Negligible at this scale.

---

## 7. Gaps and Risks in the Spec

### Gap 1 — Cache invalidation is undefined for `AssumptionChanged`

**Severity: High**

The spec states the Financial Engine is "cached by assumption hash" but does not define when the old cache entry is evicted. If `AssumptionChanged` carries only the new hash, the old entry persists in Redis until TTL (up to 1 hour). During that window, a user requesting the pre-change scenario receives stale cached NPV/ROI.

**Fix:** Include `old_assumption_hash` in the `AssumptionChanged` event payload. The audit consumer evicts it from Redis immediately on receipt, before writing to `AUDIT_LOG`.

---

### Gap 2 — Redis Streams persistence is unspecified

**Severity: High**

If Redis restarts without AOF persistence enabled, all un-consumed events are lost. The audit consumer is the most vulnerable — it may miss `AssumptionChanged` or `ValidationSigned` events and produce an incomplete audit log, which is a compliance failure.

**Fix:** Mandate `appendfsync everysec` in the Redis configuration. Use named consumer groups with explicit acknowledgement (`XACK`) so only processed events are dropped from the stream. Include a reconciliation job in Phase 0 that compares `AUDIT_LOG` record counts to `assumption_version` row counts nightly.

---

### Gap 3 — Document export has no queue backpressure

**Severity: Medium**

The spec describes async export with email notification but does not define concurrency limits. If 50 users request Executive Summary exports simultaneously, the export service could exhaust memory or CPU generating 50 concurrent large documents.

**Fix:** Route export requests through a dedicated Redis Stream with a bounded consumer (concurrency: 2–3 workers). Requests queue behind in-flight jobs. This also makes exports retryable on failure with no duplicate sends.

---

### Gap 4 — RLS session variable may not survive connection pooling

**Severity: High**

The spec states RLS policies use `current_setting('app.current_org_id')`. If PgBouncer runs in **transaction mode** (the most common production configuration), the session variable set at connection open is not guaranteed to be present on subsequent transactions from the same logical connection, because the underlying physical connection may have been recycled.

**Fix:** Set `SET LOCAL app.current_org_id = $1` at the start of every transaction, not just at connection open. Alternatively, run PgBouncer in **session mode** — lower connection density, acceptable at 200 users. Document this constraint explicitly in the Phase 0 database runbook.

---

### Gap 5 — `ORGANISATION_HIERARCHY` self-referencing tree has no traversal index

**Severity: Medium**

Group MSA reporting requires traversing the organisation hierarchy to aggregate child org data. Recursive CTEs on a self-referencing table are correct but unindexed. At 10–20 orgs this is fine; at 500+ orgs (realistic for an enterprise with subsidiaries) it degrades.

**Fix:** Add a closure table `ORG_CLOSURE (ancestor_id, descendant_id, depth)` populated by trigger on hierarchy insert/update. Hierarchy traversal queries become a single indexed join instead of a recursive CTE scan.

---

### Gap 6 — EPIS score recalculation race condition

**Severity: Medium**

If two assumption changes fire within the same second (e.g., bulk import or rapid sequential edits), two `AssumptionChanged` events trigger two concurrent cashflow recalculation jobs. Both attempt to write `EPIS_SCORE` for the same initiative. The second write may overwrite the first with a result computed from a partially-updated assumption set.

**Fix:** Add an optimistic lock (`version` column on `EPIS_SCORE`, incremented on each write). The second writer detects the conflict and retries. Alternatively, serialise EPIS recalculation behind a per-initiative Redis lock key — only one recalculation job runs per initiative at any time.

---

### Gap 7 — No export service retry or dead-letter strategy

**Severity: Low**

The spec describes async export with a download URL emailed to the user, but does not define what happens if export generation fails (e.g., a `pdfmake` OOM crash). The user receives no email and has no visibility of the failure.

**Fix:** Add a `export_job` table with status (`queued` | `processing` | `completed` | `failed`). The UI polls this endpoint (or subscribes via Server-Sent Events) so the user sees failure state. Failed jobs go to a dead-letter stream for manual retry or support investigation.

---

## 8. Recommendations on Open Questions

### Q1 — Internal Python financial engine vs external platform (Cube / Anaplan)

**Recommendation: build internally.**

The financial model is not generic — it implements a specific formula (`Net Benefit = Gross × Attribution × Realisation × Confidence`), EPIS scoring (`αF + βR + γC`), a 60-month ramp model, and IRR via sign-change detection. External platforms (Cube, Anaplan) impose their own data model and API surface, creating a translation layer that becomes the most brittle part of the system.

The Python / FastAPI stateless service is the correct call. `scipy.optimize.brentq` handles IRR edge cases (no sign change, multiple sign changes) correctly and with documented behaviour. Keep the engine strictly pure — no DB writes from within the engine, inputs validated against the assumption store before computation, outputs written by the API gateway — and the engine remains independently testable and replaceable.

**The only scenario where an external platform wins:** if the finance team needs to maintain the model logic themselves in a spreadsheet-like environment without engineering involvement. If that's a hard requirement, revisit in v2.0 — but it is not stated in the spec.

---

### Q2 — Single OIDC provider vs both Okta and Azure AD at launch

**Recommendation: support both from day 1, via per-tenant OIDC configuration.**

Store `oidc_issuer_url` and `oidc_jwks_endpoint` per organisation record. The API Gateway resolves the correct JWKS endpoint from tenant context on every request. This is 2–3 days of Phase 0 work.

The cost of retrofitting multi-provider support after launch is high: authentication middleware changes, re-testing the full auth flow, potential breaking changes to existing tenant configurations. The cost of doing it correctly at Phase 0 is low. Every enterprise customer will either be on Okta or Azure AD — there is no single dominant provider.

---

### Q4 — Multi-currency support in v1.0

**Recommendation: add the schema column now; implement only AUD enforcement in v1.0.**

Add `currency_code VARCHAR(3) NOT NULL DEFAULT 'AUD'` to all monetary fields at Phase 0. The Financial Engine asserts `currency_code = 'AUD'` and returns a validation error for any other value. No FX conversion logic is implemented.

When v1.2 ships international support, the column is already present in production data — no migration required. Without the column, adding currency in v1.2 requires a migration on 15+ tables, backfills on potentially millions of rows, API contract changes, and client-side formatting updates. The schema cost of deferring is significantly higher than the 1-day cost of adding the column now.

---

## 9. Deployment Topology

```mermaid
flowchart LR
    subgraph AWS_SYD["AWS ap-southeast-2 (primary — Australian data residency)"]
        CF["CloudFront CDN\n(static assets + HTTPS enforcement)"]
        subgraph EKS["EKS Cluster"]
            direction TB
            GW_POD["API Gateway\n(2–4 pods · HPA on CPU)"]
            BIZ_POD["Business Logic Service\n(2–4 pods · HPA on CPU)"]
            FIN_POD["Financial Model Engine\n(2–4 pods · HPA on CPU)"]
            subgraph Workers["Workers (1–2 pods each)"]
                AUD["Audit Consumer"]
                NOT["Notification Service"]
                EXP["Document Export"]
                REV["Revalidation Evaluator"]
            end
        end
        subgraph Data["Data Layer"]
            RDS_P["RDS PostgreSQL 16\n(Multi-AZ primary)"]
            RDS_R["RDS Read Replica\n(reporting · exports)"]
            REDIS["ElastiCache Redis 7\n(Streams + Cache · AOF enabled)"]
            S3["S3\n(export documents · Glacier lifecycle)"]
            SM["Secrets Manager\n(DB creds · API keys · JWKS cache)"]
        end
    end

    subgraph Azure_DR["Azure eu-west-2 (DR — warm standby)"]
        AZ_DB["Azure Database for PostgreSQL\n(continuous replication from RDS)"]
        AZ_EKS["AKS Cluster\n(pre-provisioned · manual failover gate)"]
    end

    subgraph CICD["CI/CD (GitHub Actions)"]
        GHA["lint → test → build → staging\n(manual gate → production)"]
    end

    CF -->|"serves React build"| GW_POD
    GW_POD --> BIZ_POD
    GW_POD --> FIN_POD
    GW_POD --> EXP
    BIZ_POD --> RDS_P
    BIZ_POD --> REDIS
    FIN_POD --> RDS_R
    FIN_POD --> REDIS
    AUD --> REDIS
    AUD --> RDS_P
    NOT --> REDIS
    EXP --> RDS_R
    EXP --> S3
    REV --> RDS_R
    REV --> REDIS
    RDS_P -.->|"continuous log shipping"| AZ_DB
    GHA -.->|"deploy on merge"| EKS
```

### Environment Strategy

| Environment | Purpose | Promotion gate |
|---|---|---|
| `dev` | Feature branch integration, daily CI | Automatic on push |
| `staging` | Pre-production validation, UAT, load testing | Automatic on PR merge to `main` |
| `production` | Live | Manual approval gate in GitHub Actions |

### Namespace isolation

Each environment runs in a separate EKS namespace. Network policies enforce no cross-namespace traffic. Secrets Manager paths are namespaced (`/esg/prod/`, `/esg/staging/`, `/esg/dev/`).

---

## 10. Summary & Immediate Actions

### Architecture approach in one paragraph

The ESG Business Case Engine is a **compliance-first, API-first SaaS platform** built on a modular monolith (Node.js Business Logic Service with DDD module boundaries), a separate stateless Python computation service (Financial Model Engine), and four lightweight workers (audit, notifications, export, revalidation). All state is owned by a single PostgreSQL 16 cluster with row-level security for multi-tenancy, append-only audit log, and temporal assumption versioning. A Redis layer serves both the event bus (Redis Streams, upgrading to Kafka at v2.0) and the assumption-hash computation cache. The architecture deliberately stays simple — 200 users and a 52-week build do not justify the operational overhead of 10 independent microservices.

### Three immediate actions before Phase 0 starts

| # | Action | Why it cannot wait |
|---|--------|-------------------|
| 1 | Add `currency_code VARCHAR(3) NOT NULL DEFAULT 'AUD'` to all monetary fields in the schema | Post-launch schema migration on 15+ tables is high-risk; column costs nothing now |
| 2 | Add closure table `ORG_CLOSURE (ancestor_id, descendant_id, depth)` to the data model | Recursive CTE on self-referencing hierarchy will degrade at enterprise scale; add the index structure at schema inception |
| 3 | Include `old_assumption_hash` in the `AssumptionChanged` event contract | Without it, stale NPV/ROI values persist in cache for up to 1 hour after an assumption change |

### Phase 0 hardening checklist (beyond the spec)

- [ ] Enable Redis AOF persistence (`appendfsync everysec`) in all environments
- [ ] Set `SET LOCAL app.current_org_id = $1` at transaction start, not connection open (PgBouncer compatibility)
- [ ] Add optimistic lock (`version` column) to `EPIS_SCORE` table
- [ ] Add `export_job` status table with polling endpoint for export failure visibility
- [ ] Add `source` column to `AUDIT_LOG` (`trigger` | `event`) for deduplication
- [ ] Mandate OIDC per-tenant config (`oidc_issuer_url`, `oidc_jwks_endpoint`) in `ORGANISATION` table

---

---

## 11. Entity-Relationship Diagram

All 36 entities are shown: 24 from the original ER diagram + 12 additions from the architecture review (marked **[NEW]**). Entities are grouped by bounded context. Key columns are included; audit and soft-delete columns (`deleted_at`, `created_at`, `updated_at`) are omitted for readability — they are present on every table.

### 11.1 Identity & Tenancy

```mermaid
erDiagram
    ORGANISATION {
        uuid org_id PK
        string name
        string oidc_issuer_url
        string oidc_jwks_endpoint
        string currency_code
        string data_residency_region
        timestamp created_at
    }

    ORGANISATION_HIERARCHY {
        uuid hierarchy_id PK
        uuid parent_org_id FK
        uuid child_org_id FK
        string relationship_type
        date effective_from
        date effective_to
    }

    USER {
        uuid user_id PK
        uuid org_id FK
        string email
        string full_name
        string oidc_subject
        boolean is_active
    }

    ROLE {
        uuid role_id PK
        string role_name
        string[] permissions
    }

    USER_ROLE {
        uuid user_role_id PK
        uuid user_id FK
        uuid role_id FK
        uuid assigned_by FK
        timestamp assigned_at
    }

    ORGANISATION ||--o{ ORGANISATION_HIERARCHY : "parent of"
    ORGANISATION ||--o{ ORGANISATION_HIERARCHY : "child of"
    ORGANISATION ||--o{ USER : "employs"
    USER ||--o{ USER_ROLE : "has"
    ROLE ||--o{ USER_ROLE : "assigned via"
```

### 11.2 Reference Data

```mermaid
erDiagram
    CHART_OF_ACCOUNTS {
        uuid coa_id PK
        uuid org_id FK
        string gl_code
        string account_name
        string account_type
        string cost_type
        boolean is_active
    }

    COUNTRY_RISK {
        uuid country_risk_id PK
        string country_code
        string country_name
        decimal gsi_risk_score
        string ilo_risk_tier
        date last_updated
        string source_version
    }

    PRODUCT_RISK {
        uuid product_risk_id PK
        string commodity_code
        string commodity_name
        decimal risk_score
        string risk_tier
        string ilo_source_ref
        date last_updated
    }

    EPIS_WEIGHTING_PROFILE {
        uuid profile_id PK
        uuid org_id FK
        string profile_name
        string sector
        string esg_category
        decimal alpha_weight
        decimal beta_weight
        decimal gamma_weight
        string approval_status
        uuid approved_by FK
        timestamp approved_at
    }

    REGULATORY_OBLIGATION_TEMPLATE {
        uuid template_id PK
        string jurisdiction
        string legislation_code
        string criterion_code
        string criterion_description
        string evidence_type
        boolean is_mandatory
    }

    ASSUMPTION_BENCHMARK {
        uuid benchmark_id PK
        string esg_category
        string esg_component
        string metric
        decimal p25_value
        decimal p50_value
        decimal p75_value
        uuid source_initiative_id FK
        timestamp updated_at
    }
```

### 11.3 Initiative Core

```mermaid
erDiagram
    INITIATIVE {
        uuid initiative_id PK
        uuid org_id FK
        string name
        string esg_category
        string esg_component
        string esg_subcomponent
        string objective
        string status
        date start_date
        date end_date
        date implementation_end_date
        uuid weighting_profile_id FK
        decimal discount_rate
        string currency_code
    }

    INITIATIVE_ROLE_ASSIGNMENT {
        uuid assignment_id PK
        uuid initiative_id FK
        uuid user_id FK
        uuid role_id FK
        string assignment_type
        uuid assigned_by FK
        timestamp assigned_at
    }

    SUB_INITIATIVE {
        uuid sub_initiative_id PK
        uuid initiative_id FK
        string name
        string description
        string owner_type
        string status
    }

    WORKSTREAM {
        uuid workstream_id PK
        uuid sub_initiative_id FK
        string name
        string description
        uuid lead_user_id FK
        string status
        date planned_start
        date planned_end
    }

    INITIATIVE ||--o{ INITIATIVE_ROLE_ASSIGNMENT : "has roles"
    INITIATIVE ||--o{ SUB_INITIATIVE : "contains"
    SUB_INITIATIVE ||--o{ WORKSTREAM : "has"
```

### 11.4 Data Intake & Materiality

```mermaid
erDiagram
    INITIATIVE {
        uuid initiative_id PK
    }

    MATERIALITY_RECORD {
        uuid materiality_id PK
        uuid initiative_id FK
        string sector
        string[] esg_categories_in_scope
        string[] regulatory_obligations
        timestamp completed_at
        uuid completed_by FK
    }

    DATA_CHECKLIST_ITEM {
        uuid item_id PK
        uuid materiality_id FK
        string data_type_category
        string data_type_name
        boolean is_required
        boolean is_collected
        string collection_notes
    }

    DATA_SOURCE {
        uuid source_id PK
        uuid initiative_id FK
        string source_name
        string source_type
        int recency_score
        int completeness_score
        int authority_score
        int comparability_score
        int independence_score
        decimal overall_quality_score
        boolean proxy_flag
        decimal confidence_discount
        string discount_rationale
    }

    DATA_GAP {
        uuid gap_id PK
        uuid source_id FK
        uuid initiative_id FK
        string field_name
        string gap_type
        string proxy_assumption
        decimal confidence_discount_applied
        string resolution_status
        string resolution_notes
        uuid resolved_by FK
        timestamp resolved_at
    }

    INITIATIVE ||--|| MATERIALITY_RECORD : "has one"
    MATERIALITY_RECORD ||--o{ DATA_CHECKLIST_ITEM : "generates"
    INITIATIVE ||--o{ DATA_SOURCE : "uses"
    DATA_SOURCE ||--o{ DATA_GAP : "has"
```

### 11.5 Risk Assessment

```mermaid
erDiagram
    SUPPLIER {
        uuid supplier_id PK
        uuid org_id FK
        string supplier_name
        string country_code
        string commodity_code
        decimal annual_spend
        boolean is_active
    }

    SUPPLIER_RISK_ASSESSMENT {
        uuid assessment_id PK
        uuid initiative_id FK
        uuid supplier_id FK
        decimal country_risk_score
        decimal product_risk_score
        decimal spend_concentration
        decimal inherent_risk_score
        decimal saq_completion_rate
        decimal audit_quality_score
        decimal cap_closure_rate
        decimal control_effectiveness
        decimal residual_risk_score
        string priority_tier
        string override_justification
        timestamp assessed_at
    }

    CORRECTIVE_ACTION {
        uuid action_id PK
        uuid assessment_id FK
        string finding
        string action_required
        date due_date
        string status
        timestamp closed_at
        uuid verified_by FK
    }

    ESG_RISK {
        uuid risk_id PK
        uuid initiative_id FK
        string esg_pillar
        string risk_topic
        string risk_description
        decimal likelihood_score
        decimal severity_score
        decimal breadth_score
        decimal irreversibility_score
        decimal inherent_risk_score
        decimal control_effectiveness
        decimal residual_risk_score
        boolean cross_pillar_flag
    }

    SUPPLIER ||--o{ SUPPLIER_RISK_ASSESSMENT : "assessed in"
    SUPPLIER_RISK_ASSESSMENT ||--o{ CORRECTIVE_ACTION : "generates"
    ESG_RISK }o--|| SUPPLIER_RISK_ASSESSMENT : "informs"
```

### 11.6 Assumptions & Financial Model

```mermaid
erDiagram
    ASSUMPTION {
        uuid assumption_id PK
        uuid initiative_id FK
        string assumption_type
        string category
        string metric
        string unit
        decimal conservative_value
        decimal realistic_value
        decimal aggressive_value
        string source_type
        decimal source_quality_score
        decimal confidence_level
        boolean sensitivity_relevant
        string validation_status
        int current_version
    }

    ASSUMPTION_VERSION {
        uuid version_id PK
        uuid assumption_id FK
        int version_number
        decimal old_value
        decimal new_value
        uuid changed_by FK
        timestamp changed_at
        string change_reason
        jsonb snapshot_json
    }

    PROJECT_COST {
        uuid cost_id PK
        uuid initiative_id FK
        uuid coa_id FK
        string cost_name
        string cost_type
        int month_number
        decimal amount
        string currency_code
        uuid assumption_id FK
    }

    BENEFIT_LINE {
        uuid benefit_id PK
        uuid initiative_id FK
        uuid coa_id FK
        string benefit_name
        string benefit_category
        decimal gross_amount
        decimal attribution_factor
        decimal realisation_factor
        decimal confidence_factor
        decimal net_amount
        int ramp_start_month
        string benefit_classification
        string commercial_evidence_ref
        uuid assumption_id FK
        string currency_code
    }

    ASSUMPTION ||--o{ ASSUMPTION_VERSION : "versioned by"
    ASSUMPTION ||--o{ PROJECT_COST : "drives"
    ASSUMPTION ||--o{ BENEFIT_LINE : "drives"
```

### 11.7 Scenarios & Cashflow

```mermaid
erDiagram
    SCENARIO {
        uuid scenario_id PK
        uuid initiative_id FK
        string scenario_type
        decimal total_cost
        decimal year1_benefit
        decimal annual_run_rate
        decimal gross_60m
        decimal net_60m
        decimal roi
        decimal npv
        decimal irr
        int payback_month
        decimal ebitda_impact
        decimal epis_uplift
        decimal confidence_score
        timestamp calculated_at
    }

    SCENARIO_ASSUMPTION_OVERRIDE {
        uuid override_id PK
        uuid scenario_id FK
        uuid assumption_id FK
        string scenario_type
        decimal override_value
        decimal delta_from_base
        string rationale
    }

    CASHFLOW_ENTRY {
        uuid entry_id PK
        uuid initiative_id FK
        uuid scenario_id FK
        int month_number
        decimal cost_opex
        decimal cost_capex
        decimal benefit_gross
        decimal benefit_net
        decimal net_cashflow
        decimal cumulative_cashflow
        decimal discounted_cashflow
    }

    CASHFLOW_COST_LINE {
        uuid line_id PK
        uuid cashflow_entry_id FK
        uuid cost_id FK
        decimal amount
        uuid coa_id FK
    }

    CASHFLOW_BENEFIT_LINE {
        uuid line_id PK
        uuid cashflow_entry_id FK
        uuid benefit_id FK
        decimal ramped_amount
        decimal ramp_factor_applied
        uuid coa_id FK
    }

    SCENARIO ||--o{ SCENARIO_ASSUMPTION_OVERRIDE : "has overrides"
    SCENARIO ||--o{ CASHFLOW_ENTRY : "produces"
    CASHFLOW_ENTRY ||--o{ CASHFLOW_COST_LINE : "traced to"
    CASHFLOW_ENTRY ||--o{ CASHFLOW_BENEFIT_LINE : "traced to"
```

### 11.8 EPIS Scoring

```mermaid
erDiagram
    EPIS_SCORE {
        uuid epis_id PK
        uuid initiative_id FK
        uuid weighting_profile_id FK
        decimal alpha_weight
        decimal beta_weight
        decimal gamma_weight
        decimal f_component_score
        decimal r_component_score
        decimal c_component_score
        decimal baseline_score
        decimal target_score
        decimal uplift
        string interpretation_band
        int version
        timestamp calculated_at
    }

    EPIS_ASSUMPTION_INPUT {
        uuid input_id PK
        uuid epis_id FK
        uuid assumption_id FK
        string component
        decimal contribution_value
    }

    EPIS_SCORE ||--o{ EPIS_ASSUMPTION_INPUT : "sourced from"
```

### 11.9 Governance & Validation

```mermaid
erDiagram
    VALIDATION_CHECK {
        uuid check_id PK
        uuid initiative_id FK
        uuid validator_id FK
        string check_type
        string check_description
        string outcome
        string findings
        boolean is_independent
        timestamp signed_off_at
    }

    REGULATORY_OBLIGATION_STATUS {
        uuid status_id PK
        uuid initiative_id FK
        uuid template_id FK
        string jurisdiction
        string criterion_code
        string status
        string evidence_notes
        string action_required
        uuid action_owner FK
        date target_date
    }

    REMEDIATION_RECORD {
        uuid remediation_id PK
        uuid initiative_id FK
        uuid supplier_id FK
        string finding
        string remediation_plan
        date due_date
        string status
        boolean harm_monetised_flag
        uuid verified_by FK
    }

    MEASUREMENT_KPI {
        uuid kpi_id PK
        uuid initiative_id FK
        string kpi_name
        string metric_unit
        decimal baseline_value
        decimal target_value
        string measurement_frequency
        string revalidation_trigger_type
        string revalidation_trigger_detail
        date next_review_date
    }

    REGULATORY_KPI_MAPPING {
        uuid mapping_id PK
        uuid obligation_id FK
        uuid kpi_id FK
        string evidence_description
    }

    SPONSOR_RECOMMENDATION {
        uuid recommendation_id PK
        uuid initiative_id FK
        uuid sponsor_id FK
        int version_number
        string recommendation
        string conditions
        string key_risks
        timestamp issued_at
        uuid assumption_version_set_id FK
        string pdf_watermark
    }

    PIR_RECORD {
        uuid pir_id PK
        uuid initiative_id FK
        uuid reviewer_id FK
        date review_date
        decimal actual_cost
        decimal actual_benefit
        decimal actual_epis_uplift
        decimal variance_pct
        string explanation
        boolean assumptions_updated_flag
    }

    VALIDATION_CHECK }o--|| SPONSOR_RECOMMENDATION : "gates"
    REGULATORY_OBLIGATION_STATUS }o--|| REGULATORY_KPI_MAPPING : "evidenced by"
    MEASUREMENT_KPI ||--o{ REGULATORY_KPI_MAPPING : "maps to"
    MEASUREMENT_KPI ||--o{ PIR_RECORD : "reviewed in"
```

### 11.10 Audit & Compliance

```mermaid
erDiagram
    AUDIT_LOG {
        uuid log_id PK
        string table_name
        uuid record_id
        string field_name
        text old_value
        text new_value
        uuid changed_by FK
        timestamp changed_at
        string ip_address
        string session_id
        string source
    }
```

### 11.11 Full Cross-Context Relationship Map

The diagram below shows how the major entities relate across bounded contexts, without field detail.

```mermaid
erDiagram
    ORGANISATION ||--o{ USER : ""
    ORGANISATION ||--o{ INITIATIVE : ""
    ORGANISATION ||--o{ CHART_OF_ACCOUNTS : ""
    ORGANISATION ||--o{ EPIS_WEIGHTING_PROFILE : ""
    ORGANISATION ||--o{ ORGANISATION_HIERARCHY : ""

    INITIATIVE ||--|| MATERIALITY_RECORD : ""
    INITIATIVE ||--o{ DATA_SOURCE : ""
    INITIATIVE ||--o{ SUPPLIER_RISK_ASSESSMENT : ""
    INITIATIVE ||--o{ ESG_RISK : ""
    INITIATIVE ||--o{ ASSUMPTION : ""
    INITIATIVE ||--o{ PROJECT_COST : ""
    INITIATIVE ||--o{ BENEFIT_LINE : ""
    INITIATIVE ||--o{ SCENARIO : ""
    INITIATIVE ||--o{ CASHFLOW_ENTRY : ""
    INITIATIVE ||--|| EPIS_SCORE : ""
    INITIATIVE ||--o{ VALIDATION_CHECK : ""
    INITIATIVE ||--o{ REGULATORY_OBLIGATION_STATUS : ""
    INITIATIVE ||--o{ REMEDIATION_RECORD : ""
    INITIATIVE ||--o{ MEASUREMENT_KPI : ""
    INITIATIVE ||--o{ SPONSOR_RECOMMENDATION : ""
    INITIATIVE ||--o{ PIR_RECORD : ""
    INITIATIVE ||--o{ INITIATIVE_ROLE_ASSIGNMENT : ""
    INITIATIVE ||--o{ SUB_INITIATIVE : ""

    ASSUMPTION ||--o{ ASSUMPTION_VERSION : ""
    ASSUMPTION ||--o{ SCENARIO_ASSUMPTION_OVERRIDE : ""
    ASSUMPTION ||--o{ EPIS_ASSUMPTION_INPUT : ""

    CASHFLOW_ENTRY ||--o{ CASHFLOW_COST_LINE : ""
    CASHFLOW_ENTRY ||--o{ CASHFLOW_BENEFIT_LINE : ""

    SUPPLIER ||--o{ SUPPLIER_RISK_ASSESSMENT : ""
    SUPPLIER_RISK_ASSESSMENT ||--o{ CORRECTIVE_ACTION : ""

    MEASUREMENT_KPI ||--o{ REGULATORY_KPI_MAPPING : ""
    REGULATORY_OBLIGATION_STATUS ||--o{ REGULATORY_KPI_MAPPING : ""

    EPIS_SCORE ||--o{ EPIS_ASSUMPTION_INPUT : ""
    SCENARIO ||--o{ SCENARIO_ASSUMPTION_OVERRIDE : ""
    SCENARIO ||--o{ CASHFLOW_ENTRY : ""
```

### 11.12 Entity Summary Table

| # | Entity | Context | Type | Key constraints |
|---|--------|---------|------|-----------------|
| 1 | ORGANISATION | Identity | Core | — |
| 2 | USER | Identity | Core | Unique on (org_id, email) |
| 3 | ROLE | Identity | Reference | — |
| 4 | USER_ROLE | Identity | Junction | — |
| 5 | ORGANISATION_HIERARCHY **[NEW]** | Identity | Self-ref | Closure table recommended |
| 6 | CHART_OF_ACCOUNTS | Reference Data | Reference | Unique on (org_id, gl_code) |
| 7 | COUNTRY_RISK | Reference Data | Reference | Unique on (country_code, last_updated) |
| 8 | PRODUCT_RISK | Reference Data | Reference | Unique on (commodity_code, last_updated) |
| 9 | EPIS_WEIGHTING_PROFILE | Reference Data | Reference | CHECK alpha+beta+gamma = 1.0 |
| 10 | REGULATORY_OBLIGATION_TEMPLATE | Reference Data | Reference | — |
| 11 | ASSUMPTION_BENCHMARK **[NEW]** | Reference Data | Feedback | Updated from PIR actuals |
| 12 | INITIATIVE | Initiative Core | Core | Unique on (org_id, name) |
| 13 | INITIATIVE_ROLE_ASSIGNMENT **[NEW]** | Initiative Core | Junction | Replaces hard-coded owner/sponsor FKs |
| 14 | SUB_INITIATIVE | Initiative Core | Core | — |
| 15 | WORKSTREAM | Initiative Core | Core | — |
| 16 | MATERIALITY_RECORD | Data Intake | Core | Unique on initiative_id |
| 17 | DATA_CHECKLIST_ITEM | Data Intake | Core | — |
| 18 | DATA_SOURCE | Data Intake | Core | CHECK confidence_discount BETWEEN 0 AND 1 |
| 19 | DATA_GAP | Data Intake | Core | 4-value status enum |
| 20 | SUPPLIER | Risk | Core | Unique on (org_id, supplier_name, country_code) |
| 21 | SUPPLIER_RISK_ASSESSMENT | Risk | Core | Residual cannot be manually overridden |
| 22 | CORRECTIVE_ACTION **[NEW]** | Risk | Core | CAP closure rate feeds EPIS C-component |
| 23 | ESG_RISK | Risk | Core | — |
| 24 | ASSUMPTION | Assumptions | Core | Unique on (initiative_id, category, metric) |
| 25 | ASSUMPTION_VERSION **[NEW]** | Assumptions | Immutable | Append-only; no UPDATE/DELETE |
| 26 | PROJECT_COST | Assumptions | Core | GL code mandatory |
| 27 | BENEFIT_LINE | Assumptions | Core | CHECK attribution/realisation/confidence BETWEEN 0 AND 1; net_amount is computed |
| 28 | SCENARIO | Financial | Core | — |
| 29 | SCENARIO_ASSUMPTION_OVERRIDE **[NEW]** | Financial | Junction | Resolves Scenario ↔ Assumption M:M |
| 30 | CASHFLOW_ENTRY | Financial | Core | UNIQUE (initiative_id, month_number) per scenario |
| 31 | CASHFLOW_COST_LINE **[NEW]** | Financial | Junction | Traceability — cost → cashflow month |
| 32 | CASHFLOW_BENEFIT_LINE **[NEW]** | Financial | Junction | Traceability — benefit → cashflow month |
| 33 | EPIS_SCORE | EPIS | Core | CHECK alpha+beta+gamma = 1.0; optimistic lock version column |
| 34 | EPIS_ASSUMPTION_INPUT **[NEW]** | EPIS | Junction | Resolves EPIS_SCORE ↔ ASSUMPTION M:M |
| 35 | VALIDATION_CHECK | Governance | Core | is_independent derived from validator role |
| 36 | REGULATORY_OBLIGATION_STATUS | Governance | Core | Gap rows require action + owner + date |
| 37 | REMEDIATION_RECORD | Governance | Core | CHECK harm_monetised_flag = false |
| 38 | MEASUREMENT_KPI | Governance | Core | — |
| 39 | REGULATORY_KPI_MAPPING **[NEW]** | Governance | Junction | Obligation ↔ KPI evidence link |
| 40 | SPONSOR_RECOMMENDATION | Governance | Versioned | UNIQUE (initiative_id, version_number); stores assumption version set at issue |
| 41 | PIR_RECORD **[NEW]** | Governance | Core | Available only 12 months after implementation_end_date |
| 42 | AUDIT_LOG **[NEW]** | Audit | Append-only | No UPDATE/DELETE permission on app role; source column (trigger\|event) |

> **Bold [NEW]** = entities added in the architecture review (Section 7 of the AppSpec). Total: 42 entities (24 original + 12 review additions + 6 intermediate junction/reference tables required to complete the model).

---

*Document generated from ESG Business Case Engine AppSpec v1.0. Architecture decisions are based on the constraints, NFRs and technology choices stated in the specification.*
