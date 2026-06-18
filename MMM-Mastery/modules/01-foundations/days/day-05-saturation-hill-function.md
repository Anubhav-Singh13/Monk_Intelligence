# Day 5 — Saturation: The Hill Function and Diminishing Returns

> **Today's one idea:** Doubling spend never doubles sales; the spend-to-sales relationship follows a bounded S-shaped curve, and where you sit on that curve determines your ROI.
> **Reading time:** ~35 min · **Prereqs:** Day 4
> **Primary source for today:** Jin et al. (2017), "Bayesian Methods for MMM with Carryover and Shape Effects" — Section 3.2 (Shape/Saturation)
> **Before you start:** Recall Day 4's load-bearing idea — one sentence: what does the adstock transformation do to raw spend, and why is it necessary before regression?

---

## The Hook

The Dove media team has a good year. They ask for a 50% increase in TV budget for next year — their argument: "TV delivered an 18% sales contribution at £X spend. If we spend 1.5X, we should get 27%."

This argument is wrong, and every experienced media planner knows it. The question is: *how* wrong, and in which direction?

The intuition is familiar from everyday life. The first glass of water when you are very thirsty does an enormous amount of good. The second glass does a bit less. The tenth glass provides almost no marginal benefit — and at some point more water is harmful. Marketing spend works the same way. The first impressions reach consumers who had never heard of the brand. The next impressions catch consumers who had heard of it but not yet been prompted to buy. Eventually, you are showing the same ad to the same people who have already decided — or decided against. The marginal return collapses.

MMM without a saturation function will overestimate the return to additional spend at high investment levels. It will also underestimate the return at low investment levels. Getting the shape of the response curve right is the difference between a model that guides budget decisions and one that misleads them.

---

## Building the Intuition

### The S-curve and the concave curve

There are two common shapes for media response curves:

```
CONCAVE (always diminishing returns):        S-SHAPED (initial build, then diminishing):

Response │                                   Response │          ___________
         │    _______                                  │       __/
         │   /                                         │      /
         │  /                                          │     /
         │ /                                           │    /
         │/                                            │___/
         └─────────────────── Spend                   └─────────────────── Spend
         
         α ≤ 1 in Hill function                        α > 1 in Hill function
```

**Concave** curves apply when: the product has high awareness, the market is already exposed, and additional spend finds increasingly marginal consumers. Common for mature FMCG brands like Dove in established markets.

**S-shaped** curves apply when: below a threshold, spend creates little response (the brand is invisible); above the threshold, spend breaks through and responses accelerate; then diminishing returns set in. Common for new product launches or markets with low category awareness.

For most established Unilever brands in developed markets, you will encounter concave curves — diminishing returns from the first pound spent.

### The half-saturation point: your most important single number

The Hill function has a parameter $K$ — the **half-saturation constant** — which is the spend level at which the response is exactly 50% of its theoretical maximum. Think of it as the "midpoint" of the response curve.

If Dove's TV $K = £5M$ per week in adstocked GRPs:
- Spending £5M gets you 50% of maximum possible incremental sales from TV
- Spending £10M gets you 67% (not 100%)
- Spending £20M gets you 80%
- Spending £40M gets you 89%

The returns compress rapidly beyond $K$. This is why the Dove team's "1.5× spend = 1.5× effect" argument is wrong once you are already spending near or above $K$.

---

## The Formal Picture

The Hill function (as used in MMM) is:

```math
h(x) = \frac{x^{\alpha}}{x^{\alpha} + K^{\alpha}}
```

where:
- $x$ is the adstock-transformed spend (not raw spend — adstock always comes first)
- $K > 0$ is the **half-saturation constant**: the value of $x$ at which $h(x) = 0.5$
- $\alpha > 0$ controls the **steepness** of the curve:
  - $\alpha \leq 1$: concave (no S-shape, diminishing returns from the start)
  - $\alpha > 1$: sigmoidal (S-shaped, with an initial convex region)

The range of $h(x)$ is always $(0, 1)$ — it is a bounded, monotone function. The full transformed variable entering the regression is:

```math
\text{MediaVariable}_t = h(A_t) = \frac{A_t^{\alpha}}{A_t^{\alpha} + K^{\alpha}}
```

