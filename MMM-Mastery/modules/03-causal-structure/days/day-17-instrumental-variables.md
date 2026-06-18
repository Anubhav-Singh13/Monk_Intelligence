# Day 17 — Instrumental Variables: Solving Price Endogeneity (Surf Excel)

> **Today's one idea:** A valid instrument shifts price through a mechanism unrelated to demand — isolating the variation in price that is genuinely exogenous, and using only that variation to estimate the causal elasticity.
> **Reading time:** ~35 min · **Prereqs:** Days 15–16
> **Primary source for today:** Angrist & Pischke (2009), *Mostly Harmless Econometrics*, Chapter 4 — "Instrumental Variables in Action"
> **Before you start:** Recall Day 16's load-bearing idea — one sentence: what is the DiD estimate, and what assumption must hold for it to be causal?

---

## The Hook

The Surf Excel price elasticity problem in one sentence: price is set high when demand is high, so the OLS estimate is biased toward zero (or positive), making price look safer to raise than it truly is.

DiD won't fix this — there is no control group that faced a different price. We need a different strategy: find something that shifted Surf Excel's price for reasons completely unrelated to consumer demand. If we can find that, we can use *only that price variation* — the variation we know was not driven by demand — to estimate how volume responds.

That "something" is an **instrumental variable** (IV). The instrument is our key to the door that OLS cannot open.

---

## Building the Intuition

### The core idea: finding clean price variation

Imagine you could run an experiment: randomly set Surf Excel's price each week — sometimes £3.80, sometimes £4.20, sometimes £4.60 — with no regard for demand conditions. Then regress volume on price. The coefficient would be the causal elasticity, because price was determined by a random number generator, not by demand.

You cannot literally do that. But you can find variables in the real world that *behave like* a random price-setter for your purposes — things that shifted price for reasons unrelated to demand.

**Two candidates for Surf Excel price endogeneity:**

1. **Palm oil price index (input cost):** Surf Excel's production cost is heavily linked to palm oil prices. When palm oil prices spike (due to weather events in Malaysia, global commodity markets), Unilever faces pressure to raise price to protect margins — regardless of whether Surf Excel demand is high or low. Palm oil price variation is driven by agricultural and commodity factors, not by UK consumer demand for laundry detergent.

2. **Ariel price in unrelated markets:** If Procter & Gamble raises Ariel's price in Germany for reasons specific to German market conditions, and this price change propagates to UK pricing as part of European price management, the UK Ariel price change was not caused by UK consumer demand. Surf Excel, which competes with Ariel, may then adjust price — an adjustment triggered by cross-market competitor dynamics, not by UK demand.

Both candidates pass the first test for a valid instrument: they plausibly shift price. The second test — that they don't directly affect Surf Excel volume in the UK except through price — requires more scrutiny.

### The two conditions for a valid instrument

A variable $Z$ is a valid instrument for the endogenous variable $X$ (Price) in the model predicting $Y$ (Volume) if and only if:

```
Condition 1 — RELEVANCE:
  Z affects X (Price). If Z changes, Price changes.
  Formally: Cov(Z, Price) ≠ 0
  Test: regress Price on Z — is the coefficient significant?
  
Condition 2 — EXCLUSION RESTRICTION:
  Z has no direct effect on Y (Volume) except through X (Price).
  Z's only path to Volume runs through Price.
  Formally: Cov(Z, ε) = 0  where ε is the Volume error term
  Test: UNTESTABLE directly — requires economic argument
```

**Condition 1** is testable and can be checked empirically. A weak instrument (low correlation between Z and Price) produces IV estimates with very large standard errors — making the estimate useless in practice.

**Condition 2** is the leap of faith. You can construct an argument for it, but you cannot prove it from the data. This is why the exclusion restriction is stated explicitly in every IV analysis — it is an assumption, not a finding.

### Checking the palm oil instrument

**Relevance:** Regress Surf Excel ASP on palm oil price index + controls. Is the coefficient significant? If palm oil rises 10%, does Surf Excel price rise meaningfully (say, 3–5%)? This can be checked in data. ✓ (testable)

**Exclusion restriction:** Does palm oil price directly affect how much laundry consumers do — other than through the price of detergent? The argument: palm oil prices are a global commodity market phenomenon driven by weather, trade policy, and biofuel demand in Southeast Asia. UK consumers do not reduce washing frequency when palm oil prices rise, unless this passes through to Surf Excel shelf price. ✓ (defensible argument)

**Potential violation:** Palm oil price affects other household products too (cooking oil, personal care). If consumers buy Surf Excel, cooking oil, and Dove all from the same budget, a palm oil spike that raises prices across all these categories might reduce overall household FMCG spending — directly affecting Surf Excel volume via income effects, not just the price mechanism. This is a plausible exclusion restriction violation — flagged in the CMO deck, with sensitivity analysis.

---

## The Formal Picture

### Two-Stage Least Squares (2SLS): the IV estimator

The most common IV implementation is 2SLS, run in two stages:

