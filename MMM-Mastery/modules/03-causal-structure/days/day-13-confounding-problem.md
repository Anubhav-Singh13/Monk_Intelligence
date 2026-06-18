# Day 13 — The Confounding Problem: Why Naïve MMM Produces Wrong Decisions

> **Today's one idea:** Price is set high when demand is already high — so naïve regression sees high price co-varying with high sales and produces a biased elasticity that makes price look safer to raise than it really is.
> **Reading time:** ~35 min · **Prereqs:** Days 1–11
> **Primary source for today:** Cunningham (2021), *Causal Inference: The Mixtape*, Chapter 2 — "Probability and Regression Review" and Chapter 4 — "Directed Acyclical Graphs"
> **Before you start:** Recall Day 12's R&S — in one sentence: which of the 5 Ps has the highest endogeneity risk, and in which direction does this bias its coefficient?

---

## The Hook

You work for a retailer who stocks both Surf Excel and Ariel. You have two years of Nielsen weekly data. You fit a log-log regression:

```
log(Surf Excel volume) = α + η × log(ASP) + ε
```

You get $\hat\eta = +0.4$. Positive. Price goes up, volume goes up.

Your first instinct: "Something is wrong with the data." Your second instinct: "Maybe Surf Excel is a Giffen good." Your third instinct, if you have been reading this course: "The coefficient is biased because price is endogenous."

But *how* does endogeneity produce a positive coefficient on price? Walking through the exact mechanism is the purpose of today's page — because understanding the mechanism is what allows you to fix it.

---

## Building the Intuition

### The Unilever pricing manager's logic

Surf Excel's pricing manager does not set price randomly. She has access to information you do not have in the MMM:

- Retailer forward-planning data: "Asda is running a summer homecare promotion — they expect 15% category uplift in June/July"
- Salesforce feedback: "Strong forward orders for the 1kg pack in Q3; Northern England distributors are pre-buying"
- Category trend data: "Category volume is growing 6% YoY in South Asia — headroom to push price"
- Competitive intelligence: "Ariel has just announced a price hold through Q4; we have pricing room"

In *high-demand periods*, she raises price (or holds it and avoids promotional discounting). In *low-demand periods*, she runs deeper promotions to stimulate demand.

Now look at what this creates in the data:

```
Week     Demand state      Surf Excel ASP    Surf Excel volume
---      ------------      -------------     ----------------
Q1–Q2    High (summer)     £4.40             High
Q3       Moderate          £4.20             Moderate
Q4       Low (winter)      £3.90 (promo)     Moderate (promo-driven)
```

High ASP weeks tend to be high volume weeks — not because price *caused* high volume, but because the same underlying demand conditions caused *both* high price *and* high volume. The regression, which cannot see "underlying demand conditions," incorrectly attributes the volume to the price.

This is **endogeneity**: the regressor (price) is correlated with the error term (unobserved demand) because both are driven by the same underlying factor.

### Simpson's Paradox as intuition

Simpson's Paradox is a useful illustrative device. Imagine two time periods:

**Period A (Summer — high demand):** High price, high volume. Association: positive.
**Period B (Winter — low demand):** Low price (promo), low-to-moderate volume. Association: ambiguous.

**Pooled data:** Averaging across both periods, you see a positive correlation between price and volume — not because price drives volume, but because both are correlated with season/demand.

The correct analysis would control for the demand level directly. But demand is not observed in the data — it is precisely what the error term $\epsilon$ represents. Controlling for it requires either:
1. A proxy variable for demand (which is hard to find)
2. An instrumental variable that shifts price without shifting demand (Day 17)
3. A natural experiment where price is set by factors unrelated to demand (Day 16)

### The three sources of endogeneity in MMM

| Source | Mechanism | Affected P | Direction of bias |
|--------|-----------|-----------|------------------|
| **Demand endogeneity** | Prices are raised in high-demand periods | Price | η biased toward 0 or positive |
| **Timing endogeneity** | Promotions are run in low-demand periods (to stimulate) | Promotion | β biased toward 0 (underestimates promo lift) |
| **Selection endogeneity** | Distribution is expanded in high-potential markets first | Place | β inflated (gains attributed to distribution, not to underlying market potential) |

All three are present in typical FMCG MMM data. Day 1's "cannot" table listed these as fundamental limits of naïve MMM — now you understand the exact mechanism behind each.

