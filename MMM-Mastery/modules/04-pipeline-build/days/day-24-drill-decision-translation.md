# Day 24 - Drill: Decision Translation Under Uncertainty

> **Load-bearing idea:** The rate-limiting sub-skill in MMM is not model fitting — it is translating a posterior distribution over parameters into a defensible, uncertainty-aware business recommendation.

> **Before you start:** Recall Day 23 load-bearing idea — one sentence on what white space analysis measures and how CDI and BDI combine to identify it. Write it from memory before reading further.

---

Today is a gym session, not a lecture. Six exercises, each targeting a different muscle in the decision-translation chain: reading distributions, break-even arithmetic under uncertainty, budget reallocation, causal language for executives, spotting flawed reasoning, and the integrated brief. The exercises progress from isolated reps to compound lifts.

Work each exercise for **5–7 minutes cold** — no peeking — before opening the answer. Partial credit counts: if you get the direction right but miss a nuance, note the gap and move on. Speed is not the point; honest self-assessment is.

---

## Exercise 1 — Reading a Posterior

**Foundational | 5–7 min cold**

Surf Excel Pakistan MMM posterior for TV coefficient:

- Mean ROI = 2.3x
- SD = 0.8
- HDI 94% = [0.9x, 3.8x]
- Current TV spend: £2.4M

**(a)** State three things the posterior tells you that the mean alone does not.

**(b)** Should the brand **INCREASE**, **MAINTAIN**, or **DECREASE** TV spend? Explain using the posterior, not just the mean.

**(c)** What additional information would make you more confident in this decision?

<details>
<summary>Reference answer</summary>

**(a) Three things the posterior adds over the mean:**

1. **Downside exposure.** The HDI lower bound of 0.9x means there is meaningful posterior mass below 1.0x — the spend could be at or near breakeven. The mean of 2.3x does not reveal this tail risk.
2. **Asymmetry / skew.** The interval is wider on the upside (3.8 − 2.3 = 1.5) than on the downside (2.3 − 0.9 = 1.4), suggesting slight right skew. This matters when computing expected utility.
3. **Plausible range for scenario planning.** You can say "in a pessimistic but credible world the ROI is ~1x" — that is a business-usable claim. The mean alone forces you to act as if 2.3x is certain.

**(b) Recommendation: MAINTAIN with a test to increase.**

The mean ROI (2.3x) is above 1.0x, which is the breakeven threshold assuming media cost is the only investment. However, the HDI lower bound (0.9x) is below 1.0x, so a non-trivial share of the posterior supports near-breakeven returns. Doubling spend without further evidence would push the brand further into the uncertain tail of the response curve (diminishing returns via Hill). The correct posture is: hold current spend, design a geo-test or spend increment to tighten the posterior, and revisit in the next planning cycle. Do not decrease — the central mass is clearly positive and the mean is well above breakeven.

**(c) Information that would increase confidence:**

- Normalized current spend level relative to Hill K (are we in the linear or saturated region?)
- Results of a held-out period or geo-test validating the TV coefficient out-of-sample
- Prior MMM runs for Surf Excel Pakistan — is the posterior stable year-on-year or volatile?
- Competitor TV pressure — the coefficient conflates share-of-voice effects if share is not controlled

</details>

---

## Exercise 2 — Break-Even Under Uncertainty

**Quantitative | 5–7 min cold**

Price elasticity posterior for Surf Excel Pakistan:

- Mean = −1.7
- SD = 0.35
- HDI 94% = [−2.35, −1.05]
- Gross margin = 41%
- Proposed price increase = 5%

**(a)** Calculate the break-even volume loss percentage using the formula from Day 9.

**(b)** Calculate the expected volume loss at the posterior mean.

**(c)** Calculate the volume loss at the HDI upper bound (most negative, −2.35).

**(d)** At the HDI lower bound (−1.05), is the price increase profitable? What does this tell you about the risk profile?

