# ESG Business Case Engine — ER Diagram (eraser.io)

Paste the code block below directly into eraser.io → New Diagram → Entity Relationship.  
Three layers, each broken into logical sub-groups:
- **Platform** (indigo) — ESG Taxonomy · Initiative Templates · Industry Classification · Reference Data
- **Tenant** (sky) — Company Profile · Materiality & EPIS · Chart of Accounts · Template Management
- **Business Case** (amber) — Initiative Core · Financial Model · Scoring & Compliance · Governance · Post-Investment & Audit

---

```
// ─────────────────────────────────────────────────────────────────────────────
// PLATFORM LAYER — shared reference data, no tenant_id
// ─────────────────────────────────────────────────────────────────────────────

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

// ─────────────────────────────────────────────────────────────────────────────
// TENANT LAYER — company config (tenant_id)
// ─────────────────────────────────────────────────────────────────────────────

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

tenant_materiality_override [icon: edit, color: "#0ea5e9"] {
  override_id uuid pk
  tenant_id uuid fk
  subcomponent_id uuid fk
  primary_metric_override varchar
  primary_metric_unit_override varchar
  secondary_metric_1_override varchar
  secondary_metric_2_override varchar
  override_rationale text
  overridden_by uuid fk
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
  set_by uuid fk
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
  set_by uuid fk
  effective_from date
  superseded_at timestamptz
}

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
  uploaded_by uuid fk
  status varchar
  total_rows int
  accepted_rows int
  rejected_rows int
  committed_at timestamptz
}

tenant_component_override [icon: eye-off, color: "#0ea5e9"] {
  override_id uuid pk
  tenant_id uuid fk
  component_id uuid fk
  is_hidden boolean
  updated_at timestamptz
  updated_by uuid fk
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
  created_by uuid fk
}

tenant_regulatory_scope [icon: shield, color: "#0ea5e9"] {
  scope_id uuid pk
  tenant_id uuid fk
  framework_id uuid fk
  is_active boolean
  added_at timestamptz
}

// ─────────────────────────────────────────────────────────────────────────────
// BUSINESS CASE LAYER — transactional + post-investment + audit (tenant_id)
// ─────────────────────────────────────────────────────────────────────────────

esg_initiative [icon: zap, color: "#f59e0b"] {
  initiative_id uuid pk
  tenant_id uuid fk
  initiative_name varchar
  template_ref_code varchar
  component_id uuid fk
  subcomponent_id uuid fk
  biz_size_profile_id uuid fk
  industry_group_code varchar
  sub_industry_code varchar
  status varchar
  reporting_currency char
  discount_rate decimal
  project_start_date date
  created_by uuid fk
}

context_resolution_log [icon: search, color: "#f59e0b"] {
  log_id uuid pk
  initiative_id uuid fk
  tenant_id uuid

  step_number int
  step_name varchar
  input_key varchar
  resolved_value text
  source_level varchar
  resolved_at timestamptz
}

context_override_log [icon: alert-triangle, color: "#f59e0b"] {
  override_id uuid pk
  initiative_id uuid fk
  tenant_id uuid

  field_name varchar
  original_value text
  override_value text
  override_reason text
  overridden_by uuid fk
  overridden_at timestamptz
}

assumption [icon: edit-3, color: "#f59e0b"] {
  assumption_id uuid pk
  initiative_id uuid fk
  tenant_id uuid

  assumption_name varchar
  assumption_value decimal
  unit varchar
  benchmark_value decimal
  benchmark_source varchar
  is_analyst_override boolean
}

cost_line [icon: minus-circle, color: "#f59e0b"] {
  cost_line_id uuid pk
  initiative_id uuid fk
  tenant_id uuid

  line_name varchar
  gl_code varchar
  account_type varchar
  monthly_amounts decimal[]
}

benefit_line [icon: plus-circle, color: "#f59e0b"] {
  benefit_line_id uuid pk
  initiative_id uuid fk
  tenant_id uuid

  line_name varchar
  gl_code varchar
  benefit_type varchar
  monthly_amounts decimal[]
}

scenario [icon: git-branch, color: "#f59e0b"] {
  scenario_id uuid pk
  initiative_id uuid fk
  tenant_id uuid

  scenario_name varchar
  is_primary boolean
}

cashflow_entry [icon: trending-up, color: "#f59e0b"] {
  cashflow_id uuid pk
  scenario_id uuid fk
  initiative_id uuid fk
  tenant_id uuid

  month_number int
  total_costs decimal
  total_benefits decimal
  net_cashflow decimal
  cumulative_cashflow decimal
  discounted_cashflow decimal
  assumption_hash varchar
}

financial_summary [icon: pie-chart, color: "#f59e0b"] {
  summary_id uuid pk
  scenario_id uuid fk
  initiative_id uuid fk
  tenant_id uuid

  npv decimal
  irr decimal
  roi decimal
  payback_months int
  total_project_cost decimal
  total_benefit decimal
}

epis_score [icon: award, color: "#f59e0b"] {
  score_id uuid pk
  initiative_id uuid fk
  scenario_id uuid fk
  tenant_id uuid

  impact_score decimal
  financial_score decimal
  compliance_score decimal
  composite_score decimal
  alpha_used decimal
  beta_used decimal
  gamma_used decimal
  weight_source varchar
  band varchar
}

kpi_measurement [icon: bar-chart, color: "#f59e0b"] {
  kpi_id uuid pk
  initiative_id uuid fk
  tenant_id uuid

  primary_metric varchar
  metric_unit varchar
  target_value decimal
  measurement_frequency varchar
  metric_source varchar
}

regulatory_obligation [icon: file-text, color: "#f59e0b"] {
  obligation_id uuid pk
  initiative_id uuid fk
  framework_id uuid fk
  tenant_id uuid

  obligation_text text
  compliance_status varchar
  reviewer_id uuid fk
}

physical_impact_record [icon: globe, color: "#f59e0b"] {
  impact_id uuid pk
  initiative_id uuid fk
  tenant_id uuid

  metric_name varchar
  unit varchar
  baseline_value decimal
  target_value decimal
  reported_value decimal
}

validation_check [icon: check-circle, color: "#f59e0b"] {
  check_id uuid pk
  initiative_id uuid fk
  tenant_id uuid

  validation_type varchar
  validator_id uuid fk
  status varchar
  issues_json jsonb
  checked_at timestamptz
}

sponsor_recommendation [icon: star, color: "#f59e0b"] {
  recommendation_id uuid pk
  initiative_id uuid fk
  tenant_id uuid

  sponsor_id uuid fk
  decision varchar
  rationale text
  override_count int
  created_at timestamptz
  superseded_at timestamptz
}

remediation_record [icon: tool, color: "#f59e0b"] {
  remediation_id uuid pk
  initiative_id uuid fk
  validation_check_id uuid fk
  tenant_id uuid

  issue_description text
  resolution_notes text
  resolved_by uuid fk
  resolved_at timestamptz
}

// ── Post-investment review & audit (part of Business Case layer) ─────────────

pir_record [icon: clipboard, color: "#f59e0b"] {
  pir_id uuid pk
  initiative_id uuid fk
  tenant_id uuid

  review_period_start date
  review_period_end date
  status varchar
  created_by uuid fk
  submitted_at timestamptz
}

pir_actual_entry [icon: activity, color: "#f59e0b"] {
  entry_id uuid pk
  pir_id uuid fk
  initiative_id uuid fk
  tenant_id uuid

  metric_name varchar
  modelled_value decimal
  actual_value decimal
  variance_pct decimal
  promote_to_benchmark boolean
  promotion_status varchar
  promoted_benchmark_id uuid fk
}

portfolio_aggregation [icon: layers, color: "#f59e0b"] {
  aggregation_id uuid pk
  tenant_id uuid fk
  aggregation_date date
  initiative_count int
  portfolio_npv_base_currency decimal
  epis_by_component_json jsonb
  disclosure_readiness_json jsonb
  pir_completion_rate decimal
  computed_at timestamptz
}

audit_log [icon: clock, color: "#f59e0b"] {
  log_id uuid pk
  tenant_id uuid

  user_id uuid fk
  event_type varchar
  event_category varchar
  resource_type varchar
  resource_id uuid
  old_value_json jsonb
  new_value_json jsonb
  occurred_at timestamptz
}

// =============================================================================
// RELATIONSHIPS
// =============================================================================

// ── Layer 1 internal ──────────────────────────────────────────────────────────
esg_category.category_id < esg_component.category_id
esg_component.component_id < esg_subcomponent.component_id
esg_subcomponent.subcomponent_id - materiality_definition.subcomponent_id
esg_component.component_id < epis_weighting_profile.component_id
esg_component.component_id < initiative_template.component_id
initiative_template.template_id < initiative_template_workstream.template_id
initiative_template.template_id < initiative_template_industry_tag.template_id
initiative_template_industry_tag.gics_code > gics_sub_industry.sub_industry_code
gics_sector.sector_code < gics_industry_group.sector_code
gics_industry_group.industry_group_code < gics_sub_industry.industry_group_code
esg_category.category_id < regulatory_framework.esg_category_id
esg_component.component_id < assumption_benchmark.component_id

// ── Layer 2 internal ──────────────────────────────────────────────────────────
tenant.tenant_id - tenant_industry_profile.tenant_id
tenant_industry_profile.primary_industry_group_code > gics_industry_group.industry_group_code
tenant_industry_profile.primary_sub_industry_code > gics_sub_industry.sub_industry_code
tenant.tenant_id < tenant_materiality_override.tenant_id
esg_subcomponent.subcomponent_id < tenant_materiality_override.subcomponent_id
tenant.tenant_id < tenant_epis_weight_profile.tenant_id
esg_component.component_id < tenant_epis_weight_profile.component_id
tenant.tenant_id < tenant_epis_band_config.tenant_id
tenant.tenant_id < tenant_coa.tenant_id
tenant.tenant_id < coa_upload_report.tenant_id
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

// ── Layer 4 internal ──────────────────────────────────────────────────────────
esg_component.component_id < esg_initiative.component_id
esg_subcomponent.subcomponent_id < esg_initiative.subcomponent_id
business_size_profile.profile_id < esg_initiative.biz_size_profile_id
esg_initiative.initiative_id < context_resolution_log.initiative_id
esg_initiative.initiative_id < context_override_log.initiative_id
esg_initiative.initiative_id < assumption.initiative_id
esg_initiative.initiative_id < cost_line.initiative_id
esg_initiative.initiative_id < benefit_line.initiative_id
esg_initiative.initiative_id < scenario.initiative_id
scenario.scenario_id < cashflow_entry.scenario_id
scenario.scenario_id - financial_summary.scenario_id
esg_initiative.initiative_id < epis_score.initiative_id
scenario.scenario_id < epis_score.scenario_id
esg_initiative.initiative_id < kpi_measurement.initiative_id
esg_initiative.initiative_id < regulatory_obligation.initiative_id
regulatory_framework.framework_id < regulatory_obligation.framework_id
esg_initiative.initiative_id < physical_impact_record.initiative_id
esg_initiative.initiative_id < validation_check.initiative_id
esg_initiative.initiative_id < sponsor_recommendation.initiative_id
validation_check.check_id < remediation_record.validation_check_id

// ── Layer 5 internal ──────────────────────────────────────────────────────────
esg_initiative.initiative_id < pir_record.initiative_id
pir_record.pir_id < pir_actual_entry.pir_id
assumption_benchmark.benchmark_id < pir_actual_entry.promoted_benchmark_id
tenant.tenant_id < portfolio_aggregation.tenant_id

// =============================================================================
// LAYER GROUPS  (nested sub-groups cluster related entities within each layer)
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

Business Case [color: "#f59e0b"] {

  Initiative Core [color: "#fbbf24"] {
    esg_initiative
    context_resolution_log
    context_override_log
  }

  Financial Model [color: "#fbbf24"] {
    assumption
    cost_line
    benefit_line
    scenario
    cashflow_entry
    financial_summary
  }

  Scoring and Compliance [color: "#fbbf24"] {
    epis_score
    kpi_measurement
    regulatory_obligation
    physical_impact_record
  }

  Governance [color: "#fbbf24"] {
    validation_check
    sponsor_recommendation
    remediation_record
  }

  Post-Investment and Audit [color: "#fde68a"] {
    pir_record
    pir_actual_entry
    portfolio_aggregation
    audit_log
  }
}
```
