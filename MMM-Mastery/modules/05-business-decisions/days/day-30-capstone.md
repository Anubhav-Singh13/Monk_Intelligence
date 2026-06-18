# Day 30 - Capstone: The Surf Excel South Asia Brief

> **Role:** You are the MMM lead at Unilever South Asia. The VP Marketing for Surf Excel has a board presentation in 10 days.
>
> **Two decisions on the table:**
> 1. Should Surf Excel raise price by 7% simultaneously in Pakistan and Bangladesh?
> 2. Where should the brand invest the next £4M of incremental budget for maximum volume growth?
>
> This page is your end-to-end brief. There are no further lessons to lean on. Use the course.

---

## Your Data

| Source | Granularity | Coverage | Key variables |
|---|---|---|---|
| Nielsen retail audit | Weekly | 156 weeks, Pakistan + Bangladesh separately | Surf Excel and Ariel volume, ASP, ND, WD, RoS by market |
| Media investment | Weekly | 156 weeks, both markets | TV, digital, OOH, radio spend in local currency |
| Kantar Worldpanel | Weekly (aggregated to 4-weekly) | 52 weeks | Surf Excel penetration, frequency, repeat rate by pack size and SEC A/B/C |
| Internal trade / finance | Weekly | 156 weeks | Promotional calendar, distribution by account, price list changes, gross margin by SKU |
| Commodity index | Weekly | 156 weeks | Palm oil price index (Pakistan Bureau of Statistics), Bangladesh CPI food |
| Competitor IV candidate | Weekly | 156 weeks | Ariel Germany ASP in EUR (sourced from Statista / Euromonitor) |

**Quality flags to check before modelling:**

- Nielsen series often has a reporting gap around Eid / Ramadan in Pakistan (weeks 14-17 each year). Interpolate or use week-of-year fixed effects; do not silently forward-fill.
- Bangladesh WD data before Week 52 is based on a smaller panel (180 outlets vs 450 post-rebase). Flag this structural break.
- Kantar and Nielsen use different universe definitions. Align on a consistent volume base before merging.
- Palm oil is quoted FOB Rotterdam in USD. Convert to PKR/BDT using the weekly exchange rate before using as an instrument.

---

## Deliverable 1: The MMM Pipeline

Work through this checklist in order. Each step references the day where the concept was taught. The checklist is your pipeline specification; a reviewer will read it alongside your code.

---

### Step 1 — Data Preparation

**Relevant days:** Day 4 (adstock), Day 5 (saturation and normalisation), Day 7 (price), Day 9 (distribution)

- [ ] Apply geometric adstock to TV and digital spend in each market separately. Use `l_max = 8` weeks. Do not pool Pakistan and Bangladesh adstocks; carry-over dynamics may differ.

  ```python
  def geometric_adstock(x: np.ndarray, lam: float, l_max: int = 8) -> np.ndarray:
      """At = xt + lam * At-1, truncated at l_max lags."""
      out = np.zeros_like(x, dtype=float)
      for t in range(len(x)):
          out[t] = x[t]
          for lag in range(1, min(t + 1, l_max + 1)):
              out[t] += (lam ** lag) * x[t - lag]
      return out
  ```

  The decay parameter `lam` is a model parameter (prior in Step 4), not a fixed constant.

- [ ] Apply Hill saturation normalisation to each adstocked media channel. Normalise the raw spend to [0, 1] before passing to the Hill function so that `K` is interpretable as the half-saturation point on a unit scale.

  ```python
  # Hill: h(x) = x^alpha / (x^alpha + K^alpha)
  # Normalise spend first: x_norm = spend / spend.max()
  ```

- [ ] Construct log-price: `ln_price_surf = log(ASP_surf)`. Construct relative price index: `rel_price = ASP_surf / ASP_ariel`. Both enter the model; the IV identifies `ln_price_surf`.

- [ ] Build distribution variables: ND (numeric distribution), WD (weighted distribution), RoS (rate of sale = volume / WD-weighted outlets). All from Nielsen. Log-transform WD for the distribution elasticity calculation.

- [ ] Encode seasonality: week-of-year sine/cosine pair at 52-week period, plus binary indicators for Eid and Ramadan weeks (Pakistan) and Eid-ul-Adha and Pohela Boishakh (Bangladesh). Do not use a single shared seasonality index.

