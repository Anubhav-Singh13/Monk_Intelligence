# Day 20 — Bayesian Priors as Business Knowledge

> **Today's one idea:** A Bayesian prior is not a guess — it is encoded domain knowledge; every MMM parameter has a prior stating what you believe before seeing the data, and the posterior is the data's update to that belief.
> **Reading time:** ~35 min · **Prereqs:** Day 15 (OLS endogeneity bias), Day 17 (IV estimation), Day 19 (causal toolkit decision tree)
> **Primary source for today:** Gelman, A., Carlin, J. B., Stern, H. S., Dunson, D. B., Vehtari, A., & Rubin, D. B. (2013). *Bayesian Data Analysis* (3rd ed.). CRC Press. Chapter 2.
> **Before you start:** Recall Day 19's load-bearing idea — one sentence, no looking. Specifically: what does it mean for a causal effect to be Level 3 (structurally unidentified), and what is the only path forward when you are there?

---

## The Hook

Two analysts run the same Dove UK MMM on the same 200 weeks of data.

**Analyst A** uses flat, uninformative priors. "Let the data speak," she says.

**Analyst B** encodes domain knowledge before touching a single row: price elasticity must be negative (law of demand); TV adstock lambda is likely 0.50–0.85 based on industry benchmarks; Hill saturation K should sit near the midpoint of observed spend.

Results:

| Parameter | Analyst A | Analyst B |
|---|---|---|
| Price elasticity | **+0.4** | **−1.6** |
| TV adstock lambda | 0.22 | 0.71 |
| TV contribution | 34% | 18% |

Analyst A's model says Dove UK demand *rises* as price increases. The finance team nearly uses this to justify a 15% price hike. A category manager catches it three weeks before the brief goes to market.

Same data. Completely different models. The prior was not decoration — it was the difference between a sensible model and a nonsense one.

---

## Building the Intuition

### What a prior actually is

A prior is a probability distribution over a parameter *before you look at the data*. It encodes what values you consider plausible, impossible, or likely.

Think of it as a dial with two extremes:

```
Completely flat                         Completely sharp
(uniform over all reals)                (point mass at one value)
     |___________________________|
     No prior knowledge         Complete certainty
```

Neither extreme is useful in MMM:

- **Flat prior** on price elasticity allows +0.4. It is not "neutral" — it actively permits economic nonsense, and with enough collinear data the likelihood will find it.
- **Point prior** at −1.5 says you already know the elasticity. No data can move it.

The practical sweet spot is a **weakly informative or informative prior** — narrow enough to rule out nonsense, wide enough to let the data speak within the plausible range.

### The prior predictive check — your sanity test

Before fitting any model, ask: *what volume does this prior imply, before seeing a single row of data?*

Sample parameters from the prior, run them through the model equations, and look at the distribution of implied sales.

```
Prior predictive check — Dove UK volume (units/week)

Analyst A (flat priors):
|         ████████████████████████████████████████
|     ████                                        ████
|   ██                                                ██
|  ─────────────────────────────────────────────────────
  −50,000       0       actual range       5,000,000

Analyst B (informative priors):
|              █████████████████
|           ███                 ███
|         ██                       ██
|  ─────────────────────────────────────────────────────
              40,000   80,000   160,000
              ↑ actual range sits here
```

If the prior predictive spans negative sales or 100× actual volume, the priors are wrong — regardless of what the posterior will eventually say.

### Three tiers of priors used in MMM

| Tier | What it encodes | Example |
|---|---|---|
| **Weakly informative** | Rules out nonsense, little else | `Normal(0, 1)` on log-scale coefficients |
| **Informative** | Encodes industry benchmarks | `Beta(8, 3)` for TV adstock lambda (mean ≈ 0.73) |
| **Expert / external** | Centres on a specific external estimate | IV estimate from Day 17 used as prior mean for price |

### The prior-posterior update — the full picture

The core Bayesian equation, expressed in words first:

> **Posterior ∝ Likelihood × Prior**

The posterior is what you believe about a parameter *after* seeing the data. It is a compromise between:

1. What the data says (likelihood)
2. What you believed beforehand (prior)

