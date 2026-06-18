# Day 3 — Sales Decomposition: Base vs. Incremental (Dove Body Wash)

> **Today's one idea:** Every MMM output is a partition of sales into base (what you'd sell with zero marketing) and incremental (what each driver added) — this partition is the whole point of the exercise.
> **Reading time:** ~35 min · **Prereqs:** Days 1–2
> **Primary source for today:** Jin et al. (2017), "Bayesian Methods for MMM with Carryover and Shape Effects" — Section 2 (Model Overview)
> **Before you start:** Recall Day 2's load-bearing idea — one sentence: what is the difference between what Nielsen measures and what internal finance data measures?

---

## The Hook

The Dove Body Wash brand team opens their annual MMM results deck. Page 3 shows a pie chart:

- **Base:** 61%
- **TV:** 18%
- **Digital:** 7%
- **Promotions:** 8%
- **Distribution gains:** 6%

The global CMO looks at the 61% base and says: "We could save £8M in media spend and still keep 61% of our sales."

Is she right?

Almost certainly not — and understanding *why* requires understanding what "base" actually means, and what the 61% is and is not saying. The base/incremental decomposition is the single most important output of an MMM. It is also the most misread.

---

## Building the Intuition

### What "base" really means

Imagine you are selling Dove Body Wash with no marketing whatsoever: no TV, no digital, no promotions, no distribution push. You just sit on the shelf where you already are, at your current price, in the same stores. What would you sell?

That hypothetical number is base sales. It captures:

- **Brand equity** — consumers who have been buying Dove for years and will keep buying it out of habit
- **Distribution presence** — the passive "finding" effect: your product is on the shelf, people pick it up
- **Seasonality** — body wash has modest seasonality (slightly higher in summer, slightly lower post-Christmas)
- **Long-run trend** — is the category growing or declining independent of any one brand's actions?

**Base is not "what we'd sell if we stopped marketing forever."** It is "what the current level of brand equity, distribution, and category health delivers in the absence of incremental marketing activity in *this period*." Stop marketing for five years and base would erode significantly — because brand equity decays, distribution contracts, and competitors fill the space.

### What "incremental" means

Incremental is everything the model attributes to active marketing drivers above the base. The key word is *attributed* — for the reasons established on Day 1, this attribution is not the same as causation.

Each incremental component is:

```
Incremental contribution of driver k in week t
= Estimated coefficient × Transformed driver value
= β_k × f(x_kt)
```

The sum across all drivers in a week gives total incremental sales for that week. The sum across all weeks gives total incremental sales for the year — the denominator in the "18% TV" figure.

### The Dove example: reading the decomposition properly

```
Annual Dove Body Wash Sales: £120M

Base:          £73.2M   (61%)   ← brand equity + distribution + trend
TV:            £21.6M   (18%)   ← attributed to TV adstock
Digital:        £8.4M   ( 7%)   ← attributed to digital spend
Promotions:     £9.6M   ( 8%)   ← attributed to price promotions
Distribution:   £7.2M   ( 6%)   ← attributed to distribution gains in period

Total:        £120.0M  (100%)
```

What the CMO can validly conclude:
- TV and digital together drive ~25% of sales in the modelled period — they are material levers
- The brand has a strong base (61%) reflecting established equity — it is not a "media-dependent" brand
- Distribution gains contributed £7.2M — there is still a distribution lever to pull

What the CMO cannot validly conclude from this decomposition alone:
- That cutting TV saves £8M and costs only £21.6M in lost sales (because cutting TV would also erode base over time via brand equity decay)
- That promotions "caused" £9.6M (may be mostly demand pull-forward — see Day 8)
- That the TV ROI (£21.6M / TV cost) justifies next year's budget without a saturation check (see Day 5)

### The base erosion trap

This is the most dangerous misread of MMM outputs. The base includes the *lagged effect of years of past marketing*. If you stop advertising:

```
Year 0:  Base = £73.2M  (current)
Year 1:  Base ≈ £68M    (some brand equity decay)
Year 2:  Base ≈ £61M    (further decay, some distribution loss)
Year 3:  Base ≈ £52M    (competition fills the vacuum)
```