---

### Step 2 — Causal Audit

**Relevant days:** Day 13 (DAGs), Day 14 (backdoor criterion), Day 15 (identification status)

**Task:** Draw the Surf Excel volume DAG for Pakistan, then classify every backdoor path.

**Minimum required nodes for the DAG:**

```
PalmOilPrice --> SurfExcelASP --> SurfExcelVolume
ArielASP --> SurfExcelVolume
ArielASP --> SurfExcelASP   (competitor pricing response)
SeasonalDemand --> SurfExcelVolume
SeasonalDemand --> MediaSpend  (media is planned around demand peaks)
MediaSpend --> SurfExcelVolume
DistributionExpansion --> SurfExcelVolume
DistributionExpansion --> ASP  (new channels may have different price points)
TradePromotion --> SurfExcelVolume
TradePromotion --> ASP  (promoted price is lower than list price)
UnobservedRetailerBehaviour --> DistributionExpansion
UnobservedRetailerBehaviour --> SurfExcelVolume
```

**Backdoor path classification:**

| Path | Classification | Fix |
|---|---|---|
| `ASP <-- TradePromotion --> Volume` | Level 1: observable confounder | Include promotion binary in model |
| `ASP <-- SeasonalDemand --> Volume` | Level 1: observable confounder | Week-of-year controls |
| `ASP <-- UnobservedCostShocks --> Volume` | Level 2: unobservable, fixable | Palm oil IV (Day 17) |
| `DistributionExpansion <-- UnobservedRetailerBehaviour --> Volume` | Level 2: partially fixable | Geo-level controls + DiD pilot (Day 18) |
| `MediaSpend <-- SeasonalDemand --> Volume` | Level 1: observable confounder | Seasonality controls |
| `ArielASP <-- GlobalCategoryTrend --> SurfVolume` | Level 3: unblockable | Flag; treat ArielASP coefficient as associative |

**Expected finding:** Price endogeneity is the most consequential Level 2 path. A naive OLS will overestimate price elasticity (in absolute magnitude) because promotions depress both price and volume simultaneously, confounding the signal. The IV corrects this.

---

### Step 3 — IV Setup for Price

**Relevant day:** Day 17 (instrumental variables)

The instrument is palm oil price (PKR/tonne in Pakistan; BDT/tonne in Bangladesh, both derived from Rotterdam spot converted at weekly FX).

**Validity checks:**

- **Relevance:** Palm oil is the primary feedstock for surfactant in detergent manufacturing. A cost shock passes through to list price within 4-8 weeks. This is testable.
- **Exclusion:** Palm oil price affects Surf Excel volume only through manufacturing cost, which flows through to ASP. It does not affect consumer demand for clean laundry directly.
- **Exogeneity:** Global palm oil spot is determined by Malaysian and Indonesian supply conditions, not by Surf Excel volume in South Asia.

**First-stage regression:**

```python
import statsmodels.formula.api as smf

first_stage = smf.ols(
    "ln_price_surf ~ ln_palm_oil + ln_ariel_asp + week_sin + week_cos + eid + ramadan",
    data=df_pakistan
).fit()

print(first_stage.summary())
# F-statistic on ln_palm_oil exclusion: target > 10 (weak instrument threshold)
# Rule of thumb from Stock-Yogo: F > 10 for 10% maximal IV size distortion
```

**2SLS via linearmodels:**

```python
from linearmodels.iv import IV2SLS

iv_model = IV2SLS.from_formula(
    "ln_volume ~ 1 + [ln_price_surf ~ ln_palm_oil] + ln_ariel_asp + "
    "tv_adstock + digital_adstock + ln_wd + promotion + week_sin + week_cos + eid + ramadan",
    data=df_pakistan
).fit(cov_type="kernel")

print(iv_model.summary)
```

**Interpretation of the OLS vs IV gap:**

The OLS estimate of price elasticity will likely be less negative than the IV estimate (i.e., OLS understates demand sensitivity). The reason: promotions create a negative correlation between price and volume in the raw data, but promotions also increase volume. OLS conflates a price effect with a promotion effect. The IV strips out the promotion-driven price variation and isolates the structural price response. Expect the IV elasticity to be in the range -1.5 to -2.5 for a mid-tier detergent in price-sensitive South Asian markets; OLS will likely sit at -0.8 to -1.2.