**Stage 1 — Regress endogenous variable (Price) on the instrument (Z) and controls (W):**

```math
\text{Price}_t = \pi_0 + \pi_1 Z_t + \pi_2 \mathbf{W}_t + \nu_t
```

This produces $\hat{\text{Price}}_t$ — the predicted price, which uses only the variation in Price that is explained by the instrument (the "clean" variation).

**Stage 2 — Regress outcome (Volume) on the predicted price from Stage 1:**

```math
\ln(\text{Volume}_t) = \alpha + \eta^{IV} \cdot \hat{\text{Price}}_t + \gamma \mathbf{W}_t + \epsilon_t
```

The coefficient $\eta^{IV}$ is the IV estimate of price elasticity. Because $\hat{\text{Price}}_t$ was constructed from instrument variation only (not from demand variation), the endogeneity bias is removed — as long as the exclusion restriction holds.

In Python:

```python
from linearmodels.iv import IV2SLS
import pandas as pd

# df: weekly data with columns:
#   log_volume, log_price, log_palm_oil, log_ariel_price,
#   seasonality, trend, distribution_wd

model = IV2SLS(
    dependent=df['log_volume'],
    exog=df[['seasonality', 'trend', 'distribution_wd', 'const']],
    endog=df['log_price'],           # endogenous: own price
    instruments=df[['log_palm_oil', 'log_ariel_price_germany']]  # instruments
).fit(cov_type='robust')

print(model.summary)
# Key output: 
#   Coefficient on log_price = IV price elasticity (more negative than OLS)
#   First-stage F-statistic: should be > 10 (rule of thumb for instrument strength)
```

### Diagnosing instrument strength: the F-statistic rule

A weak instrument — one where $Z$ barely correlates with Price — produces IV estimates with enormous standard errors that span from −10 to +5. The instrument nominally identifies the elasticity, but the confidence interval is useless for business decisions.

The conventional diagnostic: **the first-stage F-statistic should exceed 10** (Staiger & Stock, 1997 rule of thumb). Below 10, the instrument is "weak" and the IV estimate may be severely biased toward the OLS estimate.

| First-stage F | Instrument strength | Recommendation |
|--------------|--------------------|-|
| > 100 | Strong | Proceed with IV estimate |
| 20–100 | Good | Proceed; report first-stage F |
| 10–20 | Adequate | Proceed with caution; consider LIML |
| < 10 | Weak | Do not use; find better instrument or accept OLS bias |

### Comparing OLS and IV estimates for Surf Excel

A well-designed IV analysis for Surf Excel might produce:

| Estimator | Price elasticity | 95% CI |
|-----------|-----------------|--------|
| OLS (naive) | −1.2 | [−1.45, −0.95] |
| IV (palm oil + Ariel Germany) | −2.1 | [−2.65, −1.55] |

The IV estimate is substantially more negative — confirming that OLS was biased toward zero by demand endogeneity. The true causal price response is stronger than the naïve model suggested. The brand manager who used −1.2 for pricing decisions was systematically underestimating price risk.

---

## Where It Breaks / What It Is Not

**"Any correlated variable is a valid instrument."** Relevance is necessary but not sufficient. The exclusion restriction is the hard condition — and the harder one to test. An instrument that is relevant but violates the exclusion restriction produces biased IV estimates that may be *worse* than OLS.

**"IV always gives a more negative price elasticity."** IV gives the elasticity for the compliers — consumers whose purchasing behaviour changed due to the instrument (those who would have changed volume because of an input-cost-driven price change). If cost-driven price changes are more or less acute in different market segments, the IV estimate may not equal the population average elasticity.

**"We can use multiple instruments to get more precision."** Using multiple instruments increases power but requires all of them to satisfy the exclusion restriction. If any instrument is invalid (violates exclusion), the combined IV estimator is biased. Always test instrument over-identification with a Sargan-Hansen J-test.

**"IV is the only way to fix price endogeneity."** A randomised price experiment (A/B test in a controlled retail setting) is often cleaner. IV is the observational data alternative when an experiment is not feasible. Day 26 revisits this choice when building the CMO pricing deck.

---

## Try It Yourself

> Close the page now before attempting Exercise 1.

**Exercise 1 — Retrieval.** Without looking: state the two conditions for a valid instrumental variable. For each, state whether it is testable from the data. Give one example of a valid instrument for Surf Excel price endogeneity.

<details>
<summary>Reference answer</summary>

**Condition 1 — Relevance:** $Z$ must be correlated with Price. Testable: yes — regress Price on $Z$; check significance and F-statistic.

**Condition 2 — Exclusion restriction:** $Z$ must not directly affect Volume except through Price. Testable: no — requires an economic argument that no direct path from $Z$ to Volume exists.

**Example instrument:** Palm oil price index. Relevance: palm oil is an input to Surf Excel production; cost increases pressure price upward. Exclusion: palm oil prices are driven by Southeast Asian agricultural conditions, not by UK consumer demand for laundry detergent.
</details>

---