The model does not project base erosion under a zero-marketing scenario — it describes base under the historical marketing condition. This is a fundamental limitation you must surface in every CMO deck. (See Day 1's "cannot" table.)

---

## The Formal Picture

The decomposition follows directly from the additive structure of the MMM regression. Given the fitted model:

```math
\hat{y}_t = \hat{\alpha} + \hat{\beta}_{\text{trend}} \cdot t + \hat{\beta}_{\text{season}} \cdot s_t + \sum_{k=1}^{K} \hat{\beta}_k \cdot f(x_{kt})
```

Define:

```math
\hat{y}_t^{\text{base}} = \hat{\alpha} + \hat{\beta}_{\text{trend}} \cdot t + \hat{\beta}_{\text{season}} \cdot s_t
```

```math
\hat{y}_{kt}^{\text{incremental}} = \hat{\beta}_k \cdot f(x_{kt})
```

Then:

```math
\hat{y}_t = \hat{y}_t^{\text{base}} + \sum_{k=1}^{K} \hat{y}_{kt}^{\text{incremental}}
```

The annual contribution percentages shown in the pie chart are:

```math
\text{Share}_k = \frac{\sum_{t=1}^{T} \hat{y}_{kt}^{\text{incremental}}}{\sum_{t=1}^{T} \hat{y}_t} \times 100
```

```math
\text{Base share} = \frac{\sum_{t=1}^{T} \hat{y}_t^{\text{base}}}{\sum_{t=1}^{T} \hat{y}_t} \times 100
```

**Important implication:** incremental shares are *relative to total observed sales*. If you run a very large promotion in the historical period, it inflates the denominator and mechanically reduces everyone else's share — including TV's 18%. The pie chart is not an absolute measure of importance; it is a relative one, conditional on the historical spend mix.

### Visualising the decomposition over time

A useful diagnostic is the **stacked area chart** of weekly contributions:

```
Sales
  │
  │  ██████ Promotions (spikes)
  │  ██████ Digital (steady)
  │  ██████ TV (peaks near campaign weeks)
  │  ██████ Distribution gain (gradual build)
  │▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓ Base (stable floor)
  └──────────────────────────────────── Time
```

Things to look for:
- Does the base drift upward or downward over time? (Trend direction)
- Do promotional spikes show a post-spike dip? (Demand borrowing — Day 8)
- Does TV contribution lag the GRP peaks by 1–3 weeks? (Adstock working — Day 4)
- Is the base floor stable or does it step-change after distribution events? (Distribution elasticity — Day 9)

---

## Where It Breaks / What It Is Not

**"Base = organic sales."** Organic implies no marketing causation ever touched it. Base includes brand equity that was built by decades of prior advertising. The distinction between "organic" and "marketing-built" is a philosophical one the model cannot make.

**"The decomposition adds up, so it must be right."** By construction, any additive model decomposes to 100%. The decomposition is mathematically coherent even if every coefficient is biased. Perfect decomposition is a property of model structure, not evidence of model validity.

**"Promotions drove 8% — promotions are efficient."** Efficiency requires comparing the incremental sales to the cost of generating them. 8% of £120M = £9.6M in attributed sales. If promotions cost £12M to fund, they have a negative ROI on this metric alone — and that ignores demand cannibalization from future periods.

---

## Try It Yourself

> Close this page now before attempting Exercise 1.

**Exercise 1 — Retrieval.** Without looking: what two components does every MMM output decompose sales into? What drives each component? What is the most dangerous misinterpretation of the base?

<details>
<summary>Reference answer</summary>

Two components: **base** (brand equity, distribution, trend, seasonality — everything that would exist without current marketing activity) and **incremental** (attributed contribution of each active marketing driver).

The most dangerous misinterpretation: assuming that cutting all marketing spend would cost only the incremental share, when in reality it would erode the base over time via brand equity decay.
</details>

---

**Exercise 2 — Direct application.** A Dove MMM shows base share declining from 68% to 55% over three years with no change in media spend. List three business hypotheses this finding could be consistent with, and identify which additional data you would need to distinguish between them.

<details>
<summary>Reference answer</summary>

**Hypothesis 1:** Distribution loss — fewer stores stocking Dove → lower passive sales. *Check: Nielsen numeric/weighted distribution trend.*

**Hypothesis 2:** Category decline — the body wash category is shrinking (shower gels, bars growing). *Check: Nielsen category total value/volume trend.*

**Hypothesis 3:** Brand equity erosion — long-term decline in brand salience or quality perception. *Check: Kantar brand health tracker (awareness, consideration, trial metrics).*

These are not mutually exclusive. The base decline is a symptom; the data above identifies which disease.
</details>

---

**Exercise 3 — Stretch (callback to Day 1).** A colleague argues: "The MMM decomposition tells us TV caused 18% of Dove sales, which means we can confidently scale TV and get a proportional sales return." Using Days 1 and 3, identify two separate reasons this inference is wrong.

<details>
<summary>Reference answer</summary>

**Reason 1 (Day 1):** Attribution ≠ causation. The 18% is the historical co-variation between TV adstock and sales. It does not prove TV caused those sales — there may be confounders (e.g., TV spend was high in the same weeks as distribution gains, and the model partially conflates them).

**Reason 2 (Day 3 / tomorrow's Day 5):** The incremental percentage is relative to historical spend levels. Scaling TV beyond the historical range means operating on a different (and steeper, more saturated) part of the response curve — where the marginal return per pound is lower. "Proportional scaling" assumes a linear response, but diminishing returns mean the curve bends.
</details>

---

**Transfer — apply it:**

> In your current or most recent project, what is the equivalent of "base sales" — the outcome that would persist even if you removed all your interventions? Write one sentence naming it, and one sentence estimating whether your current analysis holds it constant or models it explicitly.

---

## Connect It Back

Today we built the fundamental output of MMM: a partition of sales into base and incremental. The base is not passive — it encodes years of brand equity — and the incremental is not causal — it is attributed. Tomorrow we confront the first technical question: how do we handle the fact that advertising doesn't stop affecting consumers the moment it stops airing?

**Sharp question to carry forward:** If Dove ran a TV campaign in Week 10, would you expect the incremental TV contribution to be zero in Week 11 if no campaign aired that week? If not, how would the model account for it?

*(The answer is adstock — tomorrow's entire page.)*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:** Jin et al. (2017), "Bayesian Methods for MMM with Carryover and Shape Effects," Section 2 — the model overview section establishes the additive decomposition structure formally. Read the model equation and confirm it matches the notation introduced today.

**If you want the deep version:**
- Robyn documentation (Meta, 2021) at github.com/facebookexperimental/Robyn — the "One-Pager" section explains the decomposition waterfall chart (base + incremental) in practitioner language. Compare to today's Dove example.
- Charan, A. *The Marketing Analytics Practitioner's Guide* — the chapter on brand equity and base sales; specifically the discussion of how to interpret a rising vs. falling base trend.

---

## Navigation

← **Previous:** [Day 2 — The Data Universe](./day-02-data-universe.md)
→ **Next:** [Day 4 — Adstock & Carryover](./day-04-adstock-carryover.md)
