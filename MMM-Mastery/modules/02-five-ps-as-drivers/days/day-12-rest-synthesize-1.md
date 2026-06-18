# Day 12 — Rest & Synthesize I: Foundations + 5Ps Arc

> **Today:** No new concepts. Only consolidation of the Foundations and 5Ps arc (Days 1–11).
> **Format:** Re-state → Re-derive → Full run → Quiz
> **Reading time:** ~35 min (longer if gaps surface — that's the point)
> **Prereqs:** [Days 1–11](../../../README.md)

You have now covered the entire driver taxonomy: what MMM measures and doesn't, how data is structured, the base/incremental decomposition, the two media transformations, and all five Ps. The causal arc (Module 3) starts tomorrow and will feel qualitatively different — it requires you to already hold the 5Ps clearly in mind. Today is the checkpoint.

**Close all previous pages. Work from memory first.**

---

## Step 1 — Re-state the 11 Arc Ideas (5 min)

> **Why this table is scrambled:** Recall in day-order lets you use sequence as a cue. The scrambled order below forces you to retrieve each concept on its own merits.

Write one sentence for each without looking anything up. Do them in the order given.

| Concept | Your sentence |
|---------|--------------|
| People — trade execution (Day 11) | |
| Adstock carryover (Day 4) | |
| Base vs. incremental (Day 3) | |
| Pack — trial-loyalty tradeoff (Day 10) | |
| What MMM measures vs. causation (Day 1) | |
| Place — weighted vs. numeric distribution (Day 9) | |
| Saturation / Hill function (Day 5) | |
| Promotion — borrowed demand (Day 8) | |
| Data universe — Nielsen vs. Kantar vs. internal (Day 2) | |
| 5Ps framework — one sentence on each P (Day 6) | |
| Price — elasticity entanglement (Day 7) | |

<details>
<summary>Compare to these</summary>

1. **People (Day 11):** Shelf compliance and consumer segmentation are measurable People drivers that explain why identical Price/Promo/Place/Pack strategies produce different outcomes in different accounts; omitting them biases the Promotion coefficient upward.

2. **Adstock (Day 4):** $A_t = x_t + \lambda A_{t-1}$ — advertising effects decay geometrically; the variable entering regression is a weighted sum of all past spend, not current spend alone; mean lag = $\lambda/(1-\lambda)$ weeks.

3. **Base vs. incremental (Day 3):** Sales decompose into base (brand equity + distribution + trend/seasonality, present without current marketing) and incremental (attributed to each active driver); the base erosion trap is the most dangerous misread.

4. **Pack (Day 10):** Pack size changes shift price-per-use, not just price-per-unit; small packs recruit trial or liquidity-constrained loyal buyers; large packs deepen loyalty and facilitate stockpiling; the trial-loyalty distinction requires Kantar repeat-rate data to resolve.

5. **MMM vs. causation (Day 1):** MMM attributes observed sales to drivers via association (Rung 1 of the causal ladder); attribution ≠ causation; changing a driver may not produce the predicted sales change if the coefficient is biased by a confounder.

6. **Place (Day 9):** Numeric distribution = % of stores stocking; weighted distribution = % of category volume in stores stocking; WD drives base sales independently of advertising; distribution gains have a 3–6 week operational build lag.

7. **Saturation (Day 5):** $h(x) = x^\alpha / (x^\alpha + K^\alpha)$; $K$ is the half-saturation constant (response = 50% of max when spend = K); $\alpha \leq 1$ is concave, $\alpha > 1$ is S-shaped; response curve determines marginal ROI at any spend level.

8. **Promotion (Day 8):** Promo spikes contain true incremental, category expansion, and demand pull-forward components; pull-forward is dominant in storable FMCG; post-promo dip lags must be modelled to estimate true net promotional ROI.

9. **Data universe (Day 2):** Nielsen = sell-through (category view); Kantar = household panel (consumer view); internal = what the brand did and earned; use Nielsen volume as dependent variable, Nielsen ASP as price driver, Nielsen distribution as place driver.

10. **5Ps (Day 6):** Price (ASP/log, endogenous); Promotion (depth/flag, endogenous timing); Place (ND/WD, partial endogeneity); Pack (size dummy/index, low endogeneity); People (compliance/call frequency, hard to measure).

11. **Price (Day 7):** Price elasticity = % volume change per 1% price change; log-log specification gives elasticity directly; entangled with promotions (ASP conflates both) and pack mix (ASP falls when large packs dominate); relative price index is the cleaner specification.
</details>

---

## Step 2 — Re-derive the End-to-End Flow (10 min)

Fill in the blanks in the MMM data and modelling pipeline below — from raw data to decomposition output. Every blank is labelled with what it is asking for.

```
RAW DATA SOURCES:
  [A: name the three primary sources]
  ↓
DEPENDENT VARIABLE SELECTION:
  Use [B: which Nielsen metric?] as y — NOT [C: what common mistake?]
  ↓
DRIVER PREPARATION:
  Media variables: apply [D: transformation 1] then [E: transformation 2]
  Price variable: use [F: which specification?]
  Promotion variable: include [G: what additional variable to capture borrowed demand?]
  Distribution variable: apply [H: what lag structure?]
  ↓
MODEL ESTIMATION:
  [I: what is the name of the basic estimator used?]
  ↓
OUTPUT DECOMPOSITION:
  Sales = [J: two components] + Σ [K: form of each incremental term]
  ↓
BUSINESS READ:
  Base share tells you [L: one thing]
  Incremental share tells you [M: one thing]
  Base share alone CANNOT tell you [N: one thing — the base erosion trap]
```

<details>
<summary>Filled-in pipeline</summary>

- **A:** Nielsen (retail audit/sell-through), Kantar (household panel), Internal (finance + media + trade)
- **B:** Nielsen volume (units sold through retail)
- **C:** Internal net revenue or internal shipment data
- **D:** Geometric adstock ($A_t = x_t + \lambda A_{t-1}$)
- **E:** Hill saturation function ($h(A) = A^\alpha / (A^\alpha + K^\alpha)$)
- **F:** Log relative price index (brand ASP / category ASP)
- **G:** Post-promotion lag dummies (1, 2, 3 weeks after promotion flag)
- **H:** 3–6 week build lag for distribution gains
- **I:** OLS (Ordinary Least Squares); Bayesian in Module 4
- **J:** Base sales ($\hat{y}^{\text{base}}_t = \hat\alpha + \hat\beta_{\text{trend}} t + \hat\beta_s s_t$)
- **K:** $\hat\beta_k \cdot f(x_{kt})$ for each driver $k$
- **L:** The proportion of sales driven by brand equity and structural factors, independent of current marketing
- **M:** The historical association between each driver and sales (not causation)
- **N:** What would happen to base if all marketing stopped — base will erode over time as brand equity decays
</details>

**Questions to answer without looking:**

1. A Dove MMM reports base share of 55%. A junior analyst says "we can save 45% of our marketing budget." What are two specific reasons this is wrong?
2. A media channel has $\lambda = 0.85$. How many weeks until the adstock effect falls below 10% of its initial value?
3. Surf Excel is at adstock = 700; Hill parameters $K = 500$, $\alpha = 0.9$. Is the brand above or below half-saturation, and what does this mean for marginal ROI?

<details>
<summary>Answers</summary>

1. (i) Base erosion: cutting all marketing would erode base over time via brand equity decay — the 55% is not "free" revenue. (ii) The 45% incremental includes base-supporting effects of media (TV builds equity that shows up in base next year); cutting media today reduces next year's base.

2. Adstock remaining after $n$ weeks = $\lambda^n$. Solve $0.85^n = 0.10$: $n = \ln(0.10)/\ln(0.85) = (-2.303)/(-0.163) \approx 14$ weeks. The effect persists ~14 weeks — a much longer tail than most practitioners assume for TV.

3. $h(700) = 700^{0.9}/(700^{0.9} + 500^{0.9})$. At $x = K = 500$, $h = 0.5$. Since $700 > 500$, the brand is **above half-saturation** — past the midpoint of the curve. Marginal ROI is declining; each additional pound spent generates less than the last.
</details>

---

## Step 3 — Run / Apply It (10 min)

Walk through this checklist for any MMM dataset you have access to (or the Dove/Surf Excel hypothetical from Days 3–11). This is a hands-on diagnostic exercise — work through it with actual numbers if possible, mental arithmetic if not.

- [ ] **Dependent variable check:** Is the dependent variable Nielsen sell-through volume? If not, what is it and what bias does that introduce?
- [ ] **Price specification:** Is price entered as log relative price index? If ASP is used raw, does the model include a separate category price control?
- [ ] **Promotion dip:** Are there post-promotion lag variables (1–3 weeks)? What is the sum of the lag coefficients as a % of the promo coefficient?
- [ ] **Distribution:** Is WD or ND included? Is there a build lag? What share of sales is attributed to distribution?
- [ ] **Pack:** Is a pack index or pack dummy included? Is it collinear with price?
- [ ] **People proxy:** Is there any execution quality variable? If not, is there systematic unexplained variance in high-promotion periods that might reflect compliance differences?
- [ ] **Base share:** Is it stable across the time series, declining, or increasing? What is the business explanation?

If any step surfaces a gap, note it — that gap is your diagnostic for where the model's outputs need to be qualified in the CMO deck.

---

## Step 4 — Quiz (5 min)

> **Why the questions jump around:** Questions in day-order let you answer by position. Answer each cold.

1. A Surf Excel MMM shows promotional coefficient +0.65 with no post-promo lag variables. The promotion runs 20 weeks per year. Why is the reported promotional ROI almost certainly an overestimate, and in which direction is the error?

2. Knorr gains 6 WD points in Q2 but the MMM shows no significant distribution coefficient. Name two data issues that could explain this — one about measurement and one about model specification.

3. The Hill function for Dove TV has $K = 800$ adstock units and the brand currently runs at adstock = 400. A competitor runs at adstock = 1,200. Which brand is getting higher marginal ROI from TV, and why?

4. A Dove model omits the shelf compliance variable (People P). The distribution coefficient is estimated at $\hat\beta_{\text{WD}} = 590$ units per WD point. Is this estimate likely biased upward or downward, and by what mechanism?

5. **Cross-concept:** The Day 4 adstock parameter $\lambda$ and the Day 5 Hill function parameter $K$ are both estimated from historical data. Explain how the joint estimation of $\lambda$ and $K$ for a single media channel creates an identification problem — and name one piece of information outside the historical time series that would help resolve it.

<details>
<summary>Answers</summary>

1. Overestimate — the +0.65 coefficient absorbs both the genuine promotional uplift AND the post-promotion period, which, if below-baseline, would offset the uplift. Without dip variables, the net effect is the gross spike only. Error direction: upward (reported ROI is too positive). True net ROI = gross − dip; the dip is unmodelled and therefore mis-attributed to the baseline.

2. **Measurement:** Nielsen ND/WD sampling may be thin in the new distribution channel Knorr entered — the 6 WD point gain may not be accurately reflected in Nielsen weekly data (noise swamps signal). **Specification:** distribution may have been gained gradually over Q2 with a build lag — a contemporaneous specification misses the lagged volume build and assigns it to trend or media carryover.

3. **Dove** (adstock 400 < K = 800, below half-saturation) is getting higher marginal ROI — operating on the steeper part of the curve. The **competitor** (adstock 1,200 > K = 800, above half-saturation) is getting lower marginal ROI — operating on the flat, saturated part. This is a strategic opportunity for Dove to increase spend while the competitor is in a low-efficiency zone.

4. Biased **upward**. High-WD accounts are also the accounts with better Dove field sales coverage (Tesco, Sainsbury's) — they have higher execution quality. Omitting execution quality means the distribution coefficient absorbs the execution effect on top of the genuine distribution effect. The 590 estimate is larger than the true distribution-only effect.

5. **Cross-concept:** A high-$\lambda$, low-$K$ model and a low-$\lambda$, high-$K$ model can produce similar in-sample fit because they agree on total effect over the campaign window but disagree on timing and saturation level. The two parameters are not jointly identifiable from a single continuous time series without external variation. **Resolution:** a geo-test or media blackout experiment that cuts TV in one market provides variation in the adstock time path that is not present in the continuous spend series, allowing $\lambda$ and $K$ to be separately identified.
</details>

---

## What's Ahead

| Day | Topic | What the reader will decide or build |
|-----|-------|-------------------------------------|
| 13 | The Confounding Problem | Recognise why every P's coefficient is potentially biased |
| 14 | DAGs for MMM | Draw the causal structure behind any MMM before fitting |
| 15 | Identification Limits | Judge which effects can be estimated and which cannot |

Today's consolidation closes the "what are we measuring" arc and opens the "can we trust what we measured" arc. Every concept from Days 1–11 will be stress-tested in Module 3.

---

## Connect It Back

The 5Ps give you vocabulary. Module 3 gives you scepticism. The right attitude entering tomorrow is: *every coefficient I have learned to interpret could be wrong — and I am about to learn exactly how and why.* That scepticism, applied rigorously, is what separates a trustworthy MMM practitioner from a confident but wrong one.

**The question you carry into Module 3:** If you had to bet, which of the 5 Ps in your current (or most recent) MMM project has the most biased coefficient — and in which direction?

---

## Navigation

← **Previous:** [Day 11 — People: Trade Execution & Consumer Segments](./day-11-people-trade-execution.md)
→ **Next:** [Day 13 — The Confounding Problem](../../03-causal-structure/days/day-13-confounding-problem.md)
