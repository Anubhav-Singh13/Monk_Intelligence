# Day 22 - Model Validation: Fit Quality vs. Causal Validity

> **Today's one idea:** A model can have excellent in-sample fit (MAPE < 8%) and still produce wrong decompositions — validation requires both statistical fit tests **and** calibration against external causal benchmarks such as DiD geo-test estimates or IV estimates.
>
> **Reading time:** ~35 min · **Prereqs:** Day 16 (DiD), Day 21 (PyMC-Marketing pipeline)
>
> **Primary sources for today:**
> - Chan, D. & Perry, M. (2017). *Challenges and Opportunities in Media Mix Modeling*. Google EMEA.
> - Jin, Y., Wang, Y., Sun, Y., Chan, D., & Koehler, J. (2017). *Bayesian Methods for Media Mix Modeling with Carryover and Shape Effects*. Google Research.
>
> **Before you start:** Recall Day 21's load-bearing idea — one sentence on what the posterior distribution gives you that OLS does not. Write it down before reading on.

---

## The Hook (2-4 min)

A Knorr Soups UK MMM is signed off. Train MAPE = 7.1%, R² = 0.94. The decomposition says TV drives 28% of volume at ROI = 3.9×. The brand manager doubles TV spend for the next fiscal year.

Volume grows 6%. Not 28%. Not compounding. 6%.

The postmortem finds that the TV coefficient had absorbed a spurious correlation with a cold-winter seasonal index that correlated with both TV investment and soup consumption. The model tracked history beautifully. It estimated the wrong causal number.

MAPE measures your model's ability to reproduce the past. It says nothing about whether the TV coefficient is the causal effect of TV. These are two entirely different tests, and most practitioners stop after the first one.

---

## Building the Intuition (10-15 min)

### The Three-Layer Validation Framework

Think of model validation as three concentric rings. Each ring catches a different class of failure.

```
┌─────────────────────────────────────────┐
│  Layer 3: Causal Calibration            │
│  "Does the coefficient agree with       │
│   DiD / IV / holdout experiments?"      │
│  ┌───────────────────────────────────┐  │
│  │  Layer 2: Structural Sanity       │  │
│  │  "Do signs, magnitudes, and       │  │
│  │   shapes make business sense?"    │  │
│  │  ┌─────────────────────────────┐  │  │
│  │  │  Layer 1: Statistical Fit   │  │  │
│  │  │  "Does the model reproduce  │  │  │
│  │  │   observed data?"           │  │  │
│  │  │  MAPE, NRMSE, PPC           │  │  │
│  │  └─────────────────────────────┘  │  │
│  └───────────────────────────────────┘  │
└─────────────────────────────────────────┘

Most practitioners stop here ──────────────────────┘
The honest test is here ──────────────────────────────┘
```

**Layer 1** answers: *Is the model consistent with the data it was trained on?* A model that fails here is broken. But passing Layer 1 is necessary, not sufficient.

**Layer 2** answers: *Is the model structurally plausible?* Negative price elasticity, positive distribution elasticity, adstock decay in the plausible range. A model that fails here has a specification problem — wrong functional form, missing variable, or sign-flipping multicollinearity.

**Layer 3** answers: *Is the causal coefficient close to what a designed experiment tells us?* This is the test that catches the Knorr soups failure. No amount of MAPE improvement would have found it.

### Why Fit and Causality Decouple

Suppose TV spend is correlated with a cold-weather index (r = 0.72). Your model includes TV spend but not the weather index. OLS will allocate some of the weather effect's explanatory power to the TV coefficient. MAPE improves because the TV variable is doing double duty — tracking both its own effect and the omitted weather effect. The fit looks good. The causal estimate is biased upward.

This is exactly the omitted variable bias formula from Day 14:

$$\hat{\beta}_{TV} = \beta_{TV}^{true} + \beta_{weather} \cdot \frac{\text{Cov}(TV, weather)}{\text{Var}(TV)}$$

MAPE cannot see this. DiD calibration can, because the geo-test isolates TV variation by design.

---

## The Formal Picture (10-15 min)

### Layer 1: Statistical Fit Metrics