With **few observations**, the prior dominates. With **many observations**, the likelihood dominates and the posterior converges toward the maximum likelihood estimate.

This has a critical implication for MMM: with 200 weeks of data, the posterior will be pulled strongly toward the likelihood. Which means — and this is the key point — *if the likelihood is biased (endogeneity from Day 15), the posterior inherits that bias*. A prior centred at −1.5 cannot fully rescue a likelihood that is pulling toward +0.4 when you have 200 data points. Priors help; they do not fix identification.

---

## The Formal Picture

### Bayes' theorem for MMM parameters

```math
p(\theta \mid y) = \frac{p(y \mid \theta) \cdot p(\theta)}{p(y)}
```

Where:
- $\theta$ — the vector of model parameters (elasticities, adstock lambdas, Hill parameters, etc.)
- $y$ — the observed data (weekly volume, price, GRPs, distribution)
- $p(\theta)$ — **prior**: your belief about $\theta$ before seeing $y$
- $p(y \mid \theta)$ — **likelihood**: the probability of observing $y$ given parameters $\theta$
- $p(y)$ — **marginal likelihood** (normalising constant; in practice, MCMC samplers bypass this)
- $p(\theta \mid y)$ — **posterior**: your updated belief after seeing $y$

### Standard MMM prior table for Dove UK

| Coefficient | Prior distribution | Parameters | Business rationale |
|---|---|---|---|
| TV adstock lambda ($\lambda$) | `Beta(8, 3)` | mean ≈ 0.73, SD ≈ 0.12 | Industry norm: TV carryover 3–4 weeks; most effect gone by week 5 |
| TV Hill alpha ($\alpha$) | `HalfNormal(1)` | positive-only | Concave response expected; diminishing returns to GRPs |
| TV Hill K | `HalfNormal(1)` on normalised spend | centred near spend midpoint | Saturation inflection near historical midpoint of media investment |
| TV beta ($\beta_{TV}$) | `HalfNormal(0.5)` | positive-only | Media cannot reduce sales (sign constraint) |
| Price elasticity ($\beta_P$) | `Normal(−1.5, 0.5)` | mean −1.5, 95% CI ≈ [−2.5, −0.5] | FMCG industry range −0.8 to −2.5; negative by law of demand |
| Weighted distribution ($\beta_{WD}$) | `HalfNormal(0.3)` | positive-only | More distribution = more volume |
| Promotion depth ($\beta_{promo}$) | `HalfNormal(0.3)` | positive-only | Promotions lift volume |
| Base intercept | `Normal(log(mean_volume), 0.5)` | centred on log of actual mean weekly volume | Anchors the model to the observed scale |
| Likelihood noise ($\sigma$) | `HalfNormal(0.2)` | positive-only | Residual variation as fraction of fitted value |

### PyMC model definition

```python
import pymc as pm
import numpy as np
import pandas as pd

# Assume df has columns: volume, price_index, grps_tv, wd, promo_depth
# All continuous inputs should be normalised to [0, 1] or log-scaled before this block

with pm.Model() as dove_mmm:

    # --- Adstock parameters ---
    lam_tv = pm.Beta("lam_tv", alpha=8, beta=3)          # decay rate, mean ~0.73

    # --- Saturation (Hill) parameters ---
    alpha_tv = pm.HalfNormal("alpha_tv", sigma=1)         # shape; >1 = S-curve, <1 = concave
    K_tv     = pm.HalfNormal("K_tv", sigma=1)             # half-saturation on normalised spend

    # --- Coefficient priors ---
    beta_tv    = pm.HalfNormal("beta_tv", sigma=0.5)      # TV contribution (positive)
    beta_price = pm.Normal("beta_price", mu=-1.5, sigma=0.5)  # price elasticity (negative-centred)
    beta_wd    = pm.HalfNormal("beta_wd", sigma=0.3)      # distribution effect
    beta_promo = pm.HalfNormal("beta_promo", sigma=0.3)   # promotion lift

    # --- Intercept and noise ---
    intercept = pm.Normal("intercept",
                          mu=np.log(df["volume"].mean()),
                          sigma=0.5)
    sigma = pm.HalfNormal("sigma", sigma=0.2)

    # --- Adstock transformation (geometric) ---
    # At = grps_t + lambda * At-1
    # PyMC-Marketing provides pm.mmm.geometric_adstock(); shown manually here for clarity
    grps = df["grps_tv"].values
    adstocked = np.zeros(len(grps))
    adstocked[0] = grps[0]
    for t in range(1, len(grps)):
        adstocked[t] = grps[t] + lam_tv * adstocked[t - 1]
    # Note: in PyMC, use pm.math.scan or PyMC-Marketing's built-in for gradient-compatible adstock

    # --- Hill saturation ---
    x_norm = adstocked / adstocked.max()  # normalise to [0,1]
    hill_tv = x_norm**alpha_tv / (x_norm**alpha_tv + K_tv**alpha_tv)

    # --- Linear predictor (log-log for price, additive for rest) ---
    mu = (intercept
          + beta_tv * hill_tv
          + beta_price * np.log(df["price_index"].values)
          + beta_wd * df["wd"].values
          + beta_promo * df["promo_depth"].values)

    # --- Likelihood ---
    volume_obs = pm.Normal("volume",
                           mu=mu,
                           sigma=sigma,
                           observed=np.log(df["volume"].values))
```