---

### Step 4 — Prior Specification

**Relevant day:** Day 20 (prior elicitation)

Complete this table before fitting the PyMC-Marketing model. Every distribution and parameter must be justified.

| Parameter | Prior distribution | Parameters | Business rationale |
|---|---|---|---|
| Price elasticity (ln_price_surf) | Normal | mu = IV estimate ± 0.2, sigma = 0.3 | IV gives us a data-grounded center; sigma = 0.3 allows ±0.6 at 2 SD, covering realistic range |
| TV adstock decay (lam_tv) | Beta | alpha = 3, beta = 7 | Prior mode ~0.3, consistent with FMCG TV carry-over literature; heavy tails avoided |
| TV adstock hill_alpha | HalfNormal | sigma = 1 | Positive-only; allows sub- and super-linear; mode near 0 favours diminishing returns as default |
| TV hill_K (half-saturation) | Beta | alpha = 2, beta = 2 | Symmetric around 0.5 on normalised scale; spend rarely reaches full saturation |
| Digital adstock decay (lam_digital) | Beta | alpha = 2, beta = 8 | Digital carry-over shorter than TV; prior mode ~0.2 |
| Distribution elasticity (ln_wd) | HalfNormal | sigma = 0.5 | Positive effect expected; sigma = 0.5 allows elasticity up to ~1.5 at 3 SD |
| Promotion effect | Normal | mu = 0.05, sigma = 0.05 | Small positive lift; promotions in this category are often bought at lower base price, net uplift modest |
| Seasonality (sin/cos) | Normal | mu = 0, sigma = 0.1 | Weakly informative; direction unknown a priori |
| Model sigma (noise) | HalfNormal | sigma = 0.1 | On log-volume scale; 0.1 corresponds to ~10% unexplained variation |

Use the IV-estimated price elasticity (Step 3) as `mu` for the price elasticity prior. This is the correct Bayesian workflow: the IV result is prior information that updates through the full probabilistic model.

---

### Step 5 — PyMC-Marketing Model

**Relevant day:** Day 21 (PyMC-Marketing)

```python
import pymc as pm
import pymc_marketing as pmm
import numpy as np

with pm.Model() as mmm_surf_pakistan:

    # --- Priors ---
    beta_price  = pm.Normal("beta_price", mu=iv_elasticity, sigma=0.3)
    beta_wd     = pm.HalfNormal("beta_wd", sigma=0.5)
    beta_promo  = pm.Normal("beta_promo", mu=0.05, sigma=0.05)

    lam_tv      = pm.Beta("lam_tv", alpha=3, beta=7)
    alpha_tv    = pm.HalfNormal("alpha_tv", sigma=1)
    K_tv        = pm.Beta("K_tv", alpha=2, beta=2)

    lam_dig     = pm.Beta("lam_dig", alpha=2, beta=8)
    alpha_dig   = pm.HalfNormal("alpha_dig", sigma=1)
    K_dig       = pm.Beta("K_dig", alpha=2, beta=2)

    beta_sin    = pm.Normal("beta_sin", mu=0, sigma=0.1)
    beta_cos    = pm.Normal("beta_cos", mu=0, sigma=0.1)
    beta_eid    = pm.Normal("beta_eid", mu=0, sigma=0.1)

    sigma       = pm.HalfNormal("sigma", sigma=0.1)

    # --- Transformations ---
    tv_adstocked  = geometric_adstock(tv_spend_norm, lam_tv, l_max=8)
    tv_saturated  = tv_adstocked**alpha_tv / (tv_adstocked**alpha_tv + K_tv**alpha_tv)

    dig_adstocked = geometric_adstock(digital_spend_norm, lam_dig, l_max=8)
    dig_saturated = dig_adstocked**alpha_dig / (dig_adstocked**alpha_dig + K_dig**alpha_dig)

    # --- Linear predictor (log-log) ---
    mu = (
        intercept
        + beta_price  * ln_price_surf
        + beta_wd     * ln_wd
        + beta_promo  * promotion
        + beta_tv     * tv_saturated
        + beta_dig    * dig_saturated
        + beta_sin    * week_sin
        + beta_cos    * week_cos
        + beta_eid    * eid
    )

    # --- Likelihood ---
    y_obs = pm.Normal("y_obs", mu=mu, sigma=sigma, observed=ln_volume)

    # --- Sample ---
    trace = pm.sample(2000, tune=1000, chains=4, target_accept=0.9,
                      return_inferencedata=True)
```