---

## The Formal Picture

### The bias formula

In a simple regression $y = \alpha + \beta x + \epsilon$ where $\text{Cov}(x, \epsilon) \neq 0$:

```math
\text{plim}(\hat\beta) = \beta + \frac{\text{Cov}(x, \epsilon)}{\text{Var}(x)}
```

The second term is the **omitted variable bias** (or endogeneity bias). Let's make it concrete for price endogeneity.

Suppose the true model is:

```math
\ln(\text{Volume}_t) = \alpha + \eta \cdot \ln(\text{Price}_t) + \delta \cdot D_t + \epsilon_t
```

where $D_t$ is an unobserved demand index (the season-specific demand condition the pricing manager sees). She sets price as:

```math
\ln(\text{Price}_t) = \gamma_0 + \gamma_1 \cdot D_t + \nu_t
```

When you omit $D_t$ from the regression, the bias in $\hat\eta$ is:

```math
\text{Bias}(\hat\eta) = \delta \cdot \frac{\text{Cov}(\ln P, D)}{\text{Var}(\ln P)} = \delta \cdot \frac{\gamma_1 \cdot \text{Var}(D)}{\text{Var}(\ln P)}
```

Since $\delta > 0$ (higher demand → higher volume) and $\gamma_1 > 0$ (higher demand → higher price), the bias is **positive** — the estimated elasticity $\hat\eta$ is larger (less negative) than the true elasticity $\eta$. In extreme cases, it flips to positive.

### What this means for a pricing decision

Suppose the true price elasticity is $\eta = -2.1$, but the naïve MMM estimates $\hat\eta = -1.2$ (biased toward zero by demand endogeneity).

The brand manager uses $\hat\eta = -1.2$ to justify a 7% price increase:
- Predicted volume loss: $7 \times 1.2 = 8.4\%$
- Break-even at 38% margin: $7/(7+38) = 15.6\%$ — 8.4% < 15.6% → looks profitable

True outcome with $\eta = -2.1$:
- Actual volume loss: $7 \times 2.1 = 14.7\%$ — close to the break-even threshold, possibly loss-making once competitive response is included

The biased model gave the green light for a decision that was marginal at best and loss-making at worst. The brand manager's confidence was purchased by the bias. This is the real cost of the confounding problem.

---

## Where It Breaks / What It Is Not

**"Including more control variables fixes endogeneity."** Adding seasonality dummies, trend variables, and competitor controls reduces some confounding, but it does not fix price endogeneity if the unobserved demand factor is not captured by any of the controls. Endogeneity is a *structural* problem (the regressor is jointly determined with the outcome), not a *specification* problem (you forgot to include a variable).

**"A longer time series provides more information."** More data helps with precision but not with bias. The bias formula above does not depend on $T$ (the number of observations). A 10-year MMM with endogenous price produces a more precisely estimated *wrong* number.

**"Bayesian priors on the price coefficient fix this."** A prior centred on $-1.5$ prevents the posterior from being $+0.4$, but if the data strongly support $-1.2$, the posterior will land near $-1.2$. The prior moves the answer toward the prior mean, not toward the true causal value. The bias is in the likelihood, not the prior.

**"My model has high R² so it's fine."** R² measures fit to the training data. A model can fit historical data perfectly and still have a completely wrong price coefficient if demand endogeneity is present. These are independent properties of the model.

---

## Try It Yourself

> Close the page now before attempting Exercise 1.

**Exercise 1 — Retrieval.** Without looking: explain the mechanism by which price endogeneity causes the MMM price coefficient to be biased. What is the direction of the bias, and what does this cause a brand manager to conclude incorrectly?

<details>
<summary>Reference answer</summary>

Mechanism: Price is set high in high-demand periods (because the pricing manager has access to demand information not in the model). The error term $\epsilon$ (unobserved demand) is positively correlated with price. OLS incorrectly attributes part of the demand-driven volume to price, making price look less harmful (or even beneficial) to volume.

Direction: Bias is positive (toward zero or positive). The naïve estimate is less negative than the true elasticity.

Incorrect conclusion: The brand manager believes price can be raised with less volume loss than will actually occur. She uses a break-even calculation that tells her the price increase is profitable, when in truth it may be marginal or loss-making.
</details>

---