### Prior predictive check

Run this *before* fitting. If the implied volume range is wildly off, fix the priors — not the model structure.

```python
with dove_mmm:
    prior_pred = pm.sample_prior_predictive(samples=500, random_seed=42)

# Extract prior-implied volume (on log scale, so exponentiate)
prior_log_vol = prior_pred.prior_predictive["volume"].values.flatten()
prior_vol = np.exp(prior_log_vol)

print(f"Prior implied volume — 5th/95th pct:  [{np.percentile(prior_vol, 5):.0f}, "
      f"{np.percentile(prior_vol, 95):.0f}]")
print(f"Actual volume — min/max:               [{df.volume.min():.0f}, {df.volume.max():.0f}]")

# Target: prior 5th–95th pct should bracket actual min–max with moderate slack
# If prior range is 10× or more off from actual, tighten the intercept sigma
```

Expected output for a well-specified model:

```
Prior implied volume — 5th/95th pct:  [31,200, 198,400]
Actual volume — min/max:              [48,300, 142,700]
```

If you see `Prior implied volume — 5th/95th pct: [0, 12,400,000]`, the flat intercept prior is the culprit.

---

## Where It Breaks / What It Is Not

**"Bayesian priors fix endogeneity."**
No. The prior constrains the parameter *space* — it does not remove the bias in the *likelihood*. With 200 weeks of data, the posterior will be pulled strongly toward the biased OLS estimate regardless of where the prior is centred. Day 15's identification problems live in the likelihood function. Day 17's IV correction lives in the data-generating structure. Priors operate on a different level entirely. A `Normal(−1.5, 0.5)` prior on price elasticity when OLS gives +0.4 will produce a posterior around −0.2 with 200 observations — not −1.5.

**"Uninformative (flat) priors are safer / more objective."**
Flat priors on log-scale parameters are not neutral — they imply extreme volumes in the prior predictive. A `Normal(0, 100)` intercept on log-volume is not "no assumption"; it is an assumption that weekly volume could plausibly be e^100 ≈ 10^43 units. Priors that seem vague on the parameter scale are often wildly informative on the outcome scale. Run the prior predictive check. Always.

**"Set the prior mean to the OLS estimate."**
Dangerous if OLS is biased. Using a biased OLS estimate as the centre of an informative prior encodes that bias permanently into the posterior. Use external evidence: industry benchmarks, IV estimates from Day 17, published meta-analyses of FMCG elasticities — not the naive regression from the same data you are about to fit.

**"The posterior is always between the prior and the likelihood."**
True in one dimension, misleading in high dimensions. In multi-parameter models, the posterior can land in regions that neither the prior nor the likelihood peak alone would suggest, because the joint distribution involves interactions across parameters. This is why prior predictive checks on the *outcome* (volume) are more reliable than checking each parameter prior individually.

---

## Try It Yourself

### Exercise 1 — Retrieval

