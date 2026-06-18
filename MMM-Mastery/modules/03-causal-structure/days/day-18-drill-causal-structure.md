# Day 18 — Drill: Causal Structure

> **Today:** Six progressive exercises on DAGs, backdoor paths, identification, DiD design, IV design, and MMM output diagnosis. No new concepts. Expect discomfort.
> **Reading time:** ~40 min (more if you get stuck — that is the point)
> **Prereqs:** Days 13–17
> **Before you start:** Do NOT re-read any prior pages before attempting. Work from memory. Retrieve first, check after.

---

This is a gym session. Every exercise tests a specific sub-skill from the causal arc. They are progressive — each one requires something the previous ones established. Work through them in order. Set a timer: aim for 5–7 minutes per exercise before consulting the answer.

---

## Exercise 1 — DAG Construction (Foundational)

**Scenario:** Knorr Soups runs a TV campaign in Q1. The following variables are measured: Knorr TV Spend, Knorr Volume, Knorr ASP (price), Category Volume (total soups category), Ariel TV Spend (competitor in the same category store space), Unobserved Consumer Demand Conditions.

Draw the DAG. Write out all edges as "A → B" pairs. Then identify:
(a) All direct causes of Knorr Volume
(b) All backdoor paths from TV Spend to Knorr Volume
(c) Which backdoor paths are blockable using observed variables?

<details>
<summary>Reference answer</summary>