**Exercise 2 — Direct application.** You are reviewing a Knorr Soups MMM. The distribution coefficient is $\hat\beta_{\text{WD}} = 620$ units per WD point. Knorr's distribution team tells you: "We always expand distribution first in the fastest-growing grocery regions." Using the confounding framework from today, explain whether this coefficient is likely biased — what type of endogeneity is at play, and in which direction?

<details>
<summary>Reference answer</summary>

Selection endogeneity. Knorr chooses to expand distribution in *already high-growth* markets. These markets would have had above-average volume growth even without Knorr's distribution gain — because of underlying regional demand dynamics. The error term (unobserved regional demand growth) is positively correlated with the distribution variable (gains happen in high-growth regions).

Direction: Upward bias. The distribution coefficient absorbs some of the regional growth that was going to happen independently of distribution. $\hat\beta_{\text{WD}} = 620$ likely overstates the causal effect of one WD point — the true causal effect may be closer to 380–450.

A geo-test (Day 16) that randomly assigns distribution gains across regions would resolve this by breaking the correlation between the distribution decision and the unobserved regional demand growth.
</details>

---

**Exercise 3 — Stretch (callbacks to Days 5 and 8).** Saturation (Day 5) causes spend and response to correlate non-linearly. Promotional endogeneity (Day 8) causes promotional timing to correlate with demand. Together, explain one scenario where these two biases point in *opposite directions* for the promotional coefficient — and what would happen to the net bias if both were present simultaneously.

<details>
<summary>Reference answer</summary>

**Saturation bias direction:** Promotions tend to run at high spend levels, pushing them onto the flatter part of the saturation curve — overstating the marginal response (because the model doesn't fully capture saturation, the coefficient absorbs the level effect rather than the marginal effect). This pushes the coefficient **upward**.

**Timing endogeneity direction:** Promotions tend to run in low-demand periods (to stimulate slow weeks). The error term (unobserved demand weakness) is *negatively* correlated with the promotion flag. OLS sees "promotion ran + moderate volume" and attributes less to the promotion than actually occurred. This pushes the coefficient **downward**.

**Net bias:** These work in opposite directions. If timing endogeneity dominates, the promotional coefficient is understated. If saturation dominates, it is overstated. In practice, for most FMCG brands, timing endogeneity is the larger effect — promotions do run in weak periods — so the net bias is typically downward. But the two effects partially cancel, which is why the naive MMM promotional estimate is sometimes not wildly wrong, even without correction.
</details>

---

**Transfer — apply it:**

> In the most recent regression model you have built (in any domain), name the variable you are most worried is endogenous. Write one sentence describing the mechanism — what unobserved factor jointly determines both the variable and the outcome — and one sentence on the direction of the resulting bias.

---

## Connect It Back

Today established *that* the problem exists and *how* it works. Tomorrow we give you the visual language to describe it: Directed Acyclic Graphs (DAGs). A DAG transforms the bias mechanism from an abstract algebraic formula into a picture that every stakeholder can see and argue about.

**Sharp question to carry forward:** If the demand endogeneity in Surf Excel price could be fully described by a DAG with nodes for Price, Volume, and an unobserved Demand Index — what would the DAG look like, and which path would you need to "block" to identify the causal effect of Price on Volume?

*(Tomorrow's answer: an arrow from Demand → Price and an arrow from Demand → Volume. These form a backdoor path Price ← Demand → Volume. Blocking it requires controlling for Demand or using an instrument that affects Price but not Demand.)*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:** Cunningham (2021), *Causal Inference: The Mixtape*, Chapter 4 (DAGs) — the first 10 pages that introduce the backdoor path concept informally. Read as a preview of tomorrow.

**If you want the deep version:**
- Angrist & Pischke (2009), Chapter 3, Section 3.1 — the omitted variable bias formula derived formally. The derivation above follows their approach; working through their version in full cements the algebra.
- Pearl & Mackenzie (2018), *The Book of Why*, Chapter 4 — "Confounding and Deconfounding." Pearl's narrative treatment of confounding, with historical examples from epidemiology that translate directly to the FMCG context.

---

## Navigation

← **Previous:** [Day 12 — Rest & Synthesize I](../../02-five-ps-as-drivers/days/day-12-rest-synthesize-1.md)
→ **Next:** [Day 14 — DAGs for MMM](./day-14-dags-for-mmm.md)