Close this page. Write one sentence each defining: **prior**, **likelihood**, **posterior**. Do not use "Bayesian" or "probability" as crutches — explain what each term *does* in the context of an MMM model. Open the page only after you have written all three.

<details>
<summary>Reference answer</summary>

**Prior:** A distribution over a model parameter — such as price elasticity — encoding what values you consider plausible based on domain knowledge and external evidence, before looking at any data from this specific dataset.

**Likelihood:** The probability of observing the actual data (weekly volumes) given a particular set of parameter values; it is the component that carries the signal from the data and, in the presence of endogeneity, the component that carries the bias.

**Posterior:** The updated distribution over the parameter after combining the prior with what the data says via the likelihood; it is the quantity you actually use for inference and scenario planning.

</details>

---

### Exercise 2 — Direct application

A brand team tells you: "Industry benchmarks suggest Surf Excel TV adstock decays over 2–3 weeks, with most of the effect gone by week 4."

**Your task:** Translate this into a `Beta(α, β)` prior for the geometric adstock lambda $\lambda$. Show your working: what mean and variance does this description imply, and how do you back out $\alpha$ and $\beta$?

*Hint: for a geometric adstock with decay $\lambda$, after $k$ weeks the remaining effect is $\lambda^k$. "Most of the effect gone by week 4" means $\lambda^4 \lesssim 0.1$, so $\lambda \lesssim 0.56$.*

<details>
<summary>Reference answer</summary>

**Step 1 — translate the verbal description into a constraint.**

"Decays over 2–3 weeks" → half-life around week 2–3, so $\lambda^2 \approx 0.5$, giving $\lambda \approx 0.71$.
"Most of the effect gone by week 4" → $\lambda^4 \lesssim 0.15$, giving $\lambda \lesssim 0.62$.

A reasonable prior mean: $\mu \approx 0.60$–$0.65$. Use $\mu = 0.62$.

**Step 2 — choose variance.**

We want the 95% CI to stay roughly within [0.40, 0.80] — plausible for a faster-decaying detergent brand vs. slower-decaying premium personal care. That implies SD ≈ 0.10.

**Step 3 — back out Beta parameters.**

For a Beta distribution: $\mu = \alpha / (\alpha + \beta)$ and $\sigma^2 = \mu(1-\mu)/(\alpha+\beta+1)$.

From $\mu = 0.62$: $\alpha = 0.62(\alpha+\beta)$.

From $\sigma^2 = 0.01$: $\alpha + \beta + 1 = \mu(1-\mu)/\sigma^2 = 0.62 \times 0.38 / 0.01 = 23.6$, so $\alpha + \beta \approx 22.6$.

Therefore $\alpha = 0.62 \times 22.6 \approx 14.0$ and $\beta \approx 8.6$.

**Result:** `Beta(14, 9)` — mean 0.61, 95% CI approximately [0.39, 0.80]. This is tighter than the Dove prior (`Beta(8, 3)`, mean 0.73) because the briefing explicitly stated faster decay.

```python
lam_surf = pm.Beta("lam_surf", alpha=14, beta=9)  # mean ~0.61, SD ~0.10
```

</details>

---

### Exercise 3 — Stretch (callback to Day 15)

You set a price elasticity prior of `Normal(−1.5, 0.5)`. Your IV estimate from Day 17 is −2.1. Your OLS estimate (subject to endogeneity bias) is −0.9. You have 200 weeks of data.

**Three questions:**

(a) With 200 observations, will the posterior be closer to the prior mean (−1.5), the OLS estimate (−0.9), or the IV estimate (−2.1)? Explain the mechanism.

(b) What does this imply about the strategy "just put a strong negative prior and the endogeneity problem goes away"?

(c) How should you use the IV estimate from Day 17 here — not to replace Bayesian estimation, but to inform it?

<details>
<summary>Reference answer</summary>

**(a)** With 200 observations, the likelihood dominates the prior. The posterior will be pulled toward the OLS estimate (approximately −0.9 to −1.1), not toward −1.5 or −2.1. The prior provides a regularising nudge but cannot overcome 200 data points of biased likelihood signal. If OLS gives −0.9 due to endogeneity, the posterior will land around −1.0 to −1.1 — better than −0.9, but far from the true −2.1.

