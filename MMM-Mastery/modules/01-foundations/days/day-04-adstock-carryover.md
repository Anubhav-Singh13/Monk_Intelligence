# Day 4 — Adstock & Carryover: Why Ads Keep Working After They Stop

> **Today's one idea:** Advertising effects decay geometrically over time, so the variable entering the regression is a weighted sum of past spend — not current spend alone.
> **Reading time:** ~35 min · **Prereqs:** Day 3
> **Primary source for today:** Jin et al. (2017), "Bayesian Methods for MMM with Carryover and Shape Effects" — Section 3.1 (Carryover)
> **Before you start:** Recall Day 3's load-bearing idea — in one sentence: what is the difference between base sales and incremental sales in the decomposition?

---

## The Hook

You are building the Dove MMM. You plot weekly TV GRPs against weekly sales uplift. The correlation is... confusing. Weeks with high GRPs sometimes have high uplift. But sometimes the highest uplift weeks come one or two weeks *after* the campaign peak. And sometimes a week with zero GRPs still shows measurable uplift from TV.

A naive modeller sees this and concludes "TV doesn't work that well for Dove." They put in raw weekly GRPs and get a small, noisy coefficient. Their CMO cuts TV. Sales eventually fall.

The mistake: advertising doesn't switch off the moment it stops airing. It leaves a residue — a "stock" of attention, memory, and brand association in consumers' minds. This stock decays over time, but it doesn't disappear instantly. Raw weekly spend is the wrong variable. You need the *decayed running total of past spend* — which is what adstock transforms.

---

## Building the Intuition

### The leaky bucket

Imagine consumer attention as a leaky bucket. Every week you run a TV campaign, you pour water in (new spend). Every week that passes, the bucket leaks — consumers forget a fraction of what they saw. The bucket never fully empties (brand equity stays), but the amount of water actively driving purchase behaviour is the current level in the bucket, not last week's pour.

If your bucket leaks 30% per week ($\lambda = 0.70$):

```
Week 1: You spend 100 GRPs. Bucket level = 100
Week 2: You spend 0 GRPs.   Bucket level = 100 × 0.70 = 70
Week 3: You spend 0 GRPs.   Bucket level = 70 × 0.70  = 49
Week 4: You spend 200 GRPs. Bucket level = 49 × 0.70 + 200 = 234
Week 5: You spend 0 GRPs.   Bucket level = 234 × 0.70 = 164
```

The variable that enters your regression is not {100, 0, 0, 200, 0} — it is {100, 70, 49, 234, 164}. The second series has a much stronger, smoother relationship with sales because it reflects the actual level of advertising "pressure" consumers are experiencing.

### The recursion formula

Formally, adstock at time $t$ is:

```math
A_t = x_t + \lambda \cdot A_{t-1}
```

where:
- $x_t$ is raw spend (or GRPs, impressions, etc.) in week $t$
- $\lambda \in [0, 1]$ is the **retention rate** (also called decay parameter or carryover rate)
- $A_{t-1}$ is last week's adstock level

Expanding the recursion:

```math
A_t = x_t + \lambda x_{t-1} + \lambda^2 x_{t-2} + \lambda^3 x_{t-3} + \cdots
```

This is a *geometric weighted average of all past spend*, with more recent weeks weighted more heavily. The weights sum to $\frac{1}{1-\lambda}$ — they don't sum to 1, which means adstock is a scaled quantity, not a normalised one. This matters when comparing coefficients across channels.

### What $\lambda$ means in business terms

The **mean lag** — the average number of weeks an advertising effect persists — is:

```math
\text{Mean lag} = \frac{\lambda}{1 - \lambda}
```

| $\lambda$ | Mean lag (weeks) | Interpretation |
|-----------|-----------------|----------------|
| 0.3 | 0.43 | Mostly immediate effect; low carryover (e.g., tactical price promotions) |
| 0.5 | 1.0 | Half-life of 1 week; moderate carryover |
| 0.7 | 2.3 | ~2-week effect; typical for TV in FMCG |
| 0.8 | 4.0 | ~4-week effect; upper end for brand-building TV |
| 0.9 | 9.0 | ~9-week effect; unusually persistent (OOH, sponsorship) |

For Dove TV: typical $\lambda$ in the range 0.6–0.75 (2–3 week mean lag). For Surf Excel price promotions: $\lambda$ close to 0 (immediate effect, negligible carryover — and actually negative due to cannibalization, which Day 8 addresses).

### Why this matters for your regression