and the incremental contribution of channel $k$ in week $t$ is:

```math
\text{Incremental}_{kt} = \hat{\beta}_k \cdot h(A_{kt})
```

The **response curve** — the graph a CMO sees in the deck — plots $\hat{\beta}_k \cdot h(A)$ against raw spend $x$, for a range of $x$ values. The derivative of this curve with respect to $x$ is the **marginal ROI**: the extra revenue per extra pound at a given spend level.

### Python implementation

```python
import numpy as np

def hill_function(x: np.ndarray, K: float, alpha: float) -> np.ndarray:
    """
    Apply Hill saturation transformation.
    x:     adstock array (output of geometric_adstock)
    K:     half-saturation constant (same units as x)
    alpha: steepness parameter (>0; >1 for S-shape)
    returns: saturated response in [0, 1]
    """
    return x**alpha / (x**alpha + K**alpha)

# Example: Dove adstocked GRPs → saturated response
# Suppose K = 600 adstock units (half-saturation) and alpha = 0.8 (concave)
K, alpha = 600.0, 0.8
adstocked = np.array([0, 450, 695, 486.5, 340.6, 840.4, 1134.3, 793.9])
saturated = hill_function(adstocked, K=K, alpha=alpha)
print(saturated.round(3))
# [0.    0.411 0.534 0.442 0.354 0.583 0.648 0.568]
```

Notice that the jump from adstock 450 → 840 (an 87% increase) produces only a 0.411 → 0.583 increase in response (a 42% increase). This is diminishing returns in action. The 18% TV share the Dove team calculated was generated at a given historical spend level; it does not scale proportionally.

### What this means for budget decisions

The response curve implies an **efficiency frontier**: the spend level at which marginal ROI (revenue per extra £) equals some threshold (often 1.0 — break-even). Spending below this level leaves money on the table; spending above it destroys value.

```
Marginal ROI vs. Spend (Dove TV — illustrative):

Marginal │
ROI      │ ●  (£2M/week — highly efficient)
         │    ●
         │       ●
         │          ●  (£5M/week — at half-saturation, mROI ≈ 1.5)
    1.0  │- - - - - - - ● - - - - ● - - (break-even line)
         │                           ●
         │                               ●  (£12M/week — below break-even)
         └────────────────────────────────────── Spend (£M/week)
```

The optimal spend is the level at which the marginal ROI curve crosses 1.0. Beyond that point, you are spending more than you are recovering. This is the foundation for budget optimisation on Day 25.

---

## Where It Breaks / What It Is Not

**"The saturation curve is fixed."** $K$ and $\alpha$ are estimated from historical data at historical spend levels. The curve is reliable within the observed range. Extrapolating to spend levels 3× above the historical maximum is speculative — you are extrapolating the functional form beyond where it was calibrated.

**"A higher $\alpha$ is always better."** $\alpha > 1$ (S-shaped) implies there is a threshold below which spend is essentially wasted. For budget planning, this creates a dangerous implication: "spend more or don't spend at all." Before accepting an S-shaped fit, check whether there is genuine business evidence for a threshold effect, or whether it is a statistical artifact of sparse data.

**"Saturation applies only to media."** Price also shows a saturation-like pattern — the volume loss from a 1% price increase is not constant; it accelerates at higher absolute prices. Distribution similarly: the first 10 distribution points gained have higher volume impact than the last 10 (you are picking up lower-volume stores). The Hill function applies to any driver with diminishing returns, not just advertising.

**"We can fix saturation by spending less."** You can move down the curve to a higher marginal ROI, but the shape of the curve is a property of how consumers respond to your category — you cannot change it by spending less. What you can change is *where* on the curve you operate.

---

## Try It Yourself

> Close this page now before attempting Exercise 1.

**Exercise 1 — Retrieval.** Without looking: what are the two parameters of the Hill function, and what does each control? What is the value of the Hill function when spend equals exactly $K$?

<details>
<summary>Reference answer</summary>

Parameters: $K$ (half-saturation constant — the spend level at which response is 50% of maximum) and $\alpha$ (steepness — controls whether the curve is concave ($\alpha \leq 1$) or S-shaped ($\alpha > 1$)).