**Convergence targets:**

| Diagnostic | Target | Action if missed |
|---|---|---|
| R-hat | < 1.01 for all parameters | Increase tuning draws; check for multicollinearity |
| ESS (bulk and tail) | > 400 | Increase chains or draws |
| Posterior predictive check | Simulated y should cover observed y distribution | Revisit likelihood family or priors |
| Energy fraction | > 0.2 (no divergences) | Reparameterise; increase `target_accept` |

Run models for Pakistan and Bangladesh separately. Do not pool unless you have a strong prior reason to believe the two markets share elasticities (they likely do not: Bangladesh is more price-sensitive and has a smaller digital media ecosystem).

---

### Step 6 — Validation

**Relevant day:** Day 22 (model validation)

**In-sample performance:**

```python
from sklearn.metrics import mean_absolute_percentage_error

y_pred_mean = trace.posterior_predictive["y_obs"].mean(("chain", "draw"))
mape_insample = mean_absolute_percentage_error(ln_volume, y_pred_mean)
# Target: < 10%
```

**Holdout validation (last 26 weeks):**

Refit on Weeks 1-130. Predict Weeks 131-156. Report MAPE on the holdout. Target < 14%. A model that fits well in-sample but fails on holdout is overfit; revisit the number of seasonality harmonics.

**Calibration against external benchmarks:**

- Cross-check the TV ROI against the Analytic Partners FMCG benchmark range (£0.40-£0.90 per £1 invested for laundry in emerging markets). Flag if your estimate is outside this range.
- Cross-check the distribution elasticity against any Unilever internal DiD pilots if available. If no DiD is available, mark the distribution coefficient as "directional only" in the model output.
- Cross-check the price elasticity against the IV estimate from Step 3. If the Bayesian posterior mean differs from the IV point estimate by more than 0.3, investigate: the model may be absorbing price variation through the promotion or seasonality terms.

**Flag for the deck (Slide 4):**

| Effect | Identification status | Method | Flag |
|---|---|---|---|
| Price elasticity | Identified | IV (palm oil instrument) | Report F-stat |
| TV elasticity | Partially identified | Controls + adstock | Assume no unmeasured TV confounders |
| Digital elasticity | Partially identified | Controls | Attribution window unclear; digital spend and search may be correlated |
| Distribution elasticity | Partially identified | Geo controls | No DiD available; flag as directional |
| Promotion effect | Associative | OLS controls | Promotion endogenous; correct direction but magnitude uncertain |

---

### Step 7 — Contribution Decomposition

Produce the 52-week stacked contribution chart. Each channel's contribution in week t:

```python
# Contribution of channel k in week t:
# contrib_k_t = beta_k * x_k_t
# where x_k_t is the transformed input (adstocked + saturated for media; log-value for price)

# Base = intercept + seasonality + fixed effects
# Total = sum of all contributions + base

# Decomposition table (last 52 weeks):
decomp = {
    "Base (intercept + seasonality)": ...,
    "TV": ...,
    "Digital": ...,
    "Price effect": ...,   # NOTE: this is the price DEVIATION from baseline, not total price
    "Trade promotion": ...,
    "Distribution (WD)": ...,
    "Residual / unexplained": ...,
}
# All entries in both % of total volume and absolute units (tonnes or cases)
```

**Produce this decomposition for both Pakistan and Bangladesh separately.** Do not combine into a single South Asia number for the operational model; the markets have different base sizes, different media mixes, and different price sensitivity profiles. Aggregate only for the board-level summary slide.

---

### Step 8 — Portfolio and Competitor Extension

**Relevant day:** Day 23 (competitive dynamics and portfolio)