Without adstock transformation, the estimated TV coefficient is **attenuated** — biased toward zero — because raw spend in a campaign week correlates with sales that week but misses the 2–3 weeks of additional sales that week's spend will generate.

With adstock transformation, the model can properly attribute those lagged sales back to the campaign that caused them.

```
Without adstock:  TV coefficient = 0.012  (noisy, low)
With adstock:     TV coefficient = 0.034  (cleaner, higher)
```

The coefficient's meaning also changes. Without adstock, $\hat{\beta}_{TV}$ is "units per GRP this week." With adstock, $\hat{\beta}_{TV}$ is "units per unit of adstock" — which implicitly includes all future lagged effects of one additional GRP today.

---

## The Formal Picture

The geometric adstock is the simplest of a family of carryover functions. Jin et al. (2017) also define a **Weibull adstock** that allows for a delayed peak (the effect builds before it decays — appropriate for channels like OOH or podcast advertising where exposure accumulates before it registers).

For geometric adstock, the Python implementation is a one-liner using cumulative weighted sums:

```python
import numpy as np

def geometric_adstock(spend: np.ndarray, decay: float) -> np.ndarray:
    """
    Apply geometric adstock transformation.
    spend: weekly spend array, shape (T,)
    decay: retention rate lambda in [0, 1]
    returns: adstock array, shape (T,)
    """
    adstock = np.zeros_like(spend, dtype=float)
    adstock[0] = spend[0]
    for t in range(1, len(spend)):
        adstock[t] = spend[t] + decay * adstock[t - 1]
    return adstock

# Example: Dove TV GRPs over 8 weeks
grps = np.array([0, 450, 380, 0, 0, 500, 420, 0], dtype=float)
adstocked = geometric_adstock(grps, decay=0.70)
print(adstocked.round(1))
# [ 0.   450.  695.  486.5 340.6 840.4 1134.3  793.9]
```

Notice that Week 3 (0 GRPs) has adstock of 486.5 — the residual effect of Week 2's 380 GRPs still working. Week 6 (500 GRPs) gets an adstock of 840.4 because it inherits the decayed remnant from Week 5's 340.6.

**The normalised version** (used in PyMC-Marketing and Robyn) divides by $(1-\lambda)$ to rescale adstock to the same units as raw spend:

```python
adstock_normalised = geometric_adstock(spend, decay) * (1 - decay)
```

This makes coefficients comparable across channels with different decay rates.

### How $\lambda$ is estimated

In a frequentist MMM, $\lambda$ is typically grid-searched (try every value from 0.1 to 0.9 in steps of 0.05 and pick the one that maximises R² or minimises holdout MAPE). This is computationally cheap but ignores uncertainty.

In a Bayesian MMM (Day 20), $\lambda$ gets a prior distribution — typically $\text{Beta}(3, 3)$ for TV, which centres around 0.5 but allows the data to move it. The posterior distribution over $\lambda$ is part of the model output and propagates into the credible intervals on all downstream estimates.

---

## Where It Breaks / What It Is Not

**"Just try a range of lambdas and pick the best."** Grid search treats $\lambda$ as a hyperparameter to be optimised against the training set. This risks overfitting — especially if the training set has few campaigns. Bayesian estimation with a meaningful prior is more robust.

**"Adstock accounts for all long-term effects."** Geometric adstock captures short-to-medium term carryover (weeks to months). It does not capture long-run brand equity effects that persist for years. For those, you need either a very long time series or a separate brand equity model. The base term in the MMM captures some of this, but only as a static level.

**"The same $\lambda$ applies to all media channels."** Not even close. TV in a mass-market FMCG category (Dove Body Wash) will have $\lambda \approx 0.65$–$0.75$. Performance digital (Google search) will have $\lambda \approx 0.1$–$0.2$ (near-immediate). Social media may vary widely. Set channel-specific priors; don't fit a single decay parameter for all media.

**"Adstock is only for media."** Adstock applies to any driver with lagged effects: word-of-mouth, retailer promotional cycles, PR coverage, and — interestingly — distribution changes (a new distribution gain takes several weeks to show up in scan data as shoppers discover the product). This is why Day 9 revisits carryover in the context of distribution.

---

## Try It Yourself

> Close the page now before attempting Exercise 1.

**Exercise 1 — Retrieval.** Without looking: write the adstock recursion formula from memory. Then give the business interpretation of $\lambda = 0.7$ in terms of how long a campaign's effect lasts.

<details>
<summary>Reference answer</summary>

Formula: $A_t = x_t + \lambda \cdot A_{t-1}$

