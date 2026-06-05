# ESG Business Case Engine — Application Specification v3.0

> **Document version:** 3.0 — Configuration-driven, multi-tenant SaaS edition  
> **Status:** Draft for review  
> **Supersedes:** v2.0 (generalised edition)  
> **Prepared by:** ESG Custodian Product Team  
> **Date:** June 2026  
> **Classification:** Internal — Confidential

---

## Table of contents

1. [Executive summary](#1-executive-summary)
2. [Multi-tenant SaaS architecture](#2-multi-tenant-saas-architecture)
3. [Configuration model](#3-configuration-model)
4. [ESG category and reference configuration](#4-esg-category-and-reference-configuration)
5. [Industry and sub-industry configuration](#5-industry-and-sub-industry-configuration)
6. [Materiality configuration](#6-materiality-configuration)
7. [Chart of accounts — company-specific](#7-chart-of-accounts--company-specific)
8. [Workspace-level isolation](#8-workspace-level-isolation)
9. [Context-resolution engine](#9-context-resolution-engine)
10. [Functional requirements](#10-functional-requirements)
11. [User stories](#11-user-stories)
12. [API design](#12-api-design)
13. [Data model](#13-data-model)
14. [Non-functional requirements](#14-non-functional-requirements)
15. [Security and access control](#15-security-and-access-control)
16. [Phased delivery roadmap](#16-phased-delivery-roadmap)
17. [Open questions](#17-open-questions)

---

## 1. Executive summary

### 1.1 What this version adds

Version 3.0 introduces three architectural enhancements to the generalised engine defined in v2.0:

1. **Configuration-driven setup** — ESG category taxonomy, ESG reference codes, industry and sub-industry taxonomy, and materiality definitions are fully configurable at the platform level. No ESG or industry context is hardcoded. Administrators configure the taxonomy; the engine resolves from it.

2. **Company-specific Chart of Accounts** — every tenant manages its own CoA. GL codes are never shared across tenants. The engine maps costs and benefits to the tenant's own account codes, not a platform default.

3. **Multi-tenant SaaS with workspace-level isolation** — the platform is a true multi-tenant SaaS product. Within each tenant, workspaces provide a second layer of isolation for business units, regions, subsidiaries, or ESG program areas. Data, configuration, and access control are isolated at both the tenant and workspace level.

### 1.2 The three-layer isolation model

```
Platform  ──  Global master data (ESG taxonomy, initiative library, regulatory frameworks)
    │
Tenant    ──  Company-specific config (CoA, materiality overrides, EPIS profiles, roles)
    │
Workspace ──  Program-level isolation (business cases, assumptions, approvals, reports)
```

Every piece of data in the system belongs to exactly one of these three levels. Rules flow downward — workspace inherits from tenant, tenant inherits from platform — but data never flows upward or across.

### 1.3 Configuration principles

| Principle | Description |
|-----------|-------------|
| **Platform-level is the default** | ESG taxonomy, 566 initiative templates, materiality definitions, regulatory frameworks, business size profiles, and EPIS weighting defaults ship as platform-level configuration |
| **Tenant-level overrides platform** | A tenant can override any platform-level configuration (e.g. rename an ESG category, add industry-specific benchmarks, define a custom EPIS profile, upload their own CoA) |
| **Workspace-level overrides tenant** | A workspace can further restrict or extend the tenant configuration (e.g. limit available ESG components, apply a region-specific regulatory framework, set a workspace-level discount rate) |
| **CoA is always tenant-specific** | The Chart of Accounts is never shared across tenants. It is always uploaded or configured at the tenant level and optionally scoped further at workspace level |
| **Materiality is configurable at tenant level** | Platform ships default materiality definitions. Tenants can override primary metrics, secondary metrics, IRO classifications, and EPIS weights for their sector and reporting obligations |

---

## 2. Multi-tenant SaaS architecture

### 2.1 Tenancy model

The platform uses a **shared infrastructure, isolated data** multi-tenancy model:

- Single application deployment serves all tenants
- All data is isolated at the database layer using row-level security (RLS) on `tenant_id`
- No cross-tenant data access is possible at any layer — API, database, or cache
- Each tenant has its own encryption key managed via AWS KMS (envelope encryption)
- Tenant provisioning is automated via a self-service onboarding flow

### 2.2 Workspace model

Within each tenant, **workspaces** provide a second isolation boundary:

```
Tenant: Acme Corporation
├── Workspace: Australia — ESG Program 2026
│   ├── Business cases (AUS initiatives)
│   ├── CoA mapping (AUS GL codes)
│   ├── Workspace roles and approvers
│   └── Workspace-level regulatory scope (AUS MSA, NGER Act)
├── Workspace: UK Operations
│   ├── Business cases (UK initiatives)
│   ├── CoA mapping (UK GL codes)
│   └── Workspace regulatory scope (UK MSA, CSRD)
└── Workspace: Group ESG Reporting
    ├── Read-only aggregated view across workspaces
    └── Consolidated disclosure readiness dashboard
```

A user can be a member of one or many workspaces within a tenant. Access to a workspace does not grant access to any other workspace. A workspace administrator can exist independently of a tenant administrator.

### 2.3 Data ownership hierarchy

| Object | Owner level | Isolation |
|--------|-------------|-----------|
| ESG taxonomy (categories, components, subcomponents) | Platform | Visible to all tenants; overridable at tenant level |
| Initiative library (566 templates) | Platform | Visible to all tenants; tenant can add private templates |
| Regulatory framework definitions | Platform | Visible to all tenants; tenant can add private frameworks |
| Business size profiles | Platform | Visible to all tenants; tenant can add custom profiles |
| EPIS weighting profiles (defaults) | Platform | Visible to all tenants; tenant can override |
| Materiality definitions (defaults) | Platform | Visible to all tenants; tenant can override per component |
| Tenant-level CoA | Tenant | Isolated; never visible outside tenant |
| Tenant-level materiality overrides | Tenant | Isolated; applied to all workspaces in the tenant |
| Tenant-level EPIS profile overrides | Tenant | Isolated; applied to all workspaces in the tenant |
| Tenant-level private initiative templates | Tenant | Isolated; available to all workspaces in the tenant |
| Workspace business cases | Workspace | Isolated; not visible to other workspaces |
| Workspace assumptions and benefits | Workspace | Isolated; not visible to other workspaces |
| Workspace validation records | Workspace | Isolated; not visible to other workspaces |
| Workspace approvals and recommendations | Workspace | Isolated; not visible to other workspaces |
| Workspace CoA scope | Workspace | Subset of tenant CoA; can restrict GL codes visible in this workspace |

### 2.4 Cross-workspace aggregation

A **Group Reporting** workspace type can be created at the tenant level. It has read-only access to aggregated data from all workspaces the tenant administrator grants it access to. It cannot modify any data in any workspace. It is used for:

- Consolidated EPIS score dashboard across all workspaces
- Cross-workspace disclosure readiness status (e.g. group MSA statement)
- Portfolio-level NPV and benefit realisation tracking
- PIR completion rate across all initiatives in the tenant

---

## 3. Configuration model

### 3.1 Configuration layers and precedence

Every configurable item follows a **three-layer inheritance** model:

```
Platform default
    ↓ (tenant can override)
Tenant configuration
    ↓ (workspace can further restrict or extend)
Workspace configuration
```

The effective value for any configuration item is resolved at runtime by walking up from workspace → tenant → platform and taking the most specific non-null value.

### 3.2 What is configurable at each layer

#### Platform level (managed by ESG Custodian)

| Configuration item | Description |
|--------------------|-------------|
| ESG category taxonomy | Three categories: Environmental, Social, Governance — not changeable |
| ESG component list | 15 components with abbreviation prefixes (ECC, EWM, SMS, etc.) |
| ESG subcomponent list | Sub-components per component (e.g. Scope 1, Scope 2, Modern Slavery) |
| Initiative library | 566 initiative templates with workstreams, objectives, assumption hints |
| Materiality definitions | Primary metric, unit, secondary metrics, IRO classification per subcomponent |
| Business size profiles | 19 profiles from Micro/Sole Trader to Global Corporation |
| Regulatory framework definitions | All jurisdictions and ESG categories |
| EPIS weighting profiles | Default α/β/γ per ESG component |
| Global project roles | 11 GLS roles available to all tenants |
| Initiative-specific roles | Roles by ESG component prefix (ECC, SMS, SLP, etc.) |
| GICS industry taxonomy | Full GICS hierarchy used for industry/sub-industry selection |

#### Tenant level (managed by Tenant Administrator)

| Configuration item | Description |
|--------------------|-------------|
| Chart of Accounts | Company-specific GL codes and descriptions — never shared |
| Materiality overrides | Override primary metric, secondary metrics, or EPIS weight per component for the tenant's sector |
| EPIS weighting profile overrides | Tenant-specific α/β/γ profiles replacing platform defaults |
| Private initiative templates | Tenant-created templates not in the platform library |
| Regulatory framework scope | Select which platform frameworks apply to this tenant; add tenant-specific frameworks |
| Assumption benchmarks | Tenant-specific assumption benchmark data (from PIR actuals or internal data) |
| Custom project roles | Tenant-specific roles beyond the platform role pool |
| Discount rate default | Default NPV discount rate for all initiatives (overridable at workspace level) |
| Reporting currency | Base currency for all financial models in this tenant |
| Tenant-level EPIS benchmark | What constitutes Low / Medium / High / Critical for this tenant |

#### Workspace level (managed by Workspace Administrator)

| Configuration item | Description |
|--------------------|-------------|
| CoA scope | Restrict which tenant GL codes are visible in this workspace (e.g. AU cost centres only) |
| Initiative template scope | Restrict which ESG components or platform templates are available in this workspace |
| Regulatory framework scope | Select which tenant-level frameworks apply to this workspace (e.g. UK MSA only for UK workspace) |
| Discount rate override | Workspace-specific discount rate overriding tenant default |
| Approval workflow | Define the approval chain (validator roles, sponsor roles) for this workspace |
| Workspace reporting currency | Override tenant currency for FX-specific workspaces |
| Data retention policy | Workspace-level data retention period (within tenant policy bounds) |
| Workspace EPIS benchmark | Override tenant-level EPIS band thresholds for this workspace |

---

## 4. ESG category and reference configuration

### 4.1 Taxonomy configuration

The ESG taxonomy is configured at the platform level and flows to all tenants. The three-tier structure — **Category → Component → Subcomponent** — is fixed at the category level (E, S, G) but fully configurable at component and subcomponent level.

#### Category level (fixed, not configurable)

```
Environmental (E)
Social (S)
Governance (G)
```

#### Component level (platform-configurable, tenant-overridable label)

Each component has:
- A unique prefix code (e.g. `ECC`, `SMS`, `GEI`)
- A display name (configurable at tenant level for localisation)
- A default risk subject type
- A default EPIS weighting profile reference
- A list of applicable regulatory framework types

Tenants can:
- Rename component labels for their internal terminology
- Hide components not relevant to their operations
- Cannot add new component prefix codes (platform-managed)

#### Subcomponent level (platform-configurable, tenant-overridable)

Each subcomponent has:
- A parent component reference
- A display name
- A materiality record reference (primary metric, unit, secondary metrics)
- An IRO classification (Impact / Risk / Opportunity)
- An impact direction (Positive / Negative)

Tenants can:
- Override the display name
- Override the materiality record (primary metric, secondary metrics)
- Add tenant-specific subcomponents under any platform component

### 4.2 ESG Reference configuration

The ESG Reference data configures the **abbreviation-to-behaviour** mapping. When an analyst selects an initiative with prefix `ECC`, the reference record determines:

```
ECC reference record:
├── prefix: ECC
├── display_name: "Climate Change and Carbon Footprint"
├── category: Environmental
├── risk_subject_type: Site / Asset / Emission source
├── risk_scoring_formula: emission_intensity × regulatory_exposure × asset_age
├── epis_profile_default: ECC_DEFAULT
├── role_pool_prefix: ECC
├── regulatory_framework_types: [TCFD, CSRD, NGER, GHG_Protocol]
├── physical_unit_tracking: true
├── primary_unit_default: tCO2e/year
└── data_checklist_template: ECC_CHECKLIST
```

**Configuration interface:** Platform administrators manage ESG reference records via a structured admin form. Tenant administrators can view all reference records and override `display_name`, `epis_profile_default`, and `regulatory_framework_types` for their tenant.

### 4.3 Initiative template configuration

The 566 initiative templates are platform-level but tenants can:

1. **Create private templates** — visible only within the tenant; appear in the initiative selector alongside platform templates
2. **Override template fields** — customise objective text, workstream descriptions, assumption hints for any platform template (override stored at tenant level; platform template unchanged)
3. **Disable templates** — hide platform templates not relevant to the tenant's operations (e.g. a software company disabling all Biodiversity templates)
4. **Tag templates** — apply tenant-specific tags to platform templates for filtering (e.g. "Priority 2026", "Mandatory reporting")

#### Template selector cascade

```
Initiative selector shows:
1. Tenant private templates (always first)
2. Platform templates NOT disabled by tenant
   - Filtered by selected ESG component
   - Filtered by selected industry / sub-industry
   - Sorted by relevance score (component match × industry match)
3. Search across all available templates
```

---

## 5. Industry and sub-industry configuration

### 5.1 GICS taxonomy

The platform ships with the full **GICS (Global Industry Classification Standard)** hierarchy effective close of 17 March 2023. This is the platform-level taxonomy used for:

- Filtering the initiative library to relevant initiatives
- Resolving industry-specific assumption benchmarks
- Determining regulatory applicability
- Calibrating risk scoring weights

The GICS hierarchy has four levels:

```
Sector (e.g. 10 — Energy)
└── Industry Group (e.g. 1010 — Energy)
    └── Industry (e.g. 10101010 — Oil and Gas Drilling)
        └── Sub-Industry (e.g. 10101010 — Oil and Gas Drilling [detail])
```

For initiative selection, the platform uses the **Industry Group** and **Sub-Industry** levels as the two configurable dimensions (consistent with the ESG Initiatives file structure).

### 5.2 Tenant-level industry configuration

At tenant setup, the Tenant Administrator:

1. **Selects primary industry and sub-industry** — the organisation's primary GICS classification; pre-populates all initiative library filters and assumption benchmark lookups
2. **Adds secondary industries** — for diversified organisations operating across multiple sectors
3. **Maps workspace to industry** — each workspace can be assigned a specific industry/sub-industry (e.g. the Mining workspace uses a different industry than the Finance workspace within the same tenant)

#### Industry profile record (per tenant)

```
TENANT_INDUSTRY_PROFILE:
├── tenant_id
├── primary_industry_group (e.g. "1010 - Energy")
├── primary_sub_industry (e.g. "10102030 - Oil and Gas Refining")
├── secondary_industries[] (optional)
├── industry_label_override (optional tenant display name)
└── assumption_benchmark_source (industry-specific / cross-industry / tenant-uploaded)
```

### 5.3 How industry drives assumption benchmarks

When an initiative is created, the assumption benchmark lookup follows this cascade:

```
1. Tenant-uploaded benchmark (esg_component + sub_industry match)
2. Platform benchmark (esg_component + sub_industry match)
3. Platform benchmark (esg_component + industry_group match)
4. Platform benchmark (esg_component + cross-industry)
5. No benchmark — assumption shown as blank with a mandatory-entry flag
```

Each assumption row in the model displays its benchmark source level so the analyst and Finance Director can assess confidence.

### 5.4 Initiative library filtering by industry

The initiative library filter works in two modes:

**Strict mode** — shows only initiatives tagged to the tenant's primary industry or sub-industry  
**Broad mode** — shows all initiatives for the ESG component, with industry relevance score shown  

The relevance score is calculated as:  
`score = (sub_industry_match × 3) + (industry_group_match × 2) + (all_industries_match × 1)`

Platform templates tagged `1000 - All Industries` always appear in both modes.

---

## 6. Materiality configuration

### 6.1 Platform-level materiality definitions

The platform ships with materiality definitions for every ESG subcomponent. Each definition contains:

| Field | Example (Modern Slavery) | Example (Scope 1 Climate) |
|-------|--------------------------|---------------------------|
| `primary_metric` | Suppliers audited for Modern Slavery | Direct emissions avoided |
| `primary_metric_unit` | % of tier-1 (or tier-n) | tCO2e/year |
| `secondary_metric_1` | Remediation actions closed % | Fuel use reduction (L) |
| `secondary_metric_2` | Incidents prevented | Refrigerant leaks prevented (kg CO2e) |
| `explanation` | [full sub-component description] | [full sub-component description] |
| `iro_classification` | Impact | Risk |
| `impact_direction` | Negative (harm reduction) | Positive (benefit) |
| `epis_beta_weight_default` | 0.55 | 0.35 |

### 6.2 Tenant-level materiality overrides

Tenants can override any field in the platform materiality definition for any ESG subcomponent. Common override scenarios:

**Sector-specific metric** — A mining company may override the Biodiversity subcomponent primary metric from `Habitat restored (ha/year)` to `Rehabilitation completions (sites/year)` to match their regulatory reporting obligation.

**Regulatory-driven unit change** — An EU-domiciled tenant may change the Scope 1 primary metric unit from `tCO2e/year` to `tCO2e/year (location-based, ESRS E1)` to be explicit about the CSRD methodology.

**EPIS weight adjustment** — A regulated utility may increase the γ (compliance) weight for Energy Management initiatives from the platform default of 0.25 to 0.40 because their regulatory reporting obligation is the dominant business driver.

#### Materiality override record

```
TENANT_MATERIALITY_OVERRIDE:
├── tenant_id
├── esg_subcomponent_id (references platform materiality)
├── primary_metric_override (null = use platform value)
├── primary_metric_unit_override (null = use platform value)
├── secondary_metric_1_override (null = use platform value)
├── secondary_metric_2_override (null = use platform value)
├── iro_classification_override (null = use platform value)
├── epis_alpha_override (null = use platform value)
├── epis_beta_override (null = use platform value)
├── epis_gamma_override (null = use platform value)
├── override_rationale (required when any field is overridden)
├── overridden_by (Tenant Administrator user_id)
└── effective_from (date)
```

### 6.3 Effective materiality at runtime

When the context-resolution engine reads materiality for an initiative, it applies this resolution:

```python
def resolve_materiality(subcomponent_id, tenant_id):
    override = get_tenant_override(subcomponent_id, tenant_id)
    platform = get_platform_materiality(subcomponent_id)
    
    return {
        'primary_metric':  override.primary_metric or platform.primary_metric,
        'unit':            override.unit or platform.unit,
        'secondary_1':     override.secondary_1 or platform.secondary_1,
        'secondary_2':     override.secondary_2 or platform.secondary_2,
        'iro':             override.iro or platform.iro,
        'alpha':           override.alpha or platform.alpha,
        'beta':            override.beta or platform.beta,
        'gamma':           override.gamma or platform.gamma,
        'source':          'tenant-override' if override else 'platform-default'
    }
```

The `source` field is displayed on every KPI row and EPIS score so users know whether the materiality definition is platform-standard or tenant-customised.

### 6.4 Materiality configuration UI

The materiality configuration screen is accessible to Tenant Administrators under **Settings → Materiality**. It shows:

- A three-level tree: Category → Component → Subcomponent
- Each subcomponent row shows platform values and, if overridden, tenant values side-by-side
- An "Override" button opens an inline edit form with a mandatory rationale field
- A "Reset to platform default" button removes the override
- An override history log shows all changes with user, date and reason
- An export to CSV for audit purposes

---

## 7. Chart of accounts — company-specific

### 7.1 CoA isolation principle

The Chart of Accounts is **always tenant-specific**. There is no platform-level CoA. No GL code data is ever shared between tenants. A tenant's CoA structure is private by default and never visible to other tenants, platform administrators, or ESG Custodian staff.

### 7.2 CoA structure

The platform expects a simple two-column CoA structure (consistent with the ESG Initiatives file's `CoA - Example` sheet):

```
GL Code    |  GL Description
-----------|-----------------
111000     |  Income from Sales
125000     |  Carbon Credits and Incentives
211000     |  Cost of Goods Sold
311100     |  Salaries Employees
322000     |  ESG Training
341000     |  Consulting Services
345000     |  Technology Services
346100     |  ESG - Environmental Services
346200     |  ESG - Social Services
346300     |  ESG - Governance Services
388500     |  Subscriptions
411000     |  Incremental Revenue
488300     |  IT Implementation & Set-Up
541000     |  Material Quality Rework
552000     |  Expedite Freight
623000     |  Legal / Compliance Risk Provision
```

Additional optional fields supported:

- `account_type` — Revenue / COGS / Labour / OPEX / CAPEX / Provision
- `cost_centre` — for workspace-level filtering
- `department` — for initiative ownership alignment
- `is_active` — to soft-exclude retired codes from selectors

### 7.3 CoA upload and management

**Initial load:** Tenant Administrator uploads CoA as a CSV or Excel file during tenant onboarding. The system validates GL code uniqueness and description completeness. Partial uploads are rejected — all rows must pass validation before any are committed.

**Updates:** Tenant Administrator can:
- Add new GL codes at any time
- Update descriptions (does not retroactively change existing business cases)
- Deactivate codes (removes from selectors; does not affect existing mapped lines)
- Bulk replace via CSV upload (diff shown before commit)

**Workspace CoA scope:** Workspace Administrators can restrict the visible GL codes to a subset of the tenant CoA. For example, the UK workspace may only show GL codes mapped to UK cost centres. This is a filter, not a copy — the underlying CoA data remains at tenant level.

### 7.4 CoA mapping in the financial model

Every cost and benefit line in a business case references a tenant GL code. The mapping is enforced:

- GL code field is mandatory on every cost row and benefit row
- The selector shows only GL codes active in the tenant CoA and within the workspace CoA scope
- Unmapped GL codes (if a code is deactivated after a business case is saved) are flagged as `[GL code retired — remap required]` in the validation layer
- Export of costs and benefits always includes the GL description alongside the code, so the output is readable without the CoA reference

---

## 8. Workspace-level isolation

### 8.1 Workspace types

| Type | Description | Key constraint |
|------|-------------|----------------|
| **Standard** | Default workspace for a business unit, region, or ESG program | Full CRUD on business cases within the workspace |
| **Read-only** | Stakeholder workspace for reporting consumers (e.g. Board, Audit Committee) | Read access to approved business cases and dashboards only |
| **Group Reporting** | Aggregated view across selected standard workspaces | Read-only; no initiative creation; consolidated metrics only |
| **Sandbox** | Copy of a standard workspace for modelling and training | Data does not flow to any report or approval; clearly labelled |

### 8.2 Workspace data model

```
WORKSPACE:
├── workspace_id (UUID)
├── tenant_id (FK — never null)
├── workspace_name
├── workspace_type (standard / read-only / group-reporting / sandbox)
├── parent_workspace_id (null for top-level; set for nested workspaces)
├── industry_group_override (null = inherit from tenant)
├── sub_industry_override (null = inherit from tenant)
├── discount_rate_override (null = inherit from tenant)
├── reporting_currency_override (null = inherit from tenant)
├── regulatory_scope_ids[] (subset of tenant regulatory frameworks)
├── coa_scope_filter (cost_centre or department filter on tenant CoA)
├── epis_band_override (null = inherit from tenant)
├── data_retention_days (within tenant policy bounds)
├── is_active
└── created_at
```

### 8.3 Workspace membership and roles

Users are assigned to workspaces independently of their tenant-level role. A user can have different roles in different workspaces:

| Role | Scope | Can do |
|------|-------|--------|
| **Workspace Administrator** | Workspace | Configure workspace settings; manage membership; set approval chain |
| **ESG Analyst** | Workspace | Create and edit business cases; populate assumptions; run model |
| **Finance Challenger** | Workspace | Perform independent validation; challenge assumptions; sign off |
| **Legal Reviewer** | Workspace | Review regulatory obligation mapping; sign off disclosure readiness |
| **Sponsor** | Workspace | Issue sponsor recommendations; view all business cases |
| **Viewer** | Workspace | Read-only access to approved business cases and dashboards |

**Tenant Administrator** has implicit read access to all workspaces in the tenant but operates no business cases directly. They manage tenant-level configuration only.

### 8.4 Workspace isolation enforcement

Isolation is enforced at three independent layers — all three must be satisfied for any data access:

**Layer 1 — Database (row-level security)**  
Every table has both `tenant_id` and `workspace_id` columns. PostgreSQL RLS policies enforce:  
```sql
USING (tenant_id = current_setting('app.current_tenant')::uuid
   AND workspace_id = current_setting('app.current_workspace')::uuid)
```
Group Reporting workspaces use an explicit allow-list policy:  
```sql
USING (tenant_id = current_setting('app.current_tenant')::uuid
   AND workspace_id = ANY(current_setting('app.permitted_workspaces')::uuid[]))
```

**Layer 2 — API gateway**  
Every request carries a JWT that includes `tenant_id` and `workspace_id` claims. The API gateway validates both claims against the authenticated user's membership record before routing. Requests with mismatched claims are rejected with `403 Forbidden`.

**Layer 3 — Application logic**  
All repository queries include explicit `tenant_id` and `workspace_id` filters even though RLS enforces this at the database layer. Defence in depth — the application layer never trusts the database layer alone.

### 8.5 Cross-workspace data flow rules

| Operation | Allowed? | Mechanism |
|-----------|----------|-----------|
| Copy a business case template from workspace A to workspace B | Yes, within tenant | Explicit copy operation; creates a new independent record; no live link |
| View a business case in another workspace | No | Blocked at all three isolation layers |
| Share an assumption benchmark between workspaces | Yes, via tenant-level benchmark | Analyst uploads benchmark to tenant level; both workspaces read from tenant |
| Cross-workspace PIR comparison | Yes, in Group Reporting workspace only | Aggregated read-only view; no individual record access |
| Share a sponsor recommendation across workspaces | No | Recommendation belongs to the workspace; cross-workspace sharing requires Group Reporting |

### 8.6 Workspace approval chain

Each workspace configures its own approval chain independently. The chain defines:

- Who can perform self-validation (ESG Analyst or above)
- Who can perform independent validation (Finance Challenger or Legal Reviewer — must be different from the model builder)
- Who can issue the sponsor recommendation (Sponsor role; must be different from validator)
- Whether dual-approval is required (two independent validators before recommendation)
- Escalation path if a validator is unavailable

The approval chain is stored as a workspace configuration record, not hardcoded. Tenant Administrators set default chains; Workspace Administrators can customise within the tenant defaults.

---

## 9. Context-resolution engine

### 9.1 Resolution sequence — v3.0 (enhanced)

The context-resolution engine now resolves from three configuration layers. Each step explicitly records which layer provided the value.

```
STEP 1: ESG Component selection
├── Input: esg_component_id
├── Resolves: risk_subject_type, epis_profile, role_pool_prefix, 
│             regulatory_framework_types, physical_unit_tracking
└── Source: Platform ESG Reference → Tenant override → Workspace override

STEP 2: ESG Subcomponent + Materiality
├── Input: esg_subcomponent_id
├── Resolves: primary_metric, unit, secondary_metrics[], 
│             iro_classification, impact_direction, alpha/beta/gamma
└── Source: Platform materiality → Tenant materiality override

STEP 3: Business Size profile
├── Input: business_size_profile_id
├── Resolves: duration_months, workstream_effort_weights[], 
│             benefit_realisation_months, sensitive_range
└── Source: Platform profiles → Tenant custom profiles

STEP 4: Industry resolution
├── Input: industry_group, sub_industry
│         (from workspace default or analyst selection)
├── Resolves: assumption_benchmark_set, regulatory_applicability_flags,
│             sector_risk_weights
└── Source: Tenant industry profile → Platform GICS taxonomy

STEP 5: Role pool resolution
├── Input: esg_component prefix
├── Resolves: global_roles (GLS, always included) + 
│             initiative_specific_roles (by prefix) +
│             tenant_custom_roles
└── Source: Platform roles + Tenant custom roles

STEP 6: CoA resolution
├── Input: tenant_id + workspace_id
├── Resolves: available GL codes for cost and benefit line selectors
└── Source: Tenant CoA → filtered by workspace CoA scope

STEP 7: Regulatory framework resolution
├── Input: esg_component + jurisdictions (from workspace settings)
├── Resolves: applicable_frameworks[], pre-populated obligation rows
└── Source: Platform frameworks → Tenant framework scope 
            → Workspace regulatory scope filter

RESOLUTION LOG: Every step writes a CONTEXT_RESOLUTION_LOG record:
  (initiative_id, step_number, input_key, resolved_value, 
   source_level [platform/tenant/workspace], resolved_at)
```

### 9.2 Override handling

Any context-resolved value can be overridden by the analyst. Every override is:

1. Recorded in `CONTEXT_OVERRIDE_LOG` with the original resolved value, override value, reason, user and timestamp
2. Displayed inline on the relevant field with an orange override indicator
3. Included in the Confidence Assessment sheet as a flagged item requiring reviewer awareness
4. Surfaced in the sponsor recommendation as an override count (e.g. "3 context overrides applied — see Confidence Assessment")

---

## 10. Functional requirements

### 10.1 Configuration management

| ID | Requirement | Priority | Acceptance criteria |
|----|-------------|----------|---------------------|
| FR-C01 | Tenant Administrators must be able to override any platform materiality definition field (primary metric, unit, secondary metrics, EPIS weights) with a mandatory rationale. Overrides are versioned by effective date. | Must | Override saves with rationale. Previous value preserved in override history. Effective from date respected — initiatives created before the date use the previous value. |
| FR-C02 | Tenant Administrators must be able to upload, update and deactivate GL codes in the tenant Chart of Accounts. All business cases within the tenant reference only this CoA. | Must | CSV upload with validation. Duplicate GL codes rejected. Deactivated codes flagged on existing business cases. No cross-tenant CoA visibility at any layer. |
| FR-C03 | Workspace Administrators must be able to restrict the tenant CoA to a subset of GL codes visible within the workspace, filtered by cost_centre or department. | Must | Workspace CoA scope filter applied to all GL code selectors in the workspace. Tenant CoA unchanged. Filtering is additive restriction only — workspace cannot see codes outside the tenant CoA. |
| FR-C04 | Tenant Administrators must be able to create private initiative templates not in the platform library. Private templates appear in the initiative selector alongside platform templates. | Must | Private templates flagged as [Tenant] in the selector. Visible only within the tenant. Support all fields available in platform templates including workstreams, assumption hints, value creation text. |
| FR-C05 | Tenant Administrators must be able to disable platform initiative templates not relevant to the tenant. Disabled templates are hidden from all workspace selectors within the tenant. | Must | Disable toggle in admin UI. Disabled templates immediately hidden from all workspace selectors. Re-enabling restores visibility. Cannot disable a template already used in an active business case. |
| FR-C06 | ESG component display names must be overridable at tenant level for localisation. Underlying prefix codes and system behaviour are unchanged by the display name change. | Should | Display name override stored at tenant level. All UI, exports and reports use the overridden name. API responses return both system_name and display_name. Prefix code unchanged. |
| FR-C07 | Platform-level ESG taxonomy, regulatory frameworks, business size profiles and EPIS default profiles must be updatable by platform administrators without requiring code deployment. | Must | All taxonomy data stored in configurable tables, not code. Platform admin UI for all updates. Changes versioned by effective date. Zero-downtime updates. |

### 10.2 Multi-tenancy and workspace

| ID | Requirement | Priority | Acceptance criteria |
|----|-------------|----------|---------------------|
| FR-W01 | All business case data, assumption data, validation records and recommendations must be isolated at the workspace level. No data is accessible across workspace boundaries except via an explicitly configured Group Reporting workspace. | Must | Penetration test confirms no cross-workspace data access via API, URL manipulation, or database query. RLS policy tested quarterly. |
| FR-W02 | A user must be able to hold different roles in different workspaces within the same tenant. Role assignments are per-workspace, not global. | Must | User with Viewer in workspace A and ESG Analyst in workspace B can create business cases in B but only read in A. JWT workspace claim validated on every request. |
| FR-W03 | Group Reporting workspace must provide read-only aggregated views across permitted standard workspaces. No individual business case record is accessible from a Group Reporting workspace — only aggregated metrics. | Must | Group Reporting workspace cannot access individual initiative detail. Aggregation queries use pre-computed summary tables updated nightly. |
| FR-W04 | Workspace creation, configuration, membership management and approval chain setup must be self-service for Tenant Administrators and Workspace Administrators without requiring platform support. | Must | Workspace creation completes in < 2 minutes via admin UI. All configuration options accessible without raising a support ticket. |
| FR-W05 | Sandbox workspaces must be clearly labelled throughout the UI and in all exports. Sandbox data must never flow into Group Reporting aggregations or disclosure readiness calculations. | Must | Every screen in a Sandbox workspace shows a persistent "SANDBOX — DATA NOT USED IN REPORTING" banner. Sandbox workspace_id is excluded from all Group Reporting queries by a hard filter. |
| FR-W06 | Tenant provisioning must be automated. A new tenant must be fully operational (with default configuration loaded, first Tenant Administrator invited, and onboarding checklist presented) within 5 minutes of account creation. | Must | Automated provisioning pipeline: tenant record created → default platform config applied → Tenant Admin invitation email sent → onboarding checklist generated. End-to-end tested in CI. |

### 10.3 CoA and financial model

| ID | Requirement | Priority | Acceptance criteria |
|----|-------------|----------|---------------------|
| FR-F01 | Every cost and benefit line in a business case must reference a GL code from the tenant's CoA. Lines with no GL code are blocked from being used in financial model calculations. | Must | GL code field is mandatory. Save blocked if GL code is blank or references a deactivated code. Validation error message identifies the specific unresolved line. |
| FR-F02 | Cost lines must be classified as OPEX or CAPEX. This classification must match the account_type field in the tenant CoA where that field is populated. | Must | account_type from CoA pre-populates OPEX/CAPEX classification. Analyst can override with documented reason. Mismatch between CoA account_type and analyst classification flagged in validation. |
| FR-F03 | All financial values in a workspace must be stored and displayed in the workspace reporting currency. Currency conversion to tenant base currency must be applied for Group Reporting aggregations. | Must | Currency conversion uses ECB daily rates (or configurable FX provider). Conversion rate and date stored alongside every cross-currency value. Group Reporting shows both workspace currency and tenant base currency. |

---

## 11. User stories

### 11.1 Tenant configuration

| ID | Role | I want to | So that | Acceptance criteria |
|----|------|-----------|---------|---------------------|
| US-T01 | Tenant Administrator | upload my company's Chart of Accounts as a CSV and have it immediately available in all workspace GL code selectors | I don't need to manually enter hundreds of GL codes | CSV validated and loaded within 30 seconds for files up to 10,000 rows. Validation report shows accepted, rejected and duplicate rows. GL codes available in selectors within 60 seconds of successful upload. |
| US-T02 | Tenant Administrator | override the primary metric for the Modern Slavery subcomponent from "Suppliers audited %" to "Tier-1 and Tier-2 suppliers audited %" to match our group reporting definition | our business cases use consistent language aligned to our sustainability report | Override saved with rationale. All new business cases in the tenant use the overridden metric label. Existing business cases unaffected. Override visible in materiality configuration screen with override indicator. |
| US-T03 | Tenant Administrator | create a private initiative template for our company's Net Zero Transition Plan that doesn't exist in the platform library | our analysts can build a business case from a consistent internal template | Private template appears in initiative selector flagged as [Acme Corp]. Supports all platform template fields. Not visible to any other tenant. |
| US-T04 | Tenant Administrator | disable all Biodiversity initiative templates for our tenant | our analysts in a software company aren't distracted by irrelevant initiative types | Disabled templates immediately hidden from all workspace selectors. Disable logged with timestamp. Re-enable restores visibility. |

### 11.2 Workspace management

| ID | Role | I want to | So that | Acceptance criteria |
|----|------|-----------|---------|---------------------|
| US-W01 | Workspace Administrator | restrict the visible GL codes in our UK workspace to codes with a UK cost centre tag | our UK analysts only see UK-relevant GL codes and can't accidentally map costs to Australian cost centres | Workspace CoA scope filter applied. GL code selector in UK workspace shows only UK-tagged codes. Tenant CoA unchanged. |
| US-W02 | Workspace Administrator | set the approval chain for my workspace so that any business case over $500k project cost requires dual independent validation | high-value decisions have stronger governance than routine ones | Approval chain configurable per workspace. Threshold-based rule: if total_project_cost > configured_threshold, two independent validators required. Recommendation blocked until both validations complete. |
| US-W03 | ESG Analyst | create a Sandbox copy of a business case I'm working on to test different scenario assumptions without affecting the approved version | I can model freely without risk of changing the live business case | Sandbox copy creates an independent record in the Sandbox workspace. Changes to the copy do not affect the original. Sandbox banner displayed throughout. Copy-back to standard workspace requires Workspace Administrator approval. |
| US-W04 | Executive Sponsor | see a consolidated portfolio view across all workspaces I have access to, showing total NPV, EPIS uplift and disclosure readiness by ESG component | I can report to the Board on our ESG program performance without running separate reports for each workspace | Group Reporting workspace shows pre-computed aggregated metrics. Filterable by ESG component, workspace, and date range. Refreshed nightly. Drill-down to workspace level (not individual business case) available. |

### 11.3 Materiality configuration

| ID | Role | I want to | So that | Acceptance criteria |
|----|------|-----------|---------|---------------------|
| US-M01 | ESG Practice Lead | increase the compliance (γ) weight for Energy Management initiatives from 0.25 to 0.40 because our NGER Act reporting obligation is the dominant driver | the EPIS score for our energy initiatives reflects our regulatory reality, not a generic default | Tenant materiality override saved for EWE Energy Management subcomponent. New initiatives use γ=0.40. Existing initiatives unaffected. Override shown with source label "Tenant config" in EPIS score breakdown. |
| US-M02 | ESG Practice Lead | see a side-by-side comparison of platform default materiality values and our tenant overrides for all subcomponents | I can audit what we've customised and why | Materiality configuration screen shows all 15 components with subcomponents. Platform value and tenant override shown in adjacent columns. Overridden rows highlighted. Override rationale and date visible on hover. Export to CSV supported. |

---

## 12. API design

### 12.1 Configuration management endpoints

| Method | Endpoint | Auth | Description | Notes |
|--------|----------|------|-------------|-------|
| `GET` | `/tenants/{tid}/config/materiality` | Tenant Admin | Get all materiality definitions with tenant overrides | Returns platform values and tenant override values side-by-side. `source` field = platform / tenant for each field. |
| `PUT` | `/tenants/{tid}/config/materiality/{subcomponent_id}` | Tenant Admin | Create or update tenant materiality override | Body: any subset of overridable fields + `override_rationale` (required). Validates α+β+γ=1.0 if any weight is changed. |
| `DELETE` | `/tenants/{tid}/config/materiality/{subcomponent_id}` | Tenant Admin | Remove tenant materiality override, reverting to platform default | Creates an override history record with action=reset. Existing business cases unaffected. |
| `GET` | `/tenants/{tid}/coa` | Tenant Admin, ESG Analyst | Get tenant Chart of Accounts | Filterable by account_type, is_active. Workspace-scoped requests filtered by workspace CoA scope. |
| `POST` | `/tenants/{tid}/coa/upload` | Tenant Admin | Bulk upload CoA from CSV | Multipart form. Returns validation report before commit. Requires explicit `?confirm=true` to commit. |
| `PATCH` | `/tenants/{tid}/coa/{gl_code}` | Tenant Admin | Update a single GL code record | Supports: description, account_type, cost_centre, department, is_active. |
| `GET` | `/tenants/{tid}/config/esg-taxonomy` | Any user | Get the effective ESG taxonomy for this tenant | Returns platform taxonomy with tenant display_name overrides and disabled_template flags applied. |
| `PUT` | `/tenants/{tid}/config/esg-taxonomy/{component_id}` | Tenant Admin | Override ESG component display name or disable templates | Body: display_name_override, disabled_template_ids[]. |
| `GET` | `/tenants/{tid}/config/industry-profile` | Any user | Get tenant industry profile | Returns primary_industry_group, primary_sub_industry, secondary_industries[], assumption_benchmark_source. |
| `PUT` | `/tenants/{tid}/config/industry-profile` | Tenant Admin | Set or update tenant industry profile | Body: primary_industry_group, primary_sub_industry, secondary_industries[]. Triggers assumption benchmark recalculation for all workspaces. |

### 12.2 Workspace management endpoints

| Method | Endpoint | Auth | Description | Notes |
|--------|----------|------|-------------|-------|
| `GET` | `/tenants/{tid}/workspaces` | Tenant Admin | List all workspaces in the tenant | Returns workspace metadata only; no business case data. |
| `POST` | `/tenants/{tid}/workspaces` | Tenant Admin | Create a new workspace | Body: name, type, industry_group_override, discount_rate_override, regulatory_scope_ids[], coa_scope_filter. |
| `GET` | `/workspaces/{wid}` | Workspace member | Get workspace configuration | Returns effective config (tenant defaults + workspace overrides). |
| `PUT` | `/workspaces/{wid}/config` | Workspace Admin | Update workspace configuration | Body: any workspace-level config fields. |
| `GET` | `/workspaces/{wid}/members` | Workspace Admin | List workspace members and roles | |
| `POST` | `/workspaces/{wid}/members` | Workspace Admin | Add a user to the workspace with a role | Body: user_id, role. User must be a member of the tenant. |
| `DELETE` | `/workspaces/{wid}/members/{user_id}` | Workspace Admin | Remove a user from the workspace | Does not remove tenant membership. |
| `GET` | `/workspaces/{wid}/coa-scope` | Workspace Admin | Get the CoA scope filter for this workspace | Returns filter definition and resulting GL code count. |
| `PUT` | `/workspaces/{wid}/coa-scope` | Workspace Admin | Set the CoA scope filter | Body: filter_type (cost_centre / department / none), filter_values[]. |
| `POST` | `/workspaces/{wid}/approval-chain` | Workspace Admin | Configure the approval chain for this workspace | Body: self_validator_roles[], independent_validator_roles[], sponsor_roles[], dual_approval_threshold, escalation_path. |
| `POST` | `/workspaces/{wid}/sandbox` | Workspace Admin | Create a Sandbox copy of this workspace | Returns new sandbox workspace_id. Copies configuration; no business case data. |

### 12.3 Context-resolution endpoints (v3.0 additions)

| Method | Endpoint | Auth | Description | Notes |
|--------|----------|------|-------------|-------|
| `POST` | `/workspaces/{wid}/initiatives/resolve-context` | ESG Analyst | Preview context resolution before creating an initiative | Body: template_ref_code, business_size_profile_id, industry_group, sub_industry. Returns 7-step resolution preview. 200 OK, not 201. |
| `POST` | `/workspaces/{wid}/initiatives/from-library` | ESG Analyst | Create initiative with full context resolution | Body: template_ref_code, business_size_profile_id, industry_group, sub_industry. All context applied from tenant+workspace config. |
| `GET` | `/workspaces/{wid}/initiatives/{id}/context` | Any member | Get resolved context for an initiative including source level | Returns each resolved field with source (platform/tenant/workspace/analyst-override). |
| `GET` | `/workspaces/{wid}/initiatives/{id}/context/overrides` | Finance Challenger | Get all analyst overrides of context-resolved values | Used in validation and Confidence Assessment. |

---

## 13. Data model

### 13.1 Tenancy and workspace tables

#### TENANT

| Column | Type | Key | Description |
|--------|------|-----|-------------|
| `tenant_id` | uuid | PK | Unique tenant identifier |
| `tenant_name` | varchar(255) | | Legal entity name |
| `subdomain` | varchar(100) | UNIQUE | SaaS subdomain (e.g. acmecorp.esgengine.com) |
| `plan_tier` | varchar(50) | | Starter / Professional / Enterprise |
| `reporting_currency` | char(3) | | ISO currency code (e.g. AUD, GBP, EUR) |
| `default_discount_rate` | decimal(5,4) | | Default NPV discount rate |
| `data_residency_region` | varchar(50) | | AWS region for data storage |
| `encryption_key_arn` | varchar(500) | | AWS KMS key ARN for tenant data |
| `is_active` | boolean | | Soft-delete flag |
| `provisioned_at` | timestamp | | Tenant creation timestamp |
| `onboarding_completed_at` | timestamp | | Null until onboarding checklist complete |

#### WORKSPACE

| Column | Type | Key | Description |
|--------|------|-----|-------------|
| `workspace_id` | uuid | PK | Unique workspace identifier |
| `tenant_id` | uuid | FK | Reference to TENANT |
| `workspace_name` | varchar(255) | | Display name |
| `workspace_type` | varchar(50) | | standard / read-only / group-reporting / sandbox |
| `parent_workspace_id` | uuid | FK nullable | For nested workspaces |
| `industry_group_override` | varchar(100) | | Null = inherit from tenant |
| `sub_industry_override` | varchar(100) | | Null = inherit from tenant |
| `discount_rate_override` | decimal(5,4) | | Null = inherit from tenant |
| `currency_override` | char(3) | | Null = inherit from tenant |
| `regulatory_scope_ids` | uuid[] | | Subset of tenant regulatory frameworks |
| `coa_scope_filter_type` | varchar(50) | | none / cost_centre / department |
| `coa_scope_filter_values` | text[] | | Filter values for CoA scope |
| `epis_band_override_json` | jsonb | | Null = inherit from tenant |
| `data_retention_days` | int | | Within tenant policy bounds |
| `is_active` | boolean | | Soft-delete flag |
| `created_at` | timestamp | | |

#### WORKSPACE_MEMBER

| Column | Type | Key | Description |
|--------|------|-----|-------------|
| `member_id` | uuid | PK | |
| `workspace_id` | uuid | FK | |
| `tenant_id` | uuid | FK | Denormalised for RLS |
| `user_id` | uuid | FK | Reference to USER |
| `role` | varchar(50) | | workspace-admin / esg-analyst / finance-challenger / legal-reviewer / sponsor / viewer |
| `assigned_at` | timestamp | | |
| `assigned_by` | uuid | FK | User who made the assignment |

#### TENANT_MATERIALITY_OVERRIDE

| Column | Type | Key | Description |
|--------|------|-----|-------------|
| `override_id` | uuid | PK | |
| `tenant_id` | uuid | FK | |
| `esg_subcomponent_id` | uuid | FK | References platform MATERIALITY_DEFINITION |
| `primary_metric_override` | varchar(255) | | Null = use platform value |
| `primary_metric_unit_override` | varchar(50) | | Null = use platform value |
| `secondary_metric_1_override` | varchar(255) | | Null = use platform value |
| `secondary_metric_2_override` | varchar(255) | | Null = use platform value |
| `iro_classification_override` | varchar(20) | | Null = use platform value |
| `epis_alpha_override` | decimal(4,3) | | Null = use platform value |
| `epis_beta_override` | decimal(4,3) | | Null = use platform value |
| `epis_gamma_override` | decimal(4,3) | | Null = use platform value |
| `override_rationale` | text | | Required if any field is overridden |
| `overridden_by` | uuid | FK | Tenant Admin user_id |
| `effective_from` | date | | |
| `superseded_at` | timestamp | | Null if current override |

#### TENANT_COA (Chart of Accounts — tenant-isolated)

| Column | Type | Key | Description |
|--------|------|-----|-------------|
| `coa_id` | uuid | PK | |
| `tenant_id` | uuid | FK | Row-level security key — never null |
| `gl_code` | varchar(20) | | Client's GL code |
| `gl_description` | varchar(255) | | Account description |
| `account_type` | varchar(50) | | Revenue / COGS / Labour / OPEX / CAPEX / Provision |
| `cost_centre` | varchar(50) | | For workspace CoA scope filtering |
| `department` | varchar(100) | | For workspace CoA scope filtering |
| `is_active` | boolean | | Soft-delete; deactivated codes flagged on existing business cases |
| `uploaded_at` | timestamp | | |
| `uploaded_by` | uuid | FK | |

> **RLS policy:** `USING (tenant_id = current_setting('app.current_tenant')::uuid)` — enforced at PostgreSQL level. Application layer also filters on tenant_id. No exceptions.

### 13.2 Workspace-scoped business case tables

All business case tables (ESG_INITIATIVE, ASSUMPTION, BENEFIT_LINE, CASHFLOW_ENTRY, EPIS_SCORE, VALIDATION_CHECK, SPONSOR_RECOMMENDATION, etc.) add two columns:

| Column | Type | Key | Description |
|--------|------|-----|-------------|
| `tenant_id` | uuid | FK | Denormalised for RLS — never null |
| `workspace_id` | uuid | FK | Workspace owner — never null |

**RLS policy on all business case tables:**
```sql
USING (
  tenant_id = current_setting('app.current_tenant')::uuid
  AND workspace_id = ANY(
    current_setting('app.permitted_workspaces')::uuid[]
  )
)
```

The `permitted_workspaces` session variable contains the single workspace for standard requests, or the list of permitted workspaces for Group Reporting workspace requests.

### 13.3 Constraints

```sql
-- EPIS weights must sum to 1.0 (platform and override tables)
ALTER TABLE TENANT_MATERIALITY_OVERRIDE
  ADD CONSTRAINT check_epis_weights
  CHECK (
    epis_alpha_override IS NULL OR epis_beta_override IS NULL OR epis_gamma_override IS NULL
    OR ABS(epis_alpha_override + epis_beta_override + epis_gamma_override - 1.0) < 0.001
  );

-- CoA GL codes must be unique within a tenant
ALTER TABLE TENANT_COA
  ADD CONSTRAINT unique_gl_per_tenant UNIQUE (tenant_id, gl_code);

-- Workspace data_retention_days must not exceed tenant policy
-- (enforced at application layer; tenant policy stored in TENANT.max_retention_days)

-- Workspace members must be tenant members
-- (enforced at application layer via FK through USER_TENANT_MEMBERSHIP)

-- harm_monetised_flag always false (from v1.0)
ALTER TABLE REMEDIATION_RECORD
  ADD CONSTRAINT no_harm_monetised CHECK (harm_monetised_flag = false);
```

---

## 14. Non-functional requirements

### 14.1 Performance

| Requirement | Target | Notes |
|-------------|--------|-------|
| Initiative library search | < 500ms (p95) | Full-text search across 566 templates with relevance scoring |
| Context resolution (7 steps) | < 3 seconds (p95) | All steps in a single transaction; cached by (template × profile × industry) hash |
| Cashflow recalculation | < 2 seconds (p95) | Financial engine stateless; cached by assumption hash |
| Workspace dashboard load | < 2 seconds (p95) | Pre-computed nightly aggregations; real-time on demand for < 50 initiatives |
| CoA upload (10,000 rows) | < 30 seconds | Validated and committed in a single transaction |
| Group Reporting aggregation | < 5 seconds (p95) | Pre-computed nightly; on-demand refresh available |
| Tenant provisioning | < 5 minutes end-to-end | Automated pipeline; zero manual steps |

### 14.2 Scalability

| Requirement | Target |
|-------------|--------|
| Tenants | 10,000 tenants per deployment |
| Workspaces per tenant | No hard limit; tested to 500 |
| Business cases per workspace | No hard limit; tested to 10,000 |
| CoA rows per tenant | Up to 100,000 GL codes |
| Concurrent users (platform-wide) | 5,000 concurrent |
| Concurrent users (per tenant) | 500 concurrent |

### 14.3 Availability and resilience

| Requirement | Target |
|-------------|--------|
| Uptime SLA | 99.9% monthly (excluding planned maintenance) |
| RTO | < 2 hours |
| RPO | < 15 minutes |
| Planned maintenance window | Sunday 02:00–04:00 local time (configurable per data residency region) |
| Tenant data backup | Continuous WAL shipping + daily snapshot; 35-day retention |

### 14.4 Security

| Requirement | Standard |
|-------------|----------|
| Authentication | OAuth 2.0 / OIDC; MFA mandatory |
| Authorisation | RBAC at API layer + RLS at database layer |
| Encryption at rest | AES-256 (AWS RDS); per-tenant KMS key |
| Encryption in transit | TLS 1.3 minimum |
| Tenant key isolation | Each tenant has a unique AWS KMS CMK; key rotation annual |
| Penetration testing | Annual; results published to Enterprise plan tenants |
| SOC 2 Type II | Annual audit; report available under NDA |
| Data residency | AP-SOUTHEAST-2 (AUS), EU-WEST-1 (EU), EU-WEST-2 (UK) by default; custom region available on Enterprise plan |

---

## 15. Security and access control

### 15.1 Role hierarchy

```
Platform Administrator  (ESG Custodian staff only)
    │  — manages platform-level configuration, tenant provisioning
    │
Tenant Administrator    (per tenant)
    │  — manages tenant config (CoA, materiality overrides, workspaces)
    │
Workspace Administrator (per workspace)
    │  — manages workspace config, membership, approval chain
    │
ESG Analyst             — creates and edits business cases
Finance Challenger       — independent validation only
Legal Reviewer          — regulatory mapping validation only
Sponsor                 — issues recommendations
Viewer                  — read-only, approved business cases only
```

### 15.2 Cross-workspace access rules

| Action | Who can do it | Mechanism |
|--------|--------------|-----------|
| Read any workspace in tenant | Tenant Administrator | Explicit read permission; no business case editing |
| Copy template from workspace A to B | Workspace Admin (both workspaces) | Explicit copy API; creates independent record |
| Aggregate data across workspaces | Group Reporting workspace Viewer | Pre-computed aggregations only; no record-level access |
| Override materiality for all workspaces | Tenant Administrator | Tenant-level override in config; not workspace-level |
| Apply workspace-specific discount rate | Workspace Administrator | Workspace config override; does not affect other workspaces |

### 15.3 Audit log

All actions at configuration level and business case level are recorded in an **immutable, append-only audit log** scoped to the tenant and workspace:

- Tenant-level events: CoA upload, materiality override, template creation/disabling, workspace creation
- Workspace-level events: membership changes, approval chain changes, all business case changes
- Audit log is exportable by Tenant Administrators (tenant scope) and Workspace Administrators (workspace scope)
- Retained for 7 years; partitioned monthly for query performance
- No UPDATE or DELETE on audit log by any role including Platform Administrator

---

## 16. Phased delivery roadmap

### Phase 0 — Foundation and tenancy (Weeks 1–8)
**Scope:** Multi-tenant database schema with RLS at both tenant and workspace level. Tenant provisioning automation. Workspace CRUD. User and membership management. OIDC authentication with tenant and workspace JWT claims. Platform ESG taxonomy loaded (15 components, all subcomponents, dropdown data). Platform materiality definitions loaded. GICS taxonomy loaded.  
**Exit criteria:** New tenant provisionable in < 5 minutes. Workspace isolation verified by penetration test. RLS policies pass all cross-tenant and cross-workspace access tests.

### Phase 1 — Configuration management (Weeks 9–16)
**Scope:** Tenant materiality override UI and API. Tenant CoA upload, management and workspace scope filtering. ESG component display name override. Initiative template disable/enable. Tenant industry profile setup. Private initiative template creation. Configuration audit log.  
**Exit criteria:** Tenant Administrator can configure all tenant-level settings without engineering support. CoA upload processes 10,000 rows in < 30 seconds. Materiality override resolves correctly at runtime.

### Phase 2 — Context-resolution engine v3.0 (Weeks 17–24)
**Scope:** Seven-step context resolution with three-layer source awareness. Context resolution log. Context override log and UI indicator. Initiative library search with relevance scoring. Assumption benchmark cascade (tenant → platform by industry → cross-industry). Role pool resolver. Regulatory framework resolver with workspace scope filter.  
**Exit criteria:** Any of 566 platform templates resolves context correctly for any tenant configuration in < 3 seconds. All seven resolution steps logged with source level. Analyst override captures correctly.

### Phase 3 — Financial model (inherited from v1.0/v2.0, workspace-scoped) (Weeks 25–32)
**Scope:** All v1.0 financial model features (GL-mapped costs, 60-month cashflow, NPV/IRR/ROI, three scenarios, attribution formula) running within workspace isolation. CoA selector scoped to workspace. EPIS scoring using tenant/workspace-resolved weights. Physical impact records for Environmental initiatives.  
**Exit criteria:** Financial model produces results matching reference Excel model. All GL mappings reference tenant CoA. EPIS weights use resolved (not hardcoded) values.

### Phase 4 — Governance and output (workspace-scoped) (Weeks 33–40)
**Scope:** Two-stage validation within workspace approval chain. Regulatory obligation mapping using resolved regulatory frameworks. Remediation record management. KPI measurement plan with auto-populated metrics from materiality. Sponsor recommendation with version control. Workspace-scoped audit log.  
**Exit criteria:** Approval chain correctly enforces workspace configuration. Regulatory mapping uses workspace-resolved frameworks. Recommendation version history preserved.

### Phase 5 — Group Reporting workspace (Weeks 41–46)
**Scope:** Group Reporting workspace type. Cross-workspace aggregation queries (nightly batch). Aggregated EPIS dashboard. Portfolio NPV and benefit tracking. Consolidated disclosure readiness by framework. No individual business case access from Group Reporting.  
**Exit criteria:** Group Reporting workspace shows aggregated metrics for all permitted workspaces. No individual business case data accessible. Nightly batch completes in < 30 minutes for 1,000 business cases.

### Phase 6 — Post-implementation review and reference library (Weeks 47–52)
**Scope:** PIR record capture with actuals vs modelled variance. Assumption benchmark update from PIR actuals (tenant-level). Reference library feedback loop. Revalidation trigger evaluation.  
**Exit criteria:** PIR records actuals and calculates variance. Tenant Administrator can promote PIR actuals to tenant assumption benchmarks. Reference library shows when each benchmark was last updated by PIR data.

### Phase 7 — Hardening and launch (Weeks 53–60)
**Scope:** Full penetration test (cross-tenant, cross-workspace). Performance testing at scale (10,000 tenants, 500 concurrent users). Accessibility audit (WCAG 2.1 AA). Disaster recovery test. SOC 2 readiness assessment. Documentation. Tenant onboarding guide.  
**Exit criteria:** All NFRs met. Pen test findings remediated. SOC 2 readiness confirmed. Platform Administrator and Tenant Administrator documentation complete.

---

## 17. Open questions

| # | Question | Owner | Impact if unresolved |
|---|----------|-------|----------------------|
| Q1 | Should the platform support white-labelling for Enterprise tenants (custom domain, logo, colour scheme)? | Product / Engineering | Adds 4–6 weeks to Phase 0 if required for launch. Affects CDN configuration and frontend build process. |
| Q2 | What is the onboarding model for the platform-level ESG taxonomy updates (new regulatory frameworks, updated GICS taxonomy)? Who reviews and approves changes, and how are tenants notified? | ESG Practice / Product | Affects change management process and notification system design in Phase 1. |
| Q3 | Should materiality overrides at the tenant level propagate down to existing business cases (retroactive), or only apply to new initiatives created after the effective date? | ESG Practice / Legal | Retroactive application requires recalculation of EPIS scores and KPI targets for all existing business cases — significant compute and audit trail implications. |
| Q4 | Is workspace nesting (workspace within workspace) required for v3.0, or is a flat workspace structure sufficient? | Product / Key customers | Nested workspaces (e.g. Region → Country → Program) add complexity to RLS policy and Group Reporting aggregation. Flat structure covers most use cases. |
| Q5 | Should the tenant CoA support account hierarchies (parent/child GL codes) or is a flat code list sufficient? | Finance / Key customers | Hierarchical CoA enables rollup reporting by account group. Flat list is simpler but may not match clients' ERP structure when ERP integration is built in v2.1. |
| Q6 | What happens to workspace data when a workspace is deleted? Soft-delete with retention period, or immediate hard-delete on Tenant Administrator instruction? | Legal / Compliance | Affects data retention architecture, audit log scope, and regulatory obligations for ESG disclosure evidence. Recommend: soft-delete with 7-year retention minimum. |
| Q7 | Should the Sandbox workspace type support an explicit "promote to production" workflow, or is copying the only mechanism to move Sandbox work to a standard workspace? | Product | Promote workflow is lower friction but requires careful validation that Sandbox data doesn't silently enter production reporting. Explicit copy is safer but adds manual steps. |
| Q8 | For Group Reporting workspace, should cross-currency aggregation use a fixed FX rate (e.g. year-start rate) or daily rates? | Finance / Legal | Fixed rate simplifies year-to-year comparisons. Daily rates are more accurate but create volatility in portfolio NPV figures. |

---

*End of ESG Business Case Engine Application Specification v3.0*