- Add Ariel ASP to the model as a cross-price variable. The cross-price elasticity should be positive (Ariel price up → Surf Excel volume up as consumers trade down).
- Check own-portfolio cross-elasticity across the 5 major Surf Excel SKUs (500g, 1kg, 2kg pouch, 3kg bag, bulk 5kg). Cannibalization must be quantified before any pack rationalization recommendation.
- Produce the CDI/BDI white space map for both markets by division/region.

**CDI/BDI calculation:**

```python
# CDI (Category Development Index):
# CDI_region = (category_volume_region / category_volume_total) /
#              (population_region / population_total) * 100

# BDI (Brand Development Index):
# BDI_region = (brand_volume_region / brand_volume_total) /
#              (population_region / population_total) * 100

# White space: high CDI, low BDI = underpenetrated Surf Excel in a developed category
# Opportunity = prioritise distribution expansion here first
```

---

## Deliverable 2: The 10-Slide CMO Deck

Each slide specification includes: what it must contain, what MMM output underpins it, and what failure looks like.

---

### Slide 1 — Executive Summary

**Must contain:**
- One decision table with two rows (price decision; budget decision) and three columns (Recommendation, Expected volume impact with HDI, Risk caveat).
- No more than 200 words on the slide.
- The recommendation must be specific: a number for the price increase and a table of budget reallocation by channel.

**Underpinned by:** Slides 5 and 6 (do those first; write this slide last).

**Failure mode:** "We recommend a moderate price increase subject to competitive conditions." This is not a recommendation. The VP needs a number and an owner.

---

### Slide 2 — Data Foundation

**Must contain:**
- Data sources table (source, period, markets, key variables).
- Explicit quality flags: Nielsen panel rebase in Bangladesh at Week 52; Eid gap interpolation in Pakistan; Kantar vs Nielsen universe alignment.
- A sentence on what data was requested but not available (if any), and how that absence affects confidence.

**Failure mode:** Omitting the quality flags. A CMO who later discovers the Bangladesh data changed methodology at Week 52 will lose confidence in every number on the deck.

---

### Slide 3 — Volume Decomposition (Last 2 Years)

**Must contain:**
- Stacked area chart by half-year, showing Base / TV / Digital / Price / Promo / Distribution / Residual.
- One sentence per half-year naming the dominant driver.
- The biggest surprise in the decomposition and its implication. Example: "Distribution decline in H2 Year 2 accounts for 8% of volume loss, larger than the combined media investment effect. This suggests the distribution recovery is a higher-return action than incremental media spend."

**Failure mode:** A chart with no narrative. Numbers without interpretation are not a recommendation.

---

### Slide 4 — Causal Identification Status

**Must contain:** The full identification table from Step 6. Every effect must have a status (Identified / Partially identified / Associative), the method used, and the implication for confidence.

**Why this slide exists:** It is the analyst's professional protection. If the price recommendation is later challenged, Slide 4 demonstrates that the price effect was identified via IV, not naive OLS correlation. This is not a defensive slide; it is a credibility slide.

**Failure mode:** Omitting it because "the CMO won't understand it." Translate it; do not remove it.

---

### Slide 5 — Pricing Decision

**Must contain:**

1. IV elasticity vs OLS elasticity, both markets. Show the gap and explain the direction in one sentence.

2. Break-even table:

| Price increase | Required volume retention | Current estimated volume retention | Decision |
|---|---|---|---|
| 3% | `3% / (3% + margin%)` | [from model] | |
| 5% | `5% / (5% + margin%)` | [from model] | |
| 7% | `7% / (7% + margin%)` | [from model] | Recommendation |
| 10% | `10% / (10% + margin%)` | [from model] | |

3. Monte Carlo `P(profitable)` for the 7% recommendation in each market. This is the probability that revenue net of volume loss is positive, computed over the posterior distribution of the price elasticity.

```python
# Monte Carlo P(profitable) for 7% price increase:
price_inc = 0.07
margin_pct = gross_margin_pct  # from internal finance data

# For each posterior sample of beta_price:
posterior_elasticities = trace.posterior["beta_price"].values.flatten()
delta_volume_pct = posterior_elasticities * np.log(1 + price_inc)  # log-log model
delta_revenue = price_inc + delta_volume_pct  # approximate: revenue = price * volume
p_profitable = np.mean(delta_revenue > 0)
```