$\lambda = 0.7$ means 70% of last week's adstock carries into this week. Mean lag = $\frac{0.7}{1-0.7} = 2.3$ weeks — so on average, a campaign's effect persists about 2.3 weeks beyond the airing week.
</details>

---

**Exercise 2 — Direct application.** A media planner argues that Dove should front-load its annual TV budget into Q1 (January–March) because "that's when consumers make resolutions about personal care." Using adstock, explain what the model predicts will happen to TV's incremental contribution in Q2 (April–June) under this strategy, compared to a flat-spread strategy. Be specific about the mechanism.

<details>
<summary>Reference answer</summary>

Under front-loading, adstock builds to a high level in Q1. With $\lambda = 0.70$:
- Week 12 (end of Q1, last campaign week): high adstock
- Week 13 (start of Q2, no spend): adstock = 0.70 × week-12 level — still substantial
- Week 17 (5 weeks into Q2): adstock = 0.70^5 × week-12 level ≈ 17% of peak — declining rapidly

By mid-Q2, the carryover is largely exhausted. The model predicts that incremental TV contribution will be moderate in April, low in May, and near-zero in June. Under a flat-spread strategy, a modest steady campaign would maintain low-but-persistent adstock throughout Q2, likely generating more total incremental volume over the full year — assuming the saturation curve isn't hit (Day 5).

This is why front-loading is often less efficient than it appears: the carryover benefit in Q2 is real but finite, and it may not offset the saturation cost of concentrating spend in Q1.
</details>

---

**Exercise 3 — Stretch.** You are estimating a Dove MMM. You fit the model with $\lambda = 0.70$ and get a TV coefficient of $\hat{\beta}_{TV} = 0.031$. A colleague refits with $\lambda = 0.50$ and gets $\hat{\beta}_{TV} = 0.048$. Both models have similar R². Which $\lambda$ is "right"? What does this tell you about the identifiability of $\lambda$ from observational data?

<details>
<summary>Reference answer</summary>

Both are plausible fits to the same data — R² doesn't distinguish them. This reveals a **joint identification problem**: in observational data, $\lambda$ and $\beta$ are not separately identified without external information. A high-$\lambda$, low-$\beta$ model and a low-$\lambda$, high-$\beta$ model can produce similar predictions because they agree on the total effect (the area under the response curve) but disagree on how it is distributed over time.

This is one of the strongest arguments for Bayesian MMM with informative priors on $\lambda$: use business knowledge (TV half-life for FMCG is 2–5 weeks, not 20) to constrain the joint estimation. Without such constraints, the optimiser can find local minima with absurd carryover values.
</details>

---

**Transfer — apply it:**

> Think of a driver in your own domain that has lagged effects — where the input today generates outputs not just today but in future periods. Write one sentence naming it, estimating how many periods the effect persists, and what the consequences would be of ignoring the lag in a model.

---

## Connect It Back

Adstock handles the *time dimension* of advertising response. Tomorrow we handle the *scale dimension*: the fact that response to spend isn't linear — doubling spend doesn't double response. Together, adstock and saturation form the two-transformation stack that all media variables in the model must go through. After Day 5, you will have the full data preparation pipeline for media drivers.

**Sharp question to carry forward:** If two channels both have the same raw spend, but Channel A has $\lambda = 0.8$ and Channel B has $\lambda = 0.3$, which channel's adstock variable will have a *larger variance* over time? Why does that matter for the regression coefficient estimate?

*(Answer: Channel A, because its adstock accumulates slowly and persists, creating larger swings. Higher variance in the predictor generally produces a more precisely estimated coefficient — assuming the model is correctly specified.)*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:** Jin et al. (2017), Section 3.1 — the carryover section defines geometric adstock, derives the mean lag formula, and introduces the Weibull extension. Read the first two pages of Section 3.1 only.

**If you want the deep version:**
- PyMC-Marketing documentation — the `GeometricAdstock` and `WeibullAdstock` API pages. Reading the source reveals that PyMC-Marketing places a Beta prior on $\lambda$ by default. Check the default parameters and ask whether they are appropriate for your category.
- Robyn documentation — the "hyperparameter tuning" section explains how Robyn searches over $\lambda$ via Nevergrad. This is the frequentist alternative to Bayesian estimation; understanding both helps you defend your modelling choices.

---

## Navigation

← **Previous:** [Day 3 — Sales Decomposition: Base vs. Incremental](./day-03-base-vs-incremental.md)
→ **Next:** [Day 5 — Saturation: The Hill Function](./day-05-saturation-hill-function.md)