```python
from sklearn.metrics import mean_absolute_percentage_error, mean_squared_error
import numpy as np
import arviz as az

def validation_metrics(y_true: np.ndarray, y_pred: np.ndarray, label: str = "") -> tuple[float, float]:
    mape = mean_absolute_percentage_error(y_true, y_pred) * 100
    nrmse = np.sqrt(mean_squared_error(y_true, y_pred)) / y_true.mean() * 100
    print(f"{label:12s}  MAPE: {mape:5.1f}%   NRMSE: {nrmse:5.1f}%")
    return mape, nrmse

# Posterior predictive mean — shapes (chain, draw, obs) → mean over MCMC dims
y_pred_train = idata.posterior_predictive["y"].mean(dim=["chain", "draw"]).values
y_pred_test  = idata.predictions["y"].mean(dim=["chain", "draw"]).values  # holdout group

validation_metrics(np.exp(log_y_train), np.exp(y_pred_train), label="Train")
validation_metrics(np.exp(log_y_test),  np.exp(y_pred_test),  label="Holdout")
```

**Fit thresholds (FMCG weekly data):**

| Metric | Good | Acceptable | Investigate |
|---|---|---|---|
| In-sample MAPE | < 8% | 8–12% | > 12% |
| Out-of-sample MAPE | < 12% | 12–18% | > 18% |
| NRMSE (normalised) | < 10% | 10–15% | > 15% |

These are not universal constants. High-noise categories (impulse confectionery, seasonal snacks) tolerate +3–4 pp. Stable staples (Knorr stock cubes) should hit the tighter thresholds.

**Holdout strategy:** Reserve the final 13–26 weeks of your series as a held-out test set. Never let the optimiser see it. The gap between train MAPE and holdout MAPE is your overfitting diagnostic. A gap > 6 pp signals too many degrees of freedom relative to signal.

### Layer 2: Structural Sanity Checks

```python
def sanity_checks(idata: az.InferenceData, var_names: list[str]) -> None:
    summary = az.summary(idata, var_names=var_names)

    checks = [
        ("Price coefficient negative",          summary.loc["beta_price", "mean"] < 0),
        ("TV coefficient positive",             summary.loc["beta_tv",    "mean"] > 0),
        ("TV adstock lambda in [0.3, 0.95]",    0.3 < summary.loc["lam_tv", "mean"] < 0.95),
        ("Distribution (WD) coeff positive",    summary.loc["beta_wd",    "mean"] > 0),
        ("Price elasticity magnitude < 3.0",    abs(summary.loc["beta_price", "mean"]) < 3.0),
    ]

    for name, passed in checks:
        status = "PASS" if passed else "FAIL"
        print(f"  [{status}] {name}")
```

**Typical FMCG magnitude ranges** (log-log specification):

| Variable | Expected sign | Plausible magnitude |
|---|---|---|
| Price elasticity | − | −0.5 to −2.5 |
| TV elasticity (log spend) | + | 0.02 to 0.15 |
| Distribution (WD) elasticity | + | 0.3 to 1.2 |
| Promo depth (% discount) | + | 0.01 to 0.08 per pp |
| Adstock lambda (TV) | + | 0.3 to 0.9 |

A coefficient outside these ranges is not automatically wrong — but it demands an explanation before sign-off.

### Layer 3: Causal Calibration Against External Benchmarks

The Knorr Yorkshire DiD geo-test from Day 16 gave us a clean causal estimate of distribution elasticity. We can now compare it to the MMM posterior.