4. Scenario table: 2x2 of Surf Excel price increase (7% / no change) × Ariel response (follows / holds price). Report expected Surf Excel volume share outcome in each cell.

**Failure mode:** Reporting only the point estimate of elasticity without the posterior distribution. The CMO needs to know: "there is a 72% chance this is profitable" not "our elasticity is -1.8."

---

### Slide 6 — Budget Allocation

**Must contain:**

1. Current vs optimised allocation table (both markets, £ amounts):

| Channel | Current spend (£) | Optimised spend (£) | Change | Expected incremental volume |
|---|---|---|---|---|
| Pakistan TV | | | | |
| Pakistan Digital | | | | |
| Bangladesh TV | | | | |
| Bangladesh Digital | | | | |
| OOH (pooled) | | | | |
| **Total** | £4M | £4M | — | |

2. ROI curves for TV and digital in each market, showing current spend position on the curve relative to the saturation point. A spend level past the knee of the Hill curve is a visual argument for reallocation.

3. Expected incremental volume from the reallocation with 90% HDI. Do not report a point estimate alone.

**The optimisation:**

```python
from scipy.optimize import minimize

def neg_total_volume(budget_allocation, models, current_base):
    """budget_allocation: array of channel budgets summing to total_budget."""
    total_vol = 0
    for i, (spend, model) in enumerate(zip(budget_allocation, models)):
        spend_norm = spend / channel_max_spend[i]
        sat = spend_norm**alpha[i] / (spend_norm**alpha[i] + K[i]**alpha[i])
        total_vol += beta[i] * sat
    return -total_vol

result = minimize(
    neg_total_volume,
    x0=current_allocation,
    constraints={"type": "eq", "fun": lambda x: x.sum() - total_budget},
    bounds=[(0, channel_cap[i]) for i in range(n_channels)],
    method="SLSQP"
)
```

Use posterior mean parameters for the optimisation; report the HDI on the volume outcome by propagating uncertainty through the allocation.

---

### Slide 7 — Distribution and White Space

**Must contain:**
- CDI/BDI map for Pakistan and Bangladesh by region/division (scatter plot: CDI on x-axis, BDI on y-axis, bubble size = current Surf Excel volume).
- Top 5 geographies for distribution investment, filtered by: (a) high CDI / low BDI (white space), and (b) RoS above the national median (the category is already working there; Surf Excel is underpresent).
- Proposed DiD pilot design for one geography: treatment market, control market, matching criteria, primary metric (volume per outlet), minimum detectable effect, duration.

**Failure mode:** Recommending distribution expansion without the RoS screen. Expanding into low-RoS geographies wastes trade investment.

---

### Slide 8 — Pack Portfolio

**Must contain:**
- Net contribution by SKU table (revenue minus variable cost, accounting for cross-SKU cannibalization from the portfolio elasticity matrix in Step 8).
- PPU (price per unit volume) architecture: are the current pack sizes spaced to trade consumers up, or are there PPU inversions that encourage trading down?
- Any delist recommendations with explicit cannibalization analysis. If delisting the 500g pack, show what % of its volume transfers to the 1kg pack vs exits the brand.

**Failure mode:** Recommending a delist based on SKU margin alone, without the cannibalization adjustment. A SKU with 15% margin may be holding 30% of its volume in the brand by acting as an affordable entry point.

---

### Slide 9 — What We Are Betting On

This is the most important slide for professional integrity. Every key input to Slide 1 is categorised here.

| Decision input | Status | Basis | Confidence | What would change this |
|---|---|---|---|---|
| Price elasticity Pakistan | Causal claim (identified) | IV (palm oil), F = [value] | High | A better instrument; a controlled price test |
| Price elasticity Bangladesh | Causal claim (identified) | IV (palm oil), F = [value] | High | As above |
| TV ROI Pakistan | Assumption (partially identified) | Controls + adstock | Medium | A geo holdout experiment |
| TV ROI Bangladesh | Assumption (partially identified) | Controls + adstock | Medium | As above |
| Digital ROI | Assumption (partially identified) | Controls; attribution window uncertain | Medium-Low | Platform lift study |
| Distribution elasticity | Bet (associative) | Observational; no DiD | Low | DiD pilot (Slide 10) |
| Ariel cross-price elasticity | Bet (associative) | Observational | Low | Controlled price gap experiment |
| Pack cannibalization | Bet (associative) | Elasticity matrix, no holdout | Low | SKU delist test in one market |