**Exercise 2 — Direct application.** You are assessing palm oil price as an instrument for Surf Excel price. The first-stage regression of $\log(\text{Price})$ on $\log(\text{PalmOil})$ + controls gives:

- Coefficient on log(PalmOil): +0.12 (s.e. = 0.04), significant at 1%
- First-stage F-statistic: 8.7
- R² of Stage 1: 0.31

(a) Is this instrument strong enough to use? What is the risk if you proceed?
(b) You find a second instrument: Ariel's price in Germany (log). First-stage F with both instruments: 22.4. Should you use both? What additional test should you run?

<details>
<summary>Reference answer</summary>

(a) F-statistic = 8.7 < 10 — the instrument is weak. Risk: the IV estimator with a weak instrument is biased toward the OLS estimate. The confidence intervals will be very wide, and the estimate may not be meaningfully more reliable than OLS. Do not proceed with this instrument alone; find additional instruments to strengthen the first stage.

(b) F = 22.4 with both instruments is adequate — proceed. You should run a **Sargan-Hansen J-test (overidentification test)**: with two instruments and one endogenous variable, you have one over-identifying restriction. The J-test checks whether both instruments give the same IV estimate (if they don't, at least one instrument is invalid/violates exclusion restriction). A significant J-test is a warning that one instrument may be contaminated.
</details>

---

**Exercise 3 — Stretch (callbacks to Days 7 and 13).** Day 7 found Surf Excel price elasticity = −1.8 from a log-log OLS regression with relative price index. Day 13 explained that demand endogeneity biases the OLS estimate toward zero (less negative). Today's IV example gave −2.1.

(a) Is the gap between −1.8 and −2.1 consistent with the direction of bias predicted by Day 13?
(b) Using the Day 7 break-even formula, recalculate whether a 7% price increase is profitable using the IV elasticity of −2.1 instead of the OLS elasticity of −1.8. Gross margin = 38%.
(c) What does this imply for the business recommendation?

<details>
<summary>Reference answer</summary>

(a) Yes — Day 13 predicted positive bias (OLS less negative than truth). OLS gives −1.8; IV gives −2.1. The IV estimate is more negative, consistent with the predicted bias direction.

(b) OLS decision: −1.8 × 7% = 12.6% volume loss vs. 14.9% break-even → profitable (margin of 2.3 pp)
IV decision: −2.1 × 7% = 14.7% volume loss vs. 14.9% break-even → barely profitable (margin of only 0.2 pp)

(c) At IV elasticity, the 7% price increase is near the break-even threshold and extremely fragile to any competitive response from Ariel. A 0.3 pp improvement in Ariel's response behaviour would push it into loss-making territory. The recommendation changes from "proceed with confidence" to "conditional on Ariel not responding — high risk, consider a smaller increase (3–4%) with significantly higher margin of safety."

This is the most important output of IV in an MMM context: not just a more accurate elasticity, but a different business decision.
</details>

---

**Transfer — apply it:**

> In your domain, what is the endogenous variable you most want to estimate the causal effect of? Write one sentence proposing a potential instrument — then evaluate it against the two IV conditions. Is it relevant? Does it plausibly satisfy the exclusion restriction?

---

## Connect It Back

Days 13–17 gave you the full causal toolkit: identify the confounding mechanism (Day 13), map it in a DAG (Day 14), assess what can and cannot be identified (Day 15), use DiD for geographic effects (Day 16), and use IV for price endogeneity (Day 17). Tomorrow is the gym — Day 18 puts all of this under pressure with six progressive exercises. Show up prepared.

**Sharp question to carry forward:** The IV estimate of −2.1 is for the "local average treatment effect" (LATE) — the effect for consumers whose purchasing was actually affected by palm oil cost-driven price changes. Is this the same as the population average treatment effect (ATE)? Who might be excluded from the LATE that the brand manager actually needs to know about?

*(Consumers who are completely price-insensitive — loyal Dove/Surf Excel buyers who would never switch — are "never-takers" in IV language. The LATE excludes them. If the brand is targeting a price increase at exactly these loyal segments, the IV estimate understates the true stability of their demand.)*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:** Angrist & Pischke (2009), Chapter 4, Section 4.1 — "The IV Estimator and 2SLS." Read the derivation of the IV estimator and the explanation of why it fixes endogeneity — the algebra here is the cleanest treatment available.

**If you want the deep version:**
- Imbens & Angrist (1994), "Identification and Estimation of Local Average Treatment Effects," *Econometrica* — the original LATE paper. The concept that IV estimates the effect only for compliers (not the full population) is established here. Read after Day 17 has settled.
- Varian (2016), "Causal inference in economics and marketing," *PNAS* — Section 3 ("Natural experiments") covers both DiD and IV in the marketing context in 3 pages. Ideal for a combined review of Days 16–17.

---

## Navigation

← **Previous:** [Day 16 — Difference-in-Differences](./day-16-difference-in-differences.md)
→ **Next:** [Day 18 — Drill: Causal Structure](./day-18-drill-causal-structure.md)