**(e)** Write the CMO recommendation in exactly two sentences.

<details>
<summary>Reference answer</summary>

**(a) Break-even volume loss:**

```
break_even = price_inc% / (price_inc% + margin%)
           = 5% / (5% + 41%)
           = 5% / 46%
           = 10.87%
```

The brand can afford to lose up to **10.9%** of volume and still be revenue-neutral on gross profit.

**(b) Expected volume loss at mean elasticity (−1.7):**

```
volume_loss = |elasticity| × price_inc% = 1.7 × 5% = 8.5%
```

At the mean, the 8.5% volume loss is **below** the 10.9% break-even threshold — the price increase is profitable in expectation.

**(c) Volume loss at HDI upper bound (−2.35):**

```
volume_loss = 2.35 × 5% = 11.75%
```

At −2.35, the volume loss (11.75%) **exceeds** the break-even threshold (10.9%) — the price increase destroys margin in this region of the posterior.

**(d) HDI lower bound (−1.05):**

```
volume_loss = 1.05 × 5% = 5.25%
```

Well below break-even. The price increase is clearly profitable here. The spread from −1.05 to −2.35 means the outcome straddles the break-even point — the decision is genuinely uncertain. Roughly the upper quarter of the posterior (the most elastic region) produces a loss. This is a **moderate-risk** recommendation, not a safe one.

**(e) Two-sentence CMO recommendation:**

"A 5% price increase is profitable in expectation — projected volume loss of 8.5% sits below the 10.9% break-even threshold — but the 94% HDI includes elasticities under which the increase destroys margin, so we recommend a phased roll-out across two regions before national execution. If the geo-test confirms elasticity closer to −1.3 or better, full national implementation is warranted in Q3."

</details>

---

## Exercise 3 — ROI Ranking and Budget Reallocation

**Decision-making | 5–7 min cold**

Dove UK posterior mean ROI estimates. Total budget: £11.2M.

| Channel | ROI mean | HDI 94% | Current spend £M |
|---|---|---|---|
| TV | 2.1x | [1.4, 2.9] | 4.2 |
| Digital | 1.8x | [1.3, 2.4] | 1.9 |
| OOH | 0.9x | [0.4, 1.5] | 0.8 |
| Trade Promo | 1.4x | [0.9, 2.0] | 3.1 |
| Distribution | 2.8x | [1.8, 3.9] | 1.2 |

Recommend a reallocation. State: what to shift from, what to shift to, approximate amounts, and the single most important caveat to flag before implementing.

<details>
<summary>Reference answer</summary>

**Reallocation logic:**

Rank by ROI mean: Distribution (2.8x) > TV (2.1x) > Digital (1.8x) > Trade Promo (1.4x) > OOH (0.9x).

OOH HDI lower bound is 0.4x — substantial posterior mass below 1.0x breakeven. Trade Promo at 1.4x is the second weakest and the largest absolute spend after TV.

**Recommended shifts:**

- **Cut OOH entirely or reduce to £0.2M** — ROI mean below 1.0x is indefensible without a branding/awareness rationale; save ~£0.6M.
- **Reduce Trade Promo by £0.8M** (from £3.1M to £2.3M) — still the most spend-heavy channel at the weakest ROI among non-OOH channels; HDI lower bound of 0.9x suggests meaningful downside.
- **Increase Distribution by £1.0M** (from £1.2M to £2.2M) — highest mean ROI, HDI entirely above 1.0x; this is the most confident reallocation.
- **Increase TV by £0.4M** (from £4.2M to £4.6M) — solid ROI but watch for diminishing returns; TV normalized spend should be checked against Hill K before allocating further.

Net: ~£1.4M freed from OOH + Trade Promo reduction, redeployed to Distribution and TV. Total budget unchanged at £11.2M.

**Most important caveat:**