```python
# ── Calibration: MMM distribution coefficient vs. DiD benchmark ──────────────
# DiD result (Day 16): +9% volume lift for +6 WD points in Yorkshire
# Yorkshire baseline volume ~ 38,000 units/week
did_volume_per_wd_pt = 0.09 * 38_000 / 6   # ≈ 570 units per WD point

mmm_beta_wd = float(az.summary(idata, var_names=["beta_wd"])["mean"])

# Convert log-log coefficient to level units at the mean
# delta_y ≈ beta_wd * (delta_WD / WD_mean) * y_mean
# Rearranging: units per WD point = beta_wd * y_mean / WD_mean
y_mean   = float(y_train.mean())
wd_mean  = float(wd_train.mean())
mmm_units_per_wd = mmm_beta_wd * y_mean / wd_mean

discrepancy_pct = abs(mmm_units_per_wd - did_volume_per_wd_pt) / did_volume_per_wd_pt * 100

print(f"MMM estimate : {mmm_units_per_wd:,.0f} units / WD point")
print(f"DiD benchmark: {did_volume_per_wd_pt:,.0f} units / WD point")
print(f"Discrepancy  : {discrepancy_pct:.0f}%")

# Interpretation
if discrepancy_pct < 20:
    print("Status: CALIBRATED — proceed with decomposition")
elif discrepancy_pct < 40:
    print("Status: FLAG — investigate confounders before sign-off")
else:
    print("Status: SUSPECT — likely confounding; do not publish decomposition")
```

**The same logic applies to TV vs. a media holdout experiment, or price vs. IV estimates from Day 17.**

### Posterior Predictive Check (PPC)

A PPC is a Bayesian-specific Layer 1 check. It asks: if we simulate new data from the fitted posterior, does the simulated distribution look like the observed data?

```python
# Plot 200 draws from the posterior predictive against observed
ax = az.plot_ppc(idata, observed_rug=True, num_pp_samples=200)
ax.set_title("Posterior Predictive Check — Knorr Soups UK")
# What to look for:
#   - Observed line (dark) should fall within the simulated envelope
#   - If observed is consistently in the tail: systematic mis-specification
#   - If envelope is very wide: high residual variance, model is uncertain
```

A PPC failure that passes MAPE often signals a distributional mis-specification — for example, using a Normal likelihood on weekly data that has heavy right tails during promotions. Switching to a Student-t or log-normal likelihood resolves it.

---

## Where It Breaks / What It Is Not (3-5 min)

**1. "Good MAPE proves the model is right."**
No. High-dimensional models with correlated predictors achieve good fit by distributing attribution across them in statistically arbitrary ways. MAPE only tests whether the sum of all parts tracks sales. It says nothing about how the parts are allocated. Always run Layer 2 and 3 before publishing a decomposition.

**2. "The posterior predictive check proves causality."**
No. The PPC only tests whether the generative model is consistent with the observed marginal distribution of sales. It cannot distinguish between a causally correct model and a well-fit confounded one. Causal validity requires Layer 3.

**3. "Discrepancy between MMM and DiD means MMM is wrong."**
Not necessarily. DiD has its own assumptions — parallel trends, no spillover (SUTVA, Day 16). A discrepancy could reflect a DiD violation in the geo-test, not a model failure in the MMM. The correct response is to document both estimates with their assumptions and explain the gap to the stakeholder. Do not silently adjust the MMM to match the DiD.

**4. "If we can't validate against DiD we should just report what the model gives us."**
Absence of an external benchmark does not mean the model is validated. It means the causal validity is unknown. The honest communication is to report the posterior with its uncertainty and note that Layer 3 validation has not been performed. Decision-making on unvalidated causal estimates carries a specific and nameable risk.

---

## Try It Yourself (5-10 min)

**Exercise 1 — Retrieval**

Close the page. Name the three validation layers, what each tests, and which layer catches causal bias. Write it from memory before checking.

<details>
<summary>Reference answer</summary>

- **Layer 1 — Statistical fit:** Does the model reproduce observed data? Tests: MAPE, NRMSE, holdout gap, PPC. Catches: broken specification, overfitting.
- **Layer 2 — Structural sanity:** Do signs, magnitudes, and shapes make business sense? Tests: sign checks, plausible elasticity ranges, adstock lambda bounds. Catches: multicollinearity-driven sign flips, implausible scale.
- **Layer 3 — Causal calibration:** Do model coefficients agree with external causal estimates (DiD, IV, holdout experiments)? Tests: coefficient comparison with geo-test estimates or IV estimates, discrepancy %. **This is the layer that catches causal bias.**

</details>

---

**Exercise 2 — Application**

A Knorr Ambient UK MMM returns:
- Train MAPE = 9.2%, holdout MAPE = 14.8%
- TV coefficient is 2.8× the DiD benchmark from a Yorkshire geo-test