**(b)** The strategy fails. The prior constrains the parameter space, but the likelihood carries the endogeneity bias into the posterior. Even a very tight prior — say `Normal(−2.1, 0.1)` — would be overwhelmed by 200 observations of biased likelihood. Endogeneity is a *structural problem in the data-generating process* (Day 15). Priors operate downstream of that structure.

**(c)** Use the IV estimate as the prior centre, not just a single number to reference:

```python
# IV estimate: beta_price_iv = -2.1, standard error from IV regression = 0.4
# Use this as an informative prior informed by the IV result
beta_price = pm.Normal("beta_price", mu=-2.1, sigma=0.4)
```

This is not the same as fixing the parameter at −2.1. It tells the model: "external causal evidence points to −2.1; move away from this if the likelihood is very strong, but start here." If the OLS likelihood then pulls the posterior toward −1.2, that tension is diagnostic — it suggests either the IV has its own instrument validity concerns (Day 17) or the time-series data contains confounders the IV did not address.

The correct workflow: run IV first (Day 17) → use IV estimate as prior centre → check if posterior is consistent with IV posterior → if strongly inconsistent, investigate why.

</details>

---

> **Transfer — apply it:** In your domain, before fitting tomorrow's PyMC model (Day 21), write down one parameter for your chosen Unilever brand and the prior distribution you would assign it — include the specific distribution family, parameters, and the one-sentence business rationale that justifies those parameters.

---

## Connect It Back

Yesterday (Day 19) closed the causal structure module with a decision tree for choosing identification strategies — DiD, IV, or structural modelling depending on what level of causal guarantee your setting supports. Today's priors are the mechanism by which the business knowledge accumulated across all of Module 3 enters the model formally. The DAG from Day 14 told you the confounders. The IV from Day 17 gave you an elasticity estimate free of endogeneity bias. Today you saw how to encode both — the sign constraint, the plausible range, the external benchmark — as probability distributions that sit on top of the likelihood, not as hard constraints that override it.

Tomorrow (Day 21) these prior distributions become the first block of a complete PyMC-Marketing pipeline: prior specification, adstock transformation, Hill saturation, likelihood, and posterior sampling in a single model object.

**Sharp question you can now answer:** Why does it matter that priors are set *before* looking at the data — not after seeing what the OLS estimates are and then constructing a prior to push back against them?

---

## Suggested Readings for Today

**Required — if you have 15 extra minutes:**
Gelman et al. (2013), *Bayesian Data Analysis* (3rd ed.), Chapter 2, Sections 2.1–2.4. Read the single-parameter normal example (Section 2.4) and notice the explicit algebra showing how the posterior mean is a precision-weighted average of the prior mean and the data mean. This is the formal version of "200 observations pull the posterior toward the likelihood."

**Deep version:**

1. Gelman, A., & Shalizi, C. R. (2013). "Philosophy and the practice of Bayesian statistics." *British Journal of Mathematical and Statistical Psychology*, 66(1), 8–38. DOI: 10.1111/j.2044-8317.2012.02063.x — Directly addresses the "uninformative priors are objective" fallacy that Analyst A committed. Read Section 3.

2. Jin, Y., Wang, Y., Sun, Y., Chan, D., & Koehler, J. (2017). "Bayesian Methods for Media Mix Modeling with Carryover and Shape Effects." Google Research Technical Report. Available: https://research.google/pubs/pub46001/ — The paper that formalized geometric adstock and Hill saturation inside a Bayesian framework. Section 3 covers the exact priors used in production MMM at Google; compare their choices to the table in today's formal picture.

3. McElreath, R. (2020). *Statistical Rethinking: A Bayesian Course with Examples in R and Stan* (2nd ed.). CRC Press. Chapter 4, Section 4.3 ("Gaussian model of height") — The clearest worked example of prior predictive simulation I have found anywhere. The logic translates directly to MMM volume.

---

## Navigation

← **Previous:** [Day 19 — Rest & Synthesize II](../../03-causal-structure/days/day-19-rest-synthesize-2.md)
→ **Next:** [Day 21 — PyMC-Marketing Pipeline](./day-21-pymc-marketing-pipeline.md)