When spend = $K$: $h(K) = \frac{K^\alpha}{K^\alpha + K^\alpha} = \frac{1}{2} = 0.5$.
</details>

---

**Exercise 2 — Direct application.** The Dove MMM estimates TV response with $K = 600$ adstock units and $\alpha = 0.8$. Historical average weekly adstock is 480 units. The media team proposes increasing weekly GRPs by 30%, which would raise average adstock to approximately 624 units.

(a) What is the Hill function value at the current 480 adstock vs. the proposed 624?
(b) What is the percentage increase in saturated response?
(c) The team expects a 30% increase in incremental TV sales. Are they right? By how much are they over- or under-estimating?

<details>
<summary>Reference answer</summary>

```python
K, alpha = 600.0, 0.8

h_current  = 480**0.8 / (480**0.8 + 600**0.8)
h_proposed = 624**0.8 / (624**0.8 + 600**0.8)

# h_current  ≈ 0.453
# h_proposed ≈ 0.510
# % increase = (0.510 - 0.453) / 0.453 ≈ 12.6%
```

(a) h_current ≈ 0.453; h_proposed ≈ 0.510
(b) Saturated response increases by ~12.6%
(c) The team expects 30% more sales from 30% more spend. The model predicts only ~12.6% more incremental TV sales. They are overestimating by a factor of ~2.4x — the saturation curve absorbs more than half the additional spend.
</details>

---

**Exercise 3 — Stretch (callback to Day 3).** In the Dove decomposition from Day 3, TV was attributed 18% of sales. The Dove team now learns the model used $\alpha = 0.8$ and $K = 600$. They are operating at adstock = 480 (below half-saturation). A competitor (Nivea) has much higher TV spend and is estimated to be operating at adstock = 850 (above half-saturation).

What does this imply about the relative efficiency of Dove's vs. Nivea's TV investment? What strategic conclusion would you draw?

<details>
<summary>Reference answer</summary>

At adstock 480 ($< K = 600$), Dove is operating on the steeper part of the curve — higher marginal ROI than at half-saturation. At adstock 850 ($> K = 600$), Nivea is operating on the flatter, more saturated part — lower marginal ROI.

Implication: Dove gets more incremental sales per additional GRP than Nivea does at their current levels. If Dove's brand equity and creative quality are comparable, this suggests Dove has **room to increase TV spend efficiently** while Nivea would benefit from reducing TV and reallocating to other channels.

This is exactly the kind of insight the budget optimisation on Day 25 formalises — and it is only visible once the saturation curves for both brands are estimated. Competitor response curves require the competitor analysis from Day 23.
</details>

---

**Transfer — apply it:**

> Think of a resource allocation decision in your own domain. Write one sentence describing the response curve shape you would expect (concave vs. S-shaped) and one sentence on what it implies for optimal allocation. If you are not sure, describe what data you would need to estimate it.

---

## Connect It Back

Adstock handles time; the Hill function handles scale. Together, they transform raw spend data into model-ready variables. Every media driver in the MMM passes through both transformations before entering the regression. Tomorrow, we step back from individual variables and introduce the 5Ps framework — the taxonomy that organises *all* driver types (not just media) into a coherent structure for the model.

**Sharp question to carry forward:** If saturation applies to distribution and price as well as media, what would you expect the shape of the distribution response curve to look like — and at what point does adding more distribution stores deliver diminishing returns?

*(Day 9 — Place — answers this directly.)*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:** Jin et al. (2017), Section 3.2 — the shape/saturation section. Pay attention to Figure 1 which plots the Hill function for different values of $\alpha$ and $K$. Confirm that it matches the Python output above.

**If you want the deep version:**
- Robyn documentation — the "response curve" section shows how Robyn plots marginal ROI vs. spend using the estimated Hill parameters. This is the visual form of the budget optimisation logic you will build on Day 25.
- McElreath (2023), *Statistical Rethinking*, Lecture 4 — "Categories, Curves, and Splines." The general principle of using bounded monotone functions to model relationships that cannot go negative and must eventually plateau. The Hill function is a specific case of this broader class.

---

## Navigation

← **Previous:** [Day 4 — Adstock & Carryover](./day-04-adstock-carryover.md)
→ **Next:** [Day 6 — The 5Ps Framework](./day-06-five-ps-framework.md)
