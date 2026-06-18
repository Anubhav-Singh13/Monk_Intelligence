# Day 1 — What MMM Actually Measures (and What It Can't)

> **Today's one idea:** MMM attributes observed sales to drivers, but attribution is not causation — and confusing the two produces decisions that backfire.
> **Reading time:** ~35 min · **Prereqs:** None
> **Primary source for today:** Pearl, J. & Mackenzie, D. (2018). *The Book of Why*, Chapter 1 — "The Ladder of Causation"
> **Before you start:** Day 1 — no prior page. Arrive curious.

---

## The Hook

A Unilever marketing director presents her annual MMM results to the Dove leadership team. The model says TV advertising drove 28% of Dove Body Wash sales last year — the single largest incremental driver. The brand's P&L is under pressure. She recommends cutting TV spend by 40%.

The following year, sales drop 31%.

The MMM wasn't wrong about the past. It correctly described the association between TV spend and sales in the historical data. What it could not tell her — and what nobody asked — was whether TV *caused* those sales, or whether TV and sales were both driven by a third factor she hadn't measured.

This distinction — attribution vs. causation — is the entire course in a sentence. Every tool, every technique, every day of work from here forward exists to either respect this distinction carefully or close the gap between them.

---

## Building the Intuition

### The thermometer and the fever

Imagine you are a doctor who has noticed that patients with high temperatures tend to recover faster. You run a regression: temperature predicts recovery speed. A naive reader of that regression might conclude: "raise the patient's temperature to speed recovery." Of course that's absurd — temperature is a symptom, not a cause. The underlying infection drives both.

MMM coefficients are, at their core, thermometers. They tell you *what moved with what*. Whether the movement was causal — whether changing the input *would change* the output — is a different question entirely, and one that standard MMM regression cannot answer on its own.

### The three rungs of the causal ladder

Pearl & Mackenzie organise what we can know about cause and effect into three levels:

```
Rung 3 — Counterfactual   "What would have happened if we hadn't run the TV campaign?"
                           ↑  requires causal model + assumptions
Rung 2 — Intervention     "What will happen if we cut TV spend by 40%?"
                           ↑  requires causal identification (Modules 3–4)
Rung 1 — Association      "How much did TV spend co-vary with sales historically?"
                           ↑  this is what standard MMM gives you
```

Standard MMM lives on Rung 1. A well-designed causal MMM — with geo-tests, instrumental variables, and validated DAGs — can reach Rung 2 for most decisions. Rung 3 (pure counterfactuals) is rarely achievable in practice and should be flagged honestly in any CMO deck.

### What MMM is actually doing

The model fits a regression of the form:

```math
\text{Sales}_t = \underbrace{\alpha + \beta_{\text{trend}} \cdot t + \beta_{\text{season}} \cdot s_t}_{\text{base}} + \underbrace{\sum_{k} \beta_k \cdot f(x_{kt})}_{\text{incremental}} + \epsilon_t
```

where:
- $\alpha$ is the long-run baseline
- $s_t$ captures seasonality
- $x_{kt}$ is the $k$-th driver (TV spend, price, distribution, etc.) at time $t$
- $f(\cdot)$ is a transformation applied to the driver (more on this Days 4–5)
- $\beta_k$ is the coefficient — the estimated association between driver $k$ and sales

The coefficients $\beta_k$ are estimated by minimising the residuals $\epsilon_t$. They describe the historical data. They do not, by themselves, answer the question "what would happen if I change $x_k$?"

### What MMM can and cannot do — the honest inventory

| MMM Can | MMM Cannot (without additional design) |
|---------|---------------------------------------|
| Decompose past sales into attributed contributions per driver | Prove that any driver *caused* those sales |
| Rank drivers by historical association strength | Guarantee rankings are stable if spend levels change dramatically |
| Estimate marginal ROI *at current spend levels* | Predict ROI at spend levels far outside historical range |
| Reveal which channels co-vary most with sales | Separate brand equity effects from media effects without long time series |
| Quantify the scale of seasonal and trend effects | Identify the *cause* of trend changes (new competitor? distribution loss?) |
| Provide budget reallocation guidance within historical norms | Tell you what happens if you exit a channel entirely |
| Serve as a starting point for causal investigation | Replace a geo-experiment or natural experiment |

Keep this table. You will return to it on Day 15 (identification limits) and Day 29 (CMO deck honesty).

---

## The Formal Picture

The MMM equation above is a **multiple linear regression** in transformed variables. The standard OLS estimate of $\boldsymbol{\beta}$ is:

```math
\hat{\boldsymbol{\beta}} = (\mathbf{X}^T \mathbf{X})^{-1} \mathbf{X}^T \mathbf{y}
```

where $\mathbf{X}$ is the design matrix of transformed driver variables and $\mathbf{y}$ is the vector of observed sales.

This estimator is *unbiased* (on average correct) only when the standard OLS assumptions hold — in particular when:

```math
\mathbb{E}[\epsilon_t \mid \mathbf{x}_t] = 0
```

That condition says: *the error term is uncorrelated with the drivers*. In other words, there is no unmeasured variable that both affects sales and correlates with what we are calling a driver.