**Do not abbreviate this table.** A VP who reads only this slide should understand exactly how much of the Slide 1 recommendation rests on solid identification vs directional evidence.

---

### Slide 10 — Validation Plan

**Must contain a test for each "bet" and "assumption" from Slide 9:**

| Test | Type | Market | Duration | Primary metric | Decision trigger |
|---|---|---|---|---|---|
| TV geo holdout | DiD | 2 matched Bangladesh regions | 26 weeks | Volume per outlet in treatment vs control | If TV ROI estimate is outside ±30% of model estimate, recalibrate before next budget cycle |
| Distribution pilot | DiD | 3 underpenetrated Pakistan districts | 16 weeks | WD gained; volume per new outlet vs national RoS | If distribution elasticity < 0.3, deprioritise distribution investment for next year |
| Price test (Bangladesh only) | Controlled | 10 matched store clusters | 12 weeks | Own-price elasticity vs model estimate | If IV elasticity and test elasticity differ by > 0.5, rerun IV with Bangladesh-specific instrument |
| Digital lift study | Platform-partnered | Both markets | 8 weeks | Incremental reach and sales lift | Flag if digital ROI < £0.30 / £1 invested |

---

## Evaluation Rubric

A completed capstone is evaluated on five dimensions, 20 points each, for a maximum of 100 points.

---

### Dimension 1 — Causal Rigour (20 points)

| Criterion | Points |
|---|---|
| Price endogeneity correctly identified in the DAG and IV applied in Step 3 | 6 |
| First-stage F-statistic reported and interpreted (F > 10 threshold noted) | 4 |
| Level 3 effects (unblockable backdoor paths) correctly identified and flagged as unidentified | 4 |
| No causal language used for effects classified as "associative" in Slide 9 | 4 |
| OLS vs IV gap explained in the correct direction with correct mechanism | 2 |

**Common failure:** Applying IV correctly in the Python code but then writing "the model shows that distribution expansion causes volume growth" in the deck. The IV buys you the causal claim on price. It does not buy you a causal claim on distribution.

---

### Dimension 2 — Model Quality (20 points)

| Criterion | Points |
|---|---|
| R-hat < 1.01 for all parameters (reported in validation report) | 5 |
| In-sample MAPE < 10% | 4 |
| Holdout MAPE (last 26 weeks) < 14% | 4 |
| Prior predictive check run and documented (simulated y covers plausible range) | 4 |
| At least one external calibration attempted (Analytic Partners benchmark or Unilever DiD) | 3 |

**Common failure:** Reporting in-sample fit only. A model with MAPE 6% in-sample and 22% on holdout is overfit. The holdout test is non-negotiable.

---

### Dimension 3 — Decision Translation (20 points)

| Criterion | Points |
|---|---|
| Price recommendation is a specific number (not a range or "it depends") with volume and margin impact | 6 |
| Budget reallocation table has specific £ amounts by channel, not percentages only | 6 |
| P(profitable) reported for the price recommendation (Monte Carlo over posterior) | 4 |
| HDI reported for the budget reallocation volume uplift | 4 |

**Common failure:** "We recommend investing more in digital and less in TV." This is not a recommendation. "We recommend shifting £800K from Pakistan TV to Bangladesh digital, expected to generate 2,400 additional tonnes of volume (90% HDI: 1,600–3,200) over 12 months" is a recommendation.

---

### Dimension 4 — Honesty (20 points)

| Criterion | Points |
|---|---|
| Slide 9 table is complete (all five 5Ps covered) | 6 |
| Identification status correctly assigned: identified / partially identified / associative | 6 |
| Limitations statement correctly distinguishes between effects the model identified and effects it estimated | 4 |
| No identified effects labelled as bets; no bets labelled as identified | 4 |

**Common failure:** Omitting Slide 9 because "the client doesn't want to hear about uncertainty." The client who makes a £4M decision based on an associative effect that turns out to be spurious will not be pleased. Slide 9 is what separates a professional MMM analyst from a data storyteller.