Distribution investment (hiring trade reps, logistics, retail activation) has a **response lag and cannot be switched on/off in-quarter** the way media can. The distribution coefficient measures the sales return per weighted distribution point once achieved — it does not capture the cost and time required to gain those points. Before approving the £1.0M increase, confirm with the trade team that the target WD uplift is actionable within the planning period, and validate the coefficient against the Day 17 DiD evidence (or flag if no causal validation exists).

</details>

---

## Exercise 4 — Causal Evidence Statement for a CMO Deck

**Communication | 5–7 min cold**

Knorr distribution initiative findings:

- DiD estimate: +9% volume for 6 WD points gained (geo-test, 8 weeks)
- MMM coefficient: 650 units per WD point, HDI 94% = [430, 870]
- Pre-initiative weekly volume: 38,000 units

Write the causal evidence statement for **Slide 4** of the CMO deck. Requirements:

- What was measured
- How it was validated
- Point estimate with uncertainty
- Recommendation

Maximum 5 sentences.

<details>
<summary>Reference answer</summary>

"We estimated the sales impact of gaining distribution using two independent methods: a geo-test (difference-in-differences across 12 matched city pairs) and the ongoing MMM. The geo-test showed that adding 6 weighted distribution points caused a 9% volume uplift — approximately 3,420 units per week at current base — over an 8-week horizon, giving us a causally identified estimate rather than a correlation. The MMM corroborates this: its distribution coefficient of 650 units per WD point (HDI 94%: 430–870) implies 3,900 units for 6 points, consistent with the geo-test within uncertainty bounds. We therefore treat distribution as a **causally validated** growth lever for Knorr, not merely an observed association. We recommend a phased expansion targeting 10 additional WD points in the South and East regions, prioritising outlets where Knorr's Rate of Sale is within 20% of the category benchmark to protect ROI."

**Marker notes:**
- Sentence 1: what was measured and how
- Sentence 2: DiD point estimate with implied units (causal claim)
- Sentence 3: MMM corroboration and consistency check
- Sentence 4: claim level (causally identified — Day 15 language)
- Sentence 5: recommendation with a guard condition

</details>

---

## Exercise 5 — Diagnosing Bad Recommendations

**Critical thinking | 5–7 min cold**

A brand team presents four decisions at the planning review:

1. "TV ROI is 3.2x so we should double TV spend." TV normalized spend is currently 0.88. Hill K = 0.6 (normalized).

2. "Price elasticity is −0.8 so a 12% price increase only costs 9.6% volume." Elasticity was estimated with OLS, no instrumental variable.

3. "Promotions have positive ROI so we should run 8 promotions per year." Post-promo dip lags were not included in the model.

4. "Distribution coefficient is significant so we should enter 10 new cities." Target cities have Rate of Sale 45% below the category benchmark.

For each: identify the specific error, name the Day that covers it, and state what corrected analysis is needed.

<details>
<summary>Reference answer</summary>

**Decision 1 — Doubling TV despite high normalized spend**

- **Error:** Ignoring saturation. Current normalized spend of 0.88 is above Hill K of 0.6 — the brand is already operating in the flattening region of the response curve. The quoted 3.2x ROI is the average historical return, not the marginal return at the next pound. Doubling spend into the saturated region will produce a substantially lower marginal ROI.
- **Day:** Day 8 (Hill saturation curve / adstock and diminishing returns).
- **Corrected analysis:** Compute marginal ROI at current spend = derivative of Hill function at x=0.88. If marginal ROI < 1.0x, recommend flat or reduced TV budget and reallocate to channels left of their K.

**Decision 2 — OLS elasticity without IV**

- **Error:** Price endogeneity. OLS elasticity is biased toward zero (less negative) because price and demand share unobserved common causes (trade promotions, competitor actions, seasonal demand). An elasticity of −0.8 from OLS likely understates price sensitivity; the true elasticity may be −1.3 to −1.8, which changes the break-even calculation materially.
- **Day:** Day 16 (instrumental variables for price endogeneity; palm oil / commodity cost instrument).
- **Corrected analysis:** Re-estimate with IV using a valid cost-side instrument (e.g., commodity input cost). Report IV elasticity and its HDI before re-running the break-even.