Diagnose the model. What are the two most likely problems and what would you do about each?

<details>
<summary>Reference answer</summary>

**Problem 1: Overfitting (train–holdout MAPE gap = 5.6 pp)**
The gap is large enough to suspect too many correlated predictors or insufficient regularisation. The model has learned idiosyncratic patterns in the training period that do not generalise.
*Action:* Tighten priors on media coefficients (pull toward zero), reduce the number of free parameters, or apply stronger hierarchical shrinkage if this is a multi-market model. Re-check whether any predictors are near-collinear.

**Problem 2: TV coefficient upward bias (2.8× DiD benchmark)**
The TV estimate has absorbed attribution from a correlated omitted variable — the most common candidates are seasonal effects (cold weather, back-to-school), macroeconomic shocks, or brand equity trends that co-move with media investment.
*Action:* Add the suspected confounder explicitly (a temperature index, a consumer confidence series). Re-run Layer 3. If the new TV coefficient falls within 20–40% of the DiD benchmark, accept with a flag. If it remains >2×, the identification strategy needs revisiting — consider using the DiD estimate as an informative prior on the TV coefficient (prior-data conflict resolution from Day 21).

</details>

---

**Exercise 3 — Stretch (Day 15)**

An analyst says: "If we cannot validate against DiD we should just report whatever the model gives us with a confidence interval."

Using Day 15's identification levels, write the correct rebuttal in two sentences.

<details>
<summary>Reference answer</summary>

Day 15 distinguishes **association** (Layer 1 fit), **conditional independence** (covariate control), and **causal identification** (designed variation or instrument). A wide confidence interval on an associational estimate does not become a causal estimate — it remains an association with wide uncertainty, and reporting it as a coefficient driving ROI decisions conflates the identification level with the precision level. The honest communication is to label the estimate as "model-based association, causal validity unverified" and note the risk to any budget decision that depends on it.

</details>

---

> **Transfer:** In your brand portfolio, identify the one coefficient that drives the largest budget reallocation recommendation — that is the coefficient you most need to validate against an external causal benchmark before presenting to a CMO.

---

## Connect It Back

Yesterday (Day 21) you built the full PyMC-Marketing pipeline and obtained a posterior distribution over all model parameters. The posterior gives you uncertainty quantification that OLS cannot — but a well-calibrated posterior over a confounded model is still a confounded model. Today's three-layer framework is the quality gate that stands between a fitted model and a defensible decomposition. Without it, the posterior's credible intervals create false confidence: they tell you how uncertain the model is about its own parameters, not whether those parameters are causally correct.

Day 23 extends the validated model into competitor and portfolio whitespace analysis — where understanding which channels drive *relative* share versus category expansion becomes the central question.

**Sharp question:** Your holdout MAPE is 11.8% and the distribution coefficient agrees with the geo-test within 8%. How do you characterise this model to a CMO — and what single action would you take before the next budget cycle to strengthen it?

---

## Suggested Readings for Today

**Required (15 min):**
- Chan & Perry (2017), §3 "Model Validation and Calibration" — the source of the fit-vs-causality distinction used throughout this day; read the geo-experiment calibration sub-section carefully.

**Deep version:**
- Jin et al. (2017), §4 "Posterior Predictive Checks" — shows how PPC failures decompose by time period, useful for diagnosing promotional mis-specification in FMCG data.
- Gelman, A. & Hill, J. (2007). *Data Analysis Using Regression and Multilevel/Hierarchical Models*, Ch. 8 — the canonical treatment of model checking that distinguishes fit from structural validity; skim pp. 158–172.
- Danaher, P. & Rust, R. (1994). "Determining the Optimal Return on Investment for an Advertising Campaign." *European Journal of Operational Research* — early formal treatment of the gap between model fit and out-of-sample predictive validity in media response models.

---

## Navigation

← Previous: [Day 21 — PyMC-Marketing Pipeline](./day-21-pymc-marketing-pipeline.md)

→ Next: [Day 23 — Competitor & Portfolio Whitespace Analysis](./day-23-competitor-portfolio-whitespace.md)