**This assumption fails constantly in real MMM data.** The most common failure:
- Price is set *in response to* demand conditions → $\text{Cov}(\text{price}, \epsilon) \neq 0$
- Media spend is increased during known high-demand periods → $\text{Cov}(\text{TV spend}, \epsilon) \neq 0$

When this assumption fails, $\hat{\boldsymbol{\beta}}$ is biased. The direction and size of the bias depends on the direction and strength of the correlation between the driver and the error — which is exactly what Module 3 of this course is designed to address.

---

## Where It Breaks / What It Is Not

**Misconception 1: "Higher R² means the MMM is more reliable."**
R² measures how well the model fits historical data. A model can have R² = 0.98 and still produce completely wrong coefficient estimates if the drivers are correlated with unmeasured confounders. Fit and identification are orthogonal.

**Misconception 2: "We ran the model on 3 years of data — that's plenty."**
No amount of data fixes a structurally biased estimator. If price is endogenous, 10 years of data gives you a more precisely wrong estimate, not a correct one.

**Misconception 3: "The model says TV drives 28% of sales, so TV is important."**
It says TV *co-varies with* 28% of the sales variation in that historical window. Whether TV caused those sales, whether that relationship holds in a different spend regime, and whether cutting TV would recover that 28% elsewhere are all separate questions.

**Misconception 4: "MMM is a black box."**
The core model is a linear regression with transformed inputs. Every assumption is explicit. The "black box" reputation comes from vendors who do not explain their transformations — not from the methodology itself.

---

## Try It Yourself

> Close this page now. Do not look back until you have attempted Exercise 1 below.

**Exercise 1 — Retrieval.** Without looking at anything above: write down in your own words what an MMM output tells you and one thing it cannot tell you. One sentence each. Then open the page and compare.

<details>
<summary>Reference answer</summary>

MMM tells you: *how much each driver co-varied with sales in the historical data, and in what proportion.*

MMM cannot tell you: *whether changing a driver would cause a change in sales (because correlation is not causation and the endogeneity assumption may fail).*

If your answer captured these two ideas in any form, it is correct.
</details>

---

**Exercise 2 — Direct application.** You are reviewing a Knorr MMM result that says "Distribution drove 35% of sales." Your brand manager is about to present this to the Sales Director and recommend a 20% increase in distribution investment.

List two questions you would ask *before* endorsing that recommendation, drawing only on today's material.

<details>
<summary>Reference answer</summary>

1. *Is the distribution coefficient identified — i.e., was distribution ever cut or held flat in any geography during the historical window, giving the model variation to work with? If distribution only ever increased, the 35% figure may be absorbing trend.*
2. *Is there an experiment or geo-test we can reference to validate this coefficient before committing budget?*

These two questions operationalise the attribution ≠ causation distinction in a real business setting.
</details>

---

**Exercise 3 — Stretch.** A colleague argues: "But we use Bayesian MMM with informative priors, so the causal problem doesn't apply to us." Using only the content from today's page, explain why this argument is wrong — and what Bayesian priors can and cannot fix.

<details>
<summary>Reference answer</summary>

Bayesian priors constrain the *plausibility* of coefficient estimates — they prevent absurd values (e.g., a negative TV coefficient when business knowledge says TV helps). But they do not change the *identification* of the effect. If price is endogenous (correlated with the error), an informative prior on the price coefficient will shift the posterior toward the prior mean, but the posterior will still be biased away from the true causal effect. Priors fix regularisation problems; they do not fix identification problems. (We will prove this formally on Day 20.)
</details>

---

**Transfer — apply it:**

> Name a situation from your own current work where someone is treating a correlation as a cause and making a resource decision based on it. Write one concrete sentence: what the input is, what the observed correlation is, and what the hidden confounder might be. If nothing comes to mind in 60 seconds, that is the signal — re-read the thermometer analogy.

---

## Connect It Back

Today's page established the epistemological foundation for the entire course: MMM produces associations, not causes. Tomorrow we look at the raw material — the data landscape of Nielsen, Kantar, and internal systems — and ask which variables even belong in the model. A misunderstood data join is often where the confounding problem begins.

**Sharp question to carry forward:** If you were shown an MMM output tomorrow and asked "is this coefficient causal?", what is the one thing you would need to know about how the data was collected?

*(Answer: whether the driver variable was ever manipulated independently of the outcome — i.e., whether there is exogenous variation in it.)*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:** Pearl & Mackenzie (2018), *The Book of Why*, Chapter 1 "The Ladder of Causation" — pages 1–30. Sets the philosophical foundation for why the attribution/causation distinction matters and why it took statistics so long to formalise it.

**If you want the deep version:**
- Chan, D. & Perry, M. (2017). "Challenges and Opportunities in Media Mix Modeling." Google Technical Report. [VERIFY] — Section 1 (Introduction and Challenges). A practitioner paper that lists, with remarkable honesty, the structural problems of MMM that this course addresses.
- Cunningham (2021), *Causal Inference: The Mixtape*, Chapter 1 — free at mixtape.scunning.com. Sets up the potential outcomes framework that underlies all of Modules 3–4.

---

## Navigation

← **Back to course overview:** [README](../../../README.md)
→ **Next:** [Day 2 — The Data Universe](./day-02-data-universe.md)