**Decision 3 — Promotions with omitted post-promo dip**

- **Error:** Omitted variable bias inflating promotional ROI. If post-promo volume dips (pantry loading / demand pull-forward) are not modelled as negative distributed lags, the model attributes the pre-promo spike to the promotion itself without netting off the demand borrowed from future weeks.
- **Day:** Day 11 (promotion mechanics, pantry loading, post-promo troughs).
- **Corrected analysis:** Add post-promo lag terms (weeks +1 to +4) to the model. Recompute net promotional ROI = total incremental volume over the full promotion window (including dip weeks), not just the uplift week.

**Decision 4 — Distribution expansion into low-RoS cities**

- **Error:** The distribution coefficient measures average return per WD point across the existing footprint. New cities with RoS 45% below benchmark are structurally different markets — the coefficient is out of sample and will overstate expected returns. High RoS is a prerequisite for distribution ROI: shelf presence only generates sales if shoppers already demand the product.
- **Day:** Day 12 (distribution elasticity, WD vs. RoS interaction) and Day 23 (BDI/CDI white space framework — low RoS signals low category development, not just low brand development).
- **Corrected analysis:** Segment the distribution coefficient by RoS quartile or use CDI/BDI analysis to identify cities where category demand is established. Restrict expansion to cities with RoS within 20% of benchmark; treat the 10 low-RoS cities as a separate development programme with different investment logic (awareness/trial, not distribution push).

</details>

---

## Exercise 6 — The Integrated Brief

**Compound lift | 5–7 min cold**

The Surf Excel CMO asks:

> "Give me the single most important thing our MMM tells us to do in the next 12 months and why you trust it."

Write a **200-word answer** covering:

- The specific driver
- The estimated effect with uncertainty
- How the estimate was validated (or flag explicitly if not)
- The causal claim level (use Day 15 language: identified / partially identified / associative)
- The one condition under which you would revise the recommendation

<details>
<summary>Reference answer</summary>

*Example answer — your numbers and driver will differ; check against the structure.*

---

"The single most important action is a **5% price increase in the core 1kg SKU**, executed in Q2 before the winter cooking season.

Our MMM estimates price elasticity at −1.7 (HDI 94%: −1.05 to −2.35). At this elasticity, a 5% increase produces an expected volume loss of 8.5% — comfortably below the 10.9% break-even threshold at 41% gross margin. The incremental gross profit is approximately £0.8M annually at current volumes.

**Trust level:** Partially identified. The elasticity was estimated with OLS; no instrumental variable has been applied to address price endogeneity. OLS tends to produce elasticities biased toward zero (less negative), meaning −1.7 may understate true price sensitivity. The decision remains robust unless the true elasticity is worse than −2.2, which requires the OLS bias to be unusually large.

**Validation gap to flag:** We have not run a geo-test on a price change for this SKU. We recommend a two-region price test in Q1 to generate a causally identified elasticity before national roll-out.

**Revision condition:** If the geo-test returns an elasticity more negative than −2.0, reduce the proposed increase to 3% and revisit with updated posterior."

---

**What a full-mark answer contains:**
- Named driver with SKU/market specificity
- Point estimate + HDI cited correctly
- Explicit acknowledgement of identification status (Day 15 framework)
- Honest validation gap stated — not glossed over
- A quantified revision trigger, not a vague "if conditions change"

</details>

---

## Navigation

← Previous: [Day 23 — Competitor Portfolio and White Space](./day-23-competitor-portfolio-whitespace.md)

→ Next: [Day 25 — ROI Curves and Budget Optimisation](../../05-business-decisions/days/day-25-roi-curves-budget.md)
