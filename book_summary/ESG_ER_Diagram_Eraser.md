# ESG Business Case Engine — ER Diagram (eraser.io)

Paste the code block below directly into eraser.io → New Diagram → Entity Relationship.  
Five colour-coded layers map to the five isolation tiers in the data architecture.

---

```
// ─────────────────────────────────────────────────────────────────────────────
// LAYER 1 — PLATFORM CONFIGURATION (no tenant_id, shared across all tenants)
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

platform_role [icon: users, color: "#6366f1"] {
  role_id uuid pk
  role_code varchar
  role_name varchar
  role_type varchar
  component_prefix varchar
}

// ─────────────────────────────────────────────────────────────────────────────
// LAYER 2 — TENANT CONFIGURATION (tenant_id, no workspace_id)
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

fx_rate_snapshot [icon: trending-up, color: "#0ea5e9"] {
  snapshot_id uuid pk
  rate_date date
  from_currency char
  to_currency char
  rate decimal
  source varchar
}

// ─────────────────────────────────────────────────────────────────────────────
// LAYER 3 — WORKSPACE CONFIGURATION (tenant_id + workspace_id, config tables)
// ─────────────────────────────────────────────────────────────────────────────

workspace [icon: layout, color: "#10b981"] {
  workspace_id uuid pk
  tenant_id uuid fk
  workspace_name varchar
  workspace_type varchar
  parent_workspace_id uuid fk
  industry_group_override varchar fk
  sub_industry_override varchar fk
  discount_rate_override decimal
  currency_override char
  coa_scope_filter_type varchar
  coa_scope_filter_values text[]
  is_active boolean
}

workspace_member [icon: users, color: "#10b981"] {
  member_id uuid pk
  workspace_id uuid fk
  tenant_id uuid fk
  user_id uuid fk
  role varchar
  is_active boolean
  assigned_at timestamptz
}

workspace_approval_chain [icon: git-merge, color: "#10b981"] {
  chain_id uuid pk
  workspace_id uuid fk
  tenant_id uuid fk
  self_validator_roles text[]
  independent_validator_roles text[]
  sponsor_roles text[]
  dual_approval_required boolean
  dual_approval_threshold decimal
  escalation_user_id uuid fk
}

workspace_regulatory_scope [icon: shield-check, color: "#10b981"] {
  id uuid pk
  workspace_id uuid fk
  tenant_id uuid fk
  framework_id uuid fk
  added_at timestamptz
}

group_reporting_source [icon: share-2, color: "#10b981"] {
  source_id uuid pk
  group_workspace_id uuid fk
  source_workspace_id uuid fk
  tenant_id uuid fk
  added_at timestamptz
}

// ─────────────────────────────────────────────────────────────────────────────
// LAYER 4 — BUSINESS CASE (tenant_id + workspace_id, transactional)
// ─────────────────────────────────────────────────────────────────────────────

esg_initiative [icon: zap, color: "#f59e0b"] {
  initiative_id uuid pk
  tenant_id uuid fk
  workspace_id uuid fk
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
  workspace_id uuid
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
  workspace_id uuid
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
  workspace_id uuid
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
  workspace_id uuid
  line_name varchar
  gl_code varchar
  account_type varchar
  monthly_amounts decimal[]
}

benefit_line [icon: plus-circle, color: "#f59e0b"] {
  benefit_line_id uuid pk
  initiative_id uuid fk
  tenant_id uuid
  workspace_id uuid
  line_name varchar
  gl_code varchar
  benefit_type varchar
  monthly_amounts decimal[]
}

scenario [icon: git-branch, color: "#f59e0b"] {
  scenario_id uuid pk
  initiative_id uuid fk
  tenant_id uuid
  workspace_id uuid
  scenario_name varchar
  is_primary boolean
}

cashflow_entry [icon: trending-up, color: "#f59e0b"] {
  cashflow_id uuid pk
  scenario_id uuid fk
  initiative_id uuid fk
  tenant_id uuid
  workspace_id uuid
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
  workspace_id uuid
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
  workspace_id uuid
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
  workspace_id uuid
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
  workspace_id uuid
  obligation_text text
  compliance_status varchar
  reviewer_id uuid fk
}

physical_impact_record [icon: globe, color: "#f59e0b"] {
  impact_id uuid pk
  initiative_id uuid fk
  tenant_id uuid
  workspace_id uuid
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
  workspace_id uuid
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
  workspace_id uuid
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
  workspace_id uuid
  issue_description text
  resolution_notes text
  resolved_by uuid fk
  resolved_at timestamptz
}

// ─────────────────────────────────────────────────────────────────────────────
// LAYER 5 — POST-INVESTMENT REVIEW & AUDIT
// ─────────────────────────────────────────────────────────────────────────────

pir_record [icon: clipboard, color: "#ef4444"] {
  pir_id uuid pk
  initiative_id uuid fk
  tenant_id uuid
  workspace_id uuid
  review_period_start date
  review_period_end date
  status varchar
  created_by uuid fk
  submitted_at timestamptz
}

pir_actual_entry [icon: activity, color: "#ef4444"] {
  entry_id uuid pk
  pir_id uuid fk
  initiative_id uuid fk
  tenant_id uuid
  workspace_id uuid
  metric_name varchar
  modelled_value decimal
  actual_value decimal
  variance_pct decimal
  promote_to_benchmark boolean
  promotion_status varchar
  promoted_benchmark_id uuid fk
}

workspace_aggregation [icon: layers, color: "#ef4444"] {
  aggregation_id uuid pk
  group_workspace_id uuid fk
  source_workspace_id uuid fk
  tenant_id uuid fk
  initiative_count int
  total_npv decimal
  total_project_cost decimal
  epis_critical_count int
  aggregated_at timestamptz
}

audit_log [icon: clock, color: "#ef4444"] {
  log_id uuid pk
  tenant_id uuid
  workspace_id uuid
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

// ── Layer 3 internal ──────────────────────────────────────────────────────────
tenant.tenant_id < workspace.tenant_id
workspace.workspace_id < workspace.parent_workspace_id
workspace.workspace_id < workspace_member.workspace_id
workspace.workspace_id - workspace_approval_chain.workspace_id
workspace.workspace_id < workspace_regulatory_scope.workspace_id
regulatory_framework.framework_id < workspace_regulatory_scope.framework_id
workspace.workspace_id < group_reporting_source.group_workspace_id
workspace.workspace_id < group_reporting_source.source_workspace_id

// ── Layer 4 internal ──────────────────────────────────────────────────────────
workspace.workspace_id < esg_initiative.workspace_id
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
workspace.workspace_id < workspace_aggregation.group_workspace_id
workspace.workspace_id < workspace_aggregation.source_workspace_id

// =============================================================================
// LAYER GROUPS
// =============================================================================

Platform Configuration [color: "#6366f1"] {
  esg_category
  esg_component
  esg_subcomponent
  materiality_definition
  epis_weighting_profile
  initiative_template
  initiative_template_workstream
  initiative_template_industry_tag
  gics_sector
  gics_industry_group
  gics_sub_industry
  regulatory_framework
  business_size_profile
  assumption_benchmark
  platform_role
}

Tenant Configuration [color: "#0ea5e9"] {
  tenant
  tenant_industry_profile
  tenant_materiality_override
  tenant_epis_weight_profile
  tenant_epis_band_config
  tenant_coa
  coa_upload_report
  tenant_component_override
  tenant_template_override
  tenant_private_template
  tenant_regulatory_scope
  fx_rate_snapshot
}

Workspace Configuration [color: "#10b981"] {
  workspace
  workspace_member
  workspace_approval_chain
  workspace_regulatory_scope
  group_reporting_source
}

Business Case [color: "#f59e0b"] {
  esg_initiative
  context_resolution_log
  context_override_log
  assumption
  cost_line
  benefit_line
  scenario
  cashflow_entry
  financial_summary
  epis_score
  kpi_measurement
  regulatory_obligation
  physical_impact_record
  validation_check
  sponsor_recommendation
  remediation_record
}

Post-Investment and Audit [color: "#ef4444"] {
  pir_record
  pir_actual_entry
  workspace_aggregation
  audit_log
}
```