**DAG edges:**
- Consumer Demand → Knorr Volume (demand drives purchasing)
- Consumer Demand → Knorr ASP (Knorr prices higher when demand is high)
- Consumer Demand → Category Volume (demand drives category)
- Category Volume → Knorr Volume (brand volume is part of category)
- Knorr TV Spend → Knorr Volume (what we want to measure)
- Knorr TV Spend → Category Volume (TV expands category slightly)
- Knorr ASP → Knorr Volume (price effect)
- Ariel TV Spend → Category Volume (Ariel's TV also grows category)
- Ariel TV Spend → Knorr Volume (cross-brand awareness effect — small)

Note: there is no arrow from Ariel TV → Knorr TV (they are independent decisions, correlated only through unobserved seasonal media planning norms, which we simplify away here).

**(a) Direct causes of Knorr Volume:** Consumer Demand, TV Spend, Knorr ASP, Category Volume, Ariel TV Spend

**(b) Backdoor paths from TV Spend to Knorr Volume:**
Only through Knorr TV Spend ← [something] — but in this DAG, TV Spend has no parents (no node causes TV Spend). Therefore, **there are no backdoor paths** from TV Spend to Knorr Volume. TV Spend is exogenous in this model.

**(c)** N/A — no backdoor paths to block. The TV coefficient is identified by controlling for the other variables. Caveat: the DAG assumes TV spend timing is not driven by consumer demand — if Knorr deliberately spends more TV in high-demand periods, this assumption fails and a TV ← Demand → Volume backdoor path exists.
</details>

---

## Exercise 2 — Identifying the Problematic P

**Scenario:** You have an MMM for Dove Body Wash with the following variables: TV adstock (Hill-transformed), Digital adstock (Hill-transformed), Promotions (depth), Numeric Distribution, Shelf Compliance Index, Seasonality, Trend.

For each of the following coefficients, state: (a) its most likely endogeneity risk; (b) the direction of the resulting OLS bias; (c) whether it is a Level 1, 2, or 3 identification problem (using Day 15's classification).

| Variable | Endogeneity risk? | Bias direction | Level |
|---------|------------------|----------------|-------|
| TV adstock | ? | ? | ? |
| Promotions | ? | ? | ? |
| Numeric Distribution | ? | ? | ? |
| Shelf Compliance | ? | ? | ? |

<details>
<summary>Reference answer</summary>

| Variable | Endogeneity risk | Bias direction | Level |
|---------|-----------------|----------------|-------|
| TV adstock | Low — TV schedules are planned months ahead and do not respond to short-run demand. Small risk if campaigns are timed to seasonal peaks. | Slightly upward (seasonal timing) | Level 1 — blockable by including seasonality controls |
| Promotions | Moderate — promotions are timed to slow periods (endogenous to demand weakness) | Downward (runs in weak periods → volume looks lower than it would be in strong periods) | Level 2 — partially fixable with timing controls or IV |
| Numeric Distribution | Moderate — distribution expanded in high-growth markets first | Upward (distribution looks more effective than it is because it was deployed in already-growing markets) | Level 2 — partially fixable with category trend controls and geo controls |
| Shelf Compliance | Low endogeneity, but hard to measure — the proxy (WD/ND ratio) may conflate execution quality with store mix changes | Imprecise rather than biased — the measurement error in the proxy attenuates the coefficient toward zero | Level 2 — better measurement reduces the problem |
</details>

---

## Exercise 3 — Backdoor Path Blocking

**Scenario:** You want to estimate the causal effect of Surf Excel Price on Volume. Your DAG has the following nodes: Price, Volume, Promotion, Category Demand (unobserved), Ariel Price, Seasonality, Household Income Index (quarterly, observable).

The following paths run from Price to Volume:

1. Price → Volume (direct effect — what you want)
2. Price ← Category Demand → Volume (backdoor via unobserved demand)
3. Price ← Seasonality → Volume (backdoor via seasonality — observed)
4. Price ← Ariel Price → Volume (Ariel prices together with Surf Excel; Ariel also directly affects Surf Excel volume)
5. Price → Promotion → Volume (Promotion mediates Price: when price rises, promotional support often also rises to defend volume)

For each path:
(a) Is it a direct path, backdoor path, or mediated path (Price causes the mediator which causes Volume)?
(b) Should you block/control for it? Why or why not?
(c) What variable would you add/remove?

<details>
<summary>Reference answer</summary>

1. **Direct path (Price → Volume):** This is the causal effect you want to estimate. Do not block it. No change to model.

2. **Backdoor via unobserved demand (Price ← Category Demand → Volume):** Backdoor path — creates confounding bias. Cannot be blocked by adding a control (Category Demand is unobserved). Requires IV or experiment. → Need palm oil index or Ariel Germany price as instrument.

3. **Backdoor via seasonality (Price ← Seasonality → Volume):** Backdoor path — blockable. Add seasonality dummies or a seasonal index to the regression. → Include quarterly seasonal dummies. ✓

4. **Path via Ariel Price (Price ← Ariel Price → Volume):** This is a fork: Ariel Price is a common cause of both Surf Excel Price (competitors adjust together) and Surf Excel Volume (cross-price elasticity). Backdoor path: Price ← Ariel Price → Volume. Block by including Ariel ASP as a control variable. → Include Ariel ASP. ✓

5. **Mediated path (Price → Promotion → Volume):** Promotion is a **mediator** — it is caused by Price and also causes Volume. If you control for Promotion, you block this mediated path and estimate only the *direct* effect of Price on Volume (not through the promotional response). Whether to control for Promotion depends on your question: if you want the total effect of a price change (including promotional support response), do NOT control for Promotion. If you want only the direct price-to-consumer effect, DO control. → Decision: for a pricing CMO deck, do NOT control for Promotion — the total effect (including brand's own promotional response) is more relevant.
</details>

---

## Exercise 4 — DiD Design

**Scenario:** Knorr wants to test a new "Taste Ambassador" programme — a salesforce initiative where dedicated brand ambassadors visit restaurants and food service outlets in a region to promote Knorr Stock products. They plan to launch in Manchester (population: 2.8M) in Q2.

Design the DiD study:

(a) What is the treatment? What is the control group? Justify your control group choice.
(b) What is the outcome variable? Where does it come from?
(c) State the parallel trends assumption in plain English for this specific case.
(d) How would you test the parallel trends assumption? Be specific about data and time window.
(e) Name one SUTVA violation risk and how you would mitigate it.

<details>
<summary>Reference answer</summary>

**(a) Treatment:** Taste Ambassador programme launched in Manchester in Q2.
**Control group:** Other comparable UK cities not receiving the programme. Best candidates: Leeds, Sheffield, Liverpool (similar size, similar food service density, similar Knorr distribution base). Avoid London (too different in food service culture and size). Avoid cities where Knorr has announced other initiatives in Q2.

**(b) Outcome variable:** Knorr Stock product volume in food service channel (Nielsen or internal shipment data by geography). Secondary outcome: Knorr distribution (ND/WD in food service outlets by city).

**(c) Parallel trends in plain English:** "In the absence of the Taste Ambassador programme, Knorr's food service volume in Manchester would have grown at the same rate as in Leeds, Sheffield, and Liverpool over Q2–Q3." This is plausible if: all cities faced similar category trends; no other marketing initiatives launched specifically in Manchester in Q2; Manchester's food service sector was not undergoing unusual structural change.

**(d) Pre-trend test:** Plot weekly Knorr food service volume in Manchester vs. control cities for 6–8 quarters before Q2 launch. If the city-level differences in growth rates are stable (Manchester does not systematically outperform or underperform the control group), the assumption is plausible. Run a regression with "Manchester × pre-period time trend" — if the coefficient is near zero and insignificant, parallel trends holds in the pre-period.

**(e) SUTVA violation risk:** Manchester Taste Ambassadors may visit restaurants near the Manchester-Leeds border whose supply and awareness effects spill into the Leeds control market. Mitigation: define Manchester as the core city only (not the metropolitan area) and exclude Leeds postcodes within 10 miles of the boundary from the control group.
</details>

---

## Exercise 5 — IV Validity Judgement

**Scenario:** You are evaluating four proposed instruments for Surf Excel price endogeneity. For each, evaluate relevance and exclusion restriction. State whether it is valid, invalid, or uncertain (requires more investigation), and explain why.

| Instrument | Relevance | Exclusion restriction | Valid? |
|-----------|-----------|----------------------|--------|
| A. Palm oil price index | ? | ? | ? |
| B. Surf Excel promotional flag (own brand) | ? | ? | ? |
| C. Ariel price in the Netherlands | ? | ? | ? |
| D. UK electricity price index | ? | ? | ? |

<details>
<summary>Reference answer</summary>

| Instrument | Relevance | Exclusion restriction | Valid? |
|-----------|-----------|----------------------|--------|
| **A. Palm oil price index** | Strong — palm oil is a major input; price pressure passes through to consumer price | Plausible — palm oil prices are driven by agricultural conditions in SE Asia, not UK consumer demand for laundry. Caveat: income effect if palm oil affects cooking oil prices too | **Likely valid — use with income effect caveat** |
| **B. Surf Excel promotional flag** | Relevant — promotions mechanically reduce ASP | **Violated** — promotions directly affect volume through multiple channels (display, consumer psychology, stockpiling) beyond price alone. Promotion → Volume is a direct path independent of price. | **INVALID** |
| **C. Ariel price in Netherlands** | Moderate — P&G European pricing is coordinated; Netherlands moves may lead/lag UK. Check F-statistic. | Plausible — Netherlands market conditions do not directly affect UK consumer demand. Risk: if P&G prices all European markets in response to the same global signals (commodity costs), the instrument may be correlated with UK demand through that shared signal. Run Sargan-Hansen J-test against palm oil instrument. | **Uncertain — use alongside palm oil; run J-test** |
| **D. UK electricity price index** | Weak — electricity cost is a small fraction of Surf Excel production. May have no significant first-stage effect. Check F-statistic. | Plausible in principle — electricity prices driven by energy markets, not detergent demand. But relevance failure makes it useless. | **INVALID (weak instrument)** |
</details>

---

## Exercise 6 — Integrated Diagnosis (Hardest)

**Scenario:** A client presents an MMM for Surf Excel in Pakistan with these key results:

- Price elasticity: −0.9 (OLS, no IV)
- TV ROI: 2.8× (adstock + Hill applied, no DiD validation)
- Distribution coefficient: +890 units per ND point (distribution expanded in urban Lahore, Karachi only)
- Promotional coefficient: +0.55 (no post-promo lags modelled)
- Base share: 42% (declining 2 pp/year)

The client wants to use these numbers to: (1) justify a 10% price increase; (2) double TV spend; (3) enter 5 new rural cities with distribution investment; (4) increase promotional frequency.

For each recommendation, write a one-paragraph response identifying: (a) the identification problem with the supporting coefficient; (b) the direction of error if the recommendation proceeds; (c) what validation or correction is needed before you would endorse it.

<details>
<summary>Reference answer</summary>

**(1) Price increase 10%:** The price elasticity of −0.9 is biased toward zero due to demand endogeneity (price is set high in high-demand periods in Pakistan's premium FMCG market). True elasticity is likely −1.4 to −2.0. At −0.9, a 10% price increase predicts 9% volume loss; at −1.7 it predicts 17% volume loss. The break-even at 38% margin is 20.8% — at −0.9 the increase looks safe; at −1.7 it is borderline. **Error direction: overconfident approval of a risky price move.** Needed: IV using palm oil price index or Ariel regional price variation; alternatively, a randomised price test in a small geography.

**(2) Double TV spend:** TV ROI of 2.8× was estimated without a DiD or geo-test validation. The adstock ($\lambda$) and Hill ($K$, $\alpha$) parameters are estimated jointly from historical data — with the identification problem that $\lambda$ and $K$ are not separately identified without external variation (Day 4's exercise 3). Additionally, doubling spend moves the brand from its historical range into a potentially more saturated regime. **Error direction: overestimated TV ROI at higher spend levels due to saturation not fully captured.** Needed: (a) a geo-test to validate the TV coefficient; (b) a saturation check showing the brand is currently below half-saturation ($K$) before doubling spend.

**(3) Enter 5 rural cities:** Distribution coefficient of +890 was estimated from urban expansion (Lahore, Karachi). Rural cities have different consumer profiles, lower WD/ND ratios (fewer large modern trade stores), and likely different category penetration. The urban coefficient will not transfer to rural markets without adjustment — the 890 unit estimate is driven by urban market characteristics. **Error direction: overestimated volume return from rural distribution investment.** Needed: (a) a pilot distribution entry in one rural city with DiD evaluation; (b) Nielsen rural coverage data to assess whether the distribution coefficient is measurable in rural channels.

**(4) Increase promotional frequency:** Without post-promo lag variables, the +0.55 promotional coefficient absorbs both the genuine uplift and ignores the dip. In Pakistan, where large pack sizes and bulk purchases are common, pull-forward demand is likely a significant portion of the uplift. Increasing frequency may increase gross promotional volume while actually reducing net volume (more dips, more demand pull-forward, no recovery period). Additionally, the 42% and declining base share (−2 pp/year) is a warning sign: high promotional frequency often erodes base share by training consumers to wait for promotions rather than buying at full price. **Error direction: increased promotion spend with near-zero or negative net ROI, accelerating base erosion.** Needed: Add 3–4 week post-promo dip lags to the model; calculate net promotional ROI with dip terms; separate net incremental buyers from pull-forward buyers using Kantar panel.
</details>

---

## Navigation

← **Previous:** [Day 17 — Instrumental Variables](./day-17-instrumental-variables.md)
→ **Next:** [Day 19 — Rest & Synthesize II](./day-19-rest-synthesize-2.md)