---

### Dimension 5 — Communication (20 points)

| Criterion | Points |
|---|---|
| Slide 1 executive summary is self-contained (a non-statistician can act on it without Slides 3–8) | 8 |
| All technical terms translated into business language at first use (e.g., "IV = cost-based instrument that removes pricing bias") | 4 |
| Charts are labelled with business units (tonnes, £), not model output names | 4 |
| The deck tells a story: problem → evidence → recommendation → validation plan | 4 |

**Common failure:** Slide 1 contains the word "posterior." Translate everything. The VP does not need to know the model is Bayesian; they need to know how confident you are and why.

---

## What "Done" Looks Like

A completed capstone consists of three files:

**File 1: Python notebook or script (`surf_excel_mmm.ipynb` or `.py`)**

Contains, in order: data loading and quality checks, adstock and saturation transformations, IV first-stage and 2SLS, prior specification table as comments, PyMC-Marketing model definition and sampling, posterior predictive check, contribution decomposition, budget optimisation, CDI/BDI calculation, portfolio elasticity matrix.

The notebook must run end-to-end without manual intervention. All random seeds set for reproducibility.

**File 2: Validation report (`validation_report.md`)**

Must include: in-sample MAPE, holdout MAPE, R-hat summary table, ESS table, posterior predictive check plot description, IV F-statistic, comparison of IV vs OLS elasticity, any external calibration attempted and the result, and a clear statement of which coefficients are partially identified or associative.

**File 3: CMO deck brief (`cmO_deck_brief.md` or slides)**

All 10 slides specified above. Must include a specific price recommendation (not "it depends"), a specific budget reallocation table with £ amounts, and the Slide 9 limitations table in full.

**Minimum bar for the price recommendation:** "Based on the IV-estimated price elasticity of [value] (90% HDI: [low, high]) in Pakistan and [value] (90% HDI: [low, high]) in Bangladesh, a 7% simultaneous price increase is expected to result in a [value]% volume decline in Pakistan and [value]% in Bangladesh. Net revenue impact is estimated at +£[value] over 12 months. P(profitable at 7% increase) = [value]% in Pakistan and [value]% in Bangladesh."

Any submission that says "we recommend monitoring the situation" or "a price increase may be considered depending on competitive response" has not completed the capstone.

---

## Further Reading

These five resources cover the complete intellectual foundation of this course. Read them in this order if you are starting from scratch; use them as references if you completed the 30 days.

1. **PyMC-Marketing documentation** — `pymc-marketing.io/en/stable/`. Start with the MMM quickstart and the contributed time-varying parameters notebook. The saturation and adstock API documentation is the ground truth for the transformations used in Steps 1 and 5 above.

2. **Gelman, Carlin, Stern, Dunson, Vehtari, Rubin — *Bayesian Data Analysis*, 3rd ed. (BDA3)** — Chapters 2-3 (prior specification), Chapter 6 (model checking), Chapter 17 (hierarchical models). The R-hat and ESS diagnostics in Step 5 come from Vehtari et al. 2021, which is a direct descendant of BDA3 Chapter 11.

3. **Angrist and Pischke — *Mostly Harmless Econometrics* (2009)** — Chapter 4 (IV and 2SLS), Chapter 5 (DiD). The clearest treatment of the identification machinery used in Steps 2, 3, and 7. The "first-stage F > 10" rule of thumb comes from Stock and Watson's treatment cited in this book.

4. **Charan — *Marketing Analytics Practitioner's Guide* (2021)** — Part III (MMM in practice), Part IV (decision translation). The contribution decomposition methodology in Step 7 and the break-even table structure in Slide 5 follow the practitioner conventions documented here.

5. **Pearl — *Causality: Models, Reasoning, and Inference*, 2nd ed. (2009)** — Chapters 1-3 (DAGs and d-separation), Chapter 5 (identifiability). The DAG audit in Step 2 and the Level 1/2/3 classification in Step 2 are grounded in Pearl's do-calculus framework. If you found Day 13 and Day 14 interesting, this is where to go next.

---

## Navigation

← Previous: [Day 29 - Pack and Portfolio Decisions](./day-29-pack-portfolio-decisions.md)

↑ Back to course overview: [README](../../../README.md)
