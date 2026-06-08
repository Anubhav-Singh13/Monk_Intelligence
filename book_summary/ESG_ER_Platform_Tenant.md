# ESG Business Case Engine — ER Diagram: Platform & Tenant Layers

Paste the code block below directly into eraser.io → New Diagram → Entity Relationship.

Two layers, each broken into logical sub-groups:
- **Platform** (indigo) — ESG Taxonomy · Initiative Templates · Industry Classification · Reference Data
- **Tenant** (sky) — Company Profile · Materiality & EPIS · Chart of Accounts · Template Management

---

```
// =============================================================================
// PLATFORM LAYER — shared reference data, no tenant_id
// =============================================================================

// ── ESG Taxonomy ──────────────────────────────────────────────────────────────

esg_category [icon: layers, color: "#6366f1"] {
  category_id uuid pk
  category_code char
  category_name varchar
  sort_order int
}

esg_component [icon: tag, color: "#6366f1"] {
  component_id uuid pk
  category_id uuid fk
  prefix_code varchar
  system_name varchar
  physical_unit_tracking boolean
  primary_unit_default varchar
  effective_from date
}

esg_subcomponent [icon: list, color: "#6366f1"] {
  subcomponent_id uuid pk
  component_id uuid fk
  subcomponent_name varchar
  iro_classification varchar
  impact_direction varchar
  is_active boolean
  sort_order int
}

materiality_definition [icon: target, color: "#6366f1"] {
  materiality_id uuid pk
  subcomponent_id uuid fk
  primary_metric varchar
  primary_metric_unit varchar
  secondary_metric_1 varchar
  secondary_metric_2 varchar
  epis_alpha_default decimal
  epis_beta_default decimal
  epis_gamma_default decimal
}

epis_weighting_profile [icon: sliders, color: "#6366f1"] {
  profile_id uuid pk
  profile_code varchar
  component_id uuid fk
  alpha decimal
  beta decimal
  gamma decimal
  is_platform boolean
}

// ── Initiative Templates ──────────────────────────────────────────────────────

initiative_template [icon: file-text, color: "#6366f1"] {
  template_id uuid pk
  template_ref_code varchar
  component_id uuid fk
  template_name varchar
  objective_text text
  is_active boolean
  effective_from date
}

initiative_template_workstream [icon: check-square, color: "#6366f1"] {
  workstream_id uuid pk
  template_id uuid fk
  workstream_name varchar
  sort_order int
}

initiative_template_industry_tag [icon: hash, color: "#6366f1"] {
  tag_id uuid pk
  template_id uuid fk
  gics_level varchar
  gics_code varchar
}

// ── Industry Classification ───────────────────────────────────────────────────

gics_sector [icon: globe, color: "#6366f1"] {
  sector_code varchar pk
  sector_name varchar
  effective_date date
}

gics_industry_group [icon: briefcase, color: "#6366f1"] {
  industry_group_code varchar pk
  sector_code varchar fk
  industry_group_name varchar
  effective_date date
}

gics_sub_industry [icon: truck, color: "#6366f1"] {
  sub_industry_code varchar pk
  industry_group_code varchar fk
  sub_industry_name varchar
  effective_date date
}

// ── Reference Data ────────────────────────────────────────────────────────────

regulatory_framework [icon: book-open, color: "#6366f1"] {
  framework_id uuid pk
  framework_code varchar
  framework_name varchar
  jurisdiction varchar
  esg_category_id uuid fk
  is_active boolean
}

business_size_profile [icon: bar-chart-2, color: "#6366f1"] {
  profile_id uuid pk
  profile_code varchar
  profile_name varchar
  duration_months int
  benefit_realisation_months int
  sensitive_range_pct decimal
  is_active boolean
}

assumption_benchmark [icon: database, color: "#6366f1"] {
  benchmark_id uuid pk
  component_id uuid fk
  gics_level varchar
  gics_code varchar
  metric_name varchar
  benchmark_value decimal
  benchmark_unit varchar
  confidence_level varchar
  is_platform boolean
  tenant_id uuid fk
}

// =============================================================================
// TENANT LAYER — company config + workspace config (isolated by tenant_id)
// =============================================================================

// ── Company Profile ───────────────────────────────────────────────────────────

tenant [icon: building, color: "#0ea5e9"] {
  tenant_id uuid pk
  tenant_name varchar
  subdomain varchar
  plan_tier varchar
  reporting_currency char
  default_discount_rate decimal
  data_residency_region varchar
  encryption_key_arn varchar
  is_active boolean
  provisioned_at timestamptz
}

tenant_industry_profile [icon: map-pin, color: "#0ea5e9"] {
  profile_id uuid pk
  tenant_id uuid fk
  primary_industry_group_code varchar fk
  primary_sub_industry_code varchar fk
  secondary_industries jsonb
  assumption_benchmark_source varchar
}

// ── Materiality and EPIS ──────────────────────────────────────────────────────

tenant_materiality_override [icon: edit, color: "#0ea5e9"] {
  override_id uuid pk
  tenant_id uuid fk
  subcomponent_id uuid fk
  primary_metric_override varchar
  primary_metric_unit_override varchar
  secondary_metric_1_override varchar
  secondary_metric_2_override varchar
  override_rationale text
  overridden_by uuid
  effective_from date
  superseded_at timestamptz
}

tenant_epis_weight_profile [icon: sliders, color: "#0ea5e9"] {
  profile_id uuid pk
  tenant_id uuid fk
  component_id uuid fk
  alpha decimal
  beta decimal
  gamma decimal
  override_rationale text
  set_by uuid
  effective_from date
  superseded_at timestamptz
}

tenant_epis_band_config [icon: activity, color: "#0ea5e9"] {
  config_id uuid pk
  tenant_id uuid fk
  low_max decimal
  medium_max decimal
  high_max decimal
  config_rationale text
  set_by uuid
  effective_from date
  superseded_at timestamptz
}

// ── Chart of Accounts ─────────────────────────────────────────────────────────

tenant_coa [icon: dollar-sign, color: "#0ea5e9"] {
  coa_id uuid pk
  tenant_id uuid fk
  gl_code varchar
  gl_description varchar
  account_type varchar
  cost_centre varchar
  department varchar
  is_active boolean
}

coa_upload_report [icon: upload-cloud, color: "#0ea5e9"] {
  report_id uuid pk
  tenant_id uuid fk
  uploaded_by uuid
  status varchar
  total_rows int
  accepted_rows int
  rejected_rows int
  committed_at timestamptz
}

// ── Template Management ───────────────────────────────────────────────────────

tenant_component_override [icon: eye-off, color: "#0ea5e9"] {
  override_id uuid pk
  tenant_id uuid fk
  component_id uuid fk
  is_hidden boolean
  updated_at timestamptz
  updated_by uuid
}

tenant_template_override [icon: file-minus, color: "#0ea5e9"] {
  override_id uuid pk
  tenant_id uuid fk
  template_id uuid fk
  objective_override text
  custom_tags text[]
  is_disabled boolean
}

tenant_private_template [icon: file-plus, color: "#0ea5e9"] {
  template_id uuid pk
  tenant_id uuid fk
  base_platform_template_id uuid fk
  component_id uuid fk
  template_ref_code varchar
  template_name varchar
  is_active boolean
  created_by uuid
}

tenant_regulatory_scope [icon: shield, color: "#0ea5e9"] {
  scope_id uuid pk
  tenant_id uuid fk
  framework_id uuid fk
  is_active boolean
  added_at timestamptz
}

// =============================================================================
// RELATIONSHIPS
// =============================================================================

// ── Platform — ESG Taxonomy ───────────────────────────────────────────────────
esg_category.category_id < esg_component.category_id
esg_component.component_id < esg_subcomponent.component_id
esg_subcomponent.subcomponent_id - materiality_definition.subcomponent_id
esg_component.component_id < epis_weighting_profile.component_id

// ── Platform — Initiative Templates ──────────────────────────────────────────
esg_component.component_id < initiative_template.component_id
initiative_template.template_id < initiative_template_workstream.template_id
initiative_template.template_id < initiative_template_industry_tag.template_id
initiative_template_industry_tag.gics_code > gics_sub_industry.sub_industry_code

// ── Platform — Industry Classification ───────────────────────────────────────
gics_sector.sector_code < gics_industry_group.sector_code
gics_industry_group.industry_group_code < gics_sub_industry.industry_group_code

// ── Platform — Reference Data ─────────────────────────────────────────────────
esg_category.category_id < regulatory_framework.esg_category_id
esg_component.component_id < assumption_benchmark.component_id

// ── Tenant — Company Profile ──────────────────────────────────────────────────
tenant.tenant_id - tenant_industry_profile.tenant_id
tenant_industry_profile.primary_industry_group_code > gics_industry_group.industry_group_code
tenant_industry_profile.primary_sub_industry_code > gics_sub_industry.sub_industry_code

// ── Tenant — Materiality and EPIS ─────────────────────────────────────────────
tenant.tenant_id < tenant_materiality_override.tenant_id
esg_subcomponent.subcomponent_id < tenant_materiality_override.subcomponent_id
tenant.tenant_id < tenant_epis_weight_profile.tenant_id
esg_component.component_id < tenant_epis_weight_profile.component_id
tenant.tenant_id < tenant_epis_band_config.tenant_id

// ── Tenant — Chart of Accounts ────────────────────────────────────────────────
tenant.tenant_id < tenant_coa.tenant_id
tenant.tenant_id < coa_upload_report.tenant_id

// ── Tenant — Template Management ─────────────────────────────────────────────
tenant.tenant_id < tenant_component_override.tenant_id
esg_component.component_id < tenant_component_override.component_id
tenant.tenant_id < tenant_template_override.tenant_id
initiative_template.template_id < tenant_template_override.template_id
tenant.tenant_id < tenant_private_template.tenant_id
initiative_template.template_id < tenant_private_template.base_platform_template_id
esg_component.component_id < tenant_private_template.component_id
tenant.tenant_id < tenant_regulatory_scope.tenant_id
regulatory_framework.framework_id < tenant_regulatory_scope.framework_id
tenant.tenant_id < assumption_benchmark.tenant_id

// =============================================================================
// LAYER GROUPS
// =============================================================================

Platform [color: "#6366f1"] {

  ESG Taxonomy [color: "#818cf8"] {
    esg_category
    esg_component
    esg_subcomponent
    materiality_definition
    epis_weighting_profile
  }

  Initiative Templates [color: "#818cf8"] {
    initiative_template
    initiative_template_workstream
    initiative_template_industry_tag
  }

  Industry Classification [color: "#818cf8"] {
    gics_sector
    gics_industry_group
    gics_sub_industry
  }

  Reference Data [color: "#818cf8"] {
    regulatory_framework
    business_size_profile
    assumption_benchmark
  }
}

Tenant [color: "#0ea5e9"] {

  Company Profile [color: "#7dd3fc"] {
    tenant
    tenant_industry_profile
  }

  Materiality and EPIS [color: "#7dd3fc"] {
    tenant_materiality_override
    tenant_epis_weight_profile
    tenant_epis_band_config
  }

  Chart of Accounts [color: "#7dd3fc"] {
    tenant_coa
    coa_upload_report
  }

  Template Management [color: "#7dd3fc"] {
    tenant_component_override
    tenant_template_override
    tenant_private_template
    tenant_regulatory_scope
  }
}
```
