# Day 15 — Identification: What You Can and Cannot Estimate (and Why More Data Won't Help)

> **Today's one idea:** A causal effect is "identified" only when all backdoor paths are blocked — and if the blocking requires an unobserved variable, no amount of additional data closes that gap.
> **Reading time:** ~35 min · **Prereqs:** Day 14
> **Primary source for today:** Huntington-Klein (2021), *The Effect*, Chapter 8 — "Identification"
> **Before you start:** Recall Day 14's load-bearing idea — one sentence: what does a DAG do, and what is a backdoor path?

---

## The Hook

A Unilever data scientist wants to estimate the causal effect of Dove TV advertising on sales. She has 5 years of weekly data — 260 observations. Her manager says "260 data points should be plenty."

She fits the model. The TV coefficient looks reasonable. A colleague with 10 years of data (520 observations) gets almost the same coefficient. A third colleague with 15 years (780 observations) gets the same coefficient still.

They compare notes and realise: all three estimates converge to the same number — not the true causal effect, but a *biased* number. More data did not reduce the bias. The bias is structural — a backdoor path via an unobserved demand variable that no amount of data can block.

Identification is binary: an effect is either identified (the bias is zero, in expectation, given the design) or it is not (the bias is a fixed, non-vanishing distortion). More data helps with *precision* of an identified estimate. It does nothing for *bias* of an unidentified one.

---

## Building the Intuition

### The identification question

"Is this effect identified?" is a question about research design, not data quantity. It asks: *is there a valid path from the variation in the data to the causal quantity of interest?*

Think of it as a plumbing problem. You have a tap (your treatment variable — say, Price) and a sink (Volume). Water flows through pipes (causal and non-causal pathways). Identification means: can you route water *only* through the direct causal pipe Price → Volume, and block all the leaky indirect pipes (backdoor paths)?

```
IDENTIFIED:                          NOT IDENTIFIED:

Price ──────────────────► Volume     Price ──────────────────► Volume
                                           ↑                  ↑
(All backdoor paths blocked)         Demand_U ─────────────────┘
                                     (Unobserved — cannot block)
```

In the identified case, the regression coefficient measures the causal effect. In the unidentified case, it measures a contaminated mixture of the causal effect and the confound.

### Three levels of identification failure

| Level | What's wrong | Example | Fix |
|-------|-------------|---------|-----|
| **Observable confounder, not controlled** | Backdoor path via observed Z, but Z omitted from regression | Competitor price not in Surf Excel model | Add Ariel ASP as control |
| **Unobservable confounder, blockable by design** | Backdoor path via U, but we can design around it | Demand endogeneity — an IV exists | Use IV (Day 17) or geo-test (Day 16) |
| **Unobservable confounder, unblockable** | Backdoor path via U, no valid instrument or experiment available | Long-run brand equity effect on sales | Accept the limit; flag in CMO deck |

Level 3 is the honest frontier of MMM. Some effects cannot be estimated from observational data regardless of methodology. The professional obligation is to identify Level 3 limits clearly and not paper over them with false precision.

### What MMM can identify (with appropriate design)

| Effect | Identifiable? | Method |
|--------|--------------|--------|
| Media ROI at observed spend levels | Partially — with saturation + adstock | Standard MMM (Modules 1–2) |
| True price elasticity | Yes — with IV or experiment | IV (Day 17) or price experiment |
| Causal distribution effect | Mostly — with geo controls + category trend | MMM + geo-blocking |
| Promotional net ROI | Yes — with post-promo lags | Extended MMM (Day 8) |
| Long-run brand equity effect | No — not identified from weekly data alone | Brand tracking + long-run model |
| Pack size causal effect on trial | Partially — confounded by distribution | Kantar + MMM integration |
| Competitive cross-price elasticity | Yes — with competitor price as control | Extended MMM (Day 23) |

Print this table. Paste it inside the front cover of every CMO deck you ever produce.

### The fundamental identification equation

Formally, identification means the causal quantity $P(Y | do(X))$ equals an expressible function of the observational distribution $P(Y, X, Z, \ldots)$:

```math
P(Y | do(X = x)) = \sum_{\mathbf{z}} P(Y | X = x, \mathbf{Z} = \mathbf{z}) \cdot P(\mathbf{Z} = \mathbf{z})
```

This is Pearl's adjustment formula. It says: if you can block all backdoor paths by conditioning on $\mathbf{Z}$, the interventional distribution equals the conditional observational distribution averaged over $\mathbf{Z}$.

The critical phrase: "if you can block all backdoor paths." When you cannot (Level 3 above), the formula does not apply, and no amount of data or sophisticated estimation recovers the causal quantity.

---

## The Formal Picture

### Checking identification for the Knorr distribution example

Recall from Day 14's exercise: the Knorr distribution DAG has a backdoor path via Unobserved Market Growth.

**Backdoor path:** Distribution ← Market Growth → Volume

**Is Market Growth observable?** Partially:
- Category value growth (Nielsen) is a proxy — observed
- Regional population growth (ONS data) is another proxy — observed
- Underlying consumer propensity for convenience foods — not observed

**Identification test:** If including Category Growth and Population Growth in the regression blocks the confounding *sufficiently*, the distribution coefficient is approximately identified. If a significant residual correlation between Distribution and Volume remains after these controls, identification is incomplete.

**Sensitivity analysis.** A rigorous approach estimates how large the remaining unobserved confound would have to be to change the sign of the distribution coefficient:

```python
# Informal sensitivity analysis:
# Estimated distribution coefficient: β_WD = 620
# Standard error: se = 95
# 95% CI: [434, 806]
# 
# For confounding to invalidate this estimate, the unobserved U would need to:
# (1) correlate with WD at r > 0.35 AND
# (2) correlate with Volume at r > 0.40
# 
# Given the controls already included, this is implausible — 
# most reasonable confounds are weaker than this threshold.
# The distribution effect is approximately identified.
```

This informal sensitivity test is sufficient for most CMO presentations. A formal Rosenbaum bounds test provides the rigorous version.

### What "approximately identified" means for the CMO deck

An identified estimate can be interpreted causally: "We estimate that gaining 1 WD point causes a volume increase of approximately 620 units per week."

An unidentified estimate should be interpreted associatively only: "We observe that periods with higher TV spend are associated with higher Dove volume — but we cannot rule out that both are driven by high-demand conditions."

The language difference is not semantic precision for its own sake. It is the difference between a recommendation the CMO can act on with confidence and one she should treat as hypothesis-generating.

### The honest MMM limitations statement

Every MMM output should include a limitations section. After today, you can write it precisely:

```
IDENTIFIED EFFECTS (causal claims supported):
• Promotional net ROI (post-promo lags included, timing endogeneity partially addressed)
• Distribution volume effect (category trend and population controls included;
  residual confounding estimated at < 15% of coefficient magnitude)

PARTIALLY IDENTIFIED EFFECTS (directionally reliable, magnitude uncertain):
• Price elasticity (demand endogeneity not fully corrected without IV;
  true elasticity likely 10-30% more negative than OLS estimate)
• Media ROI (saturation and carryover modelled; long-run equity effects not captured)

NOT IDENTIFIED EFFECTS (associative claims only):
• Long-run brand equity contribution to base
• Pack size causal effect on trial rate (distribution confound)
• People (execution quality) effect (measured only via proxy)
```

---

## Where It Breaks / What It Is Not

**"Identification is a theoretical concern — in practice, the estimates are close enough."** For price elasticity in FMCG, demand endogeneity can cause the elasticity estimate to be 30–50% less negative than the true value. A 30% error in the elasticity used for pricing decisions translates to millions in misallocated margin. This is not a theoretical concern.

**"Adding more variables always helps."** Adding colliders hurts. Adding irrelevant variables reduces degrees of freedom without reducing bias. The right attitude: add variables that block backdoor paths; don't add variables because "more controls is better."

**"If the model fits well, it is identified."** Fit and identification are unrelated. A model can have R² = 0.97 and a completely unidentified price coefficient. In-sample fit measures whether the model captures the associations in the data, not whether the associations are causal.

**"I can always find an instrument."** Finding a *valid* instrument is genuinely hard. In practice, you can find instruments for price endogeneity (input cost shocks, competitor price moves in other markets) and for promotional timing (competitor promotions, regulatory changes). You usually cannot find instruments for long-run brand equity effects or distribution selection effects without an experiment. Day 17 covers what makes an instrument valid — and what makes it weak.

---

## Try It Yourself

> Close the page now before attempting Exercise 1.

**Exercise 1 — Retrieval.** Without looking: what does it mean for a causal effect to be "identified"? Give one example of an MMM effect that can be identified with appropriate design, and one that cannot.

<details>
<summary>Reference answer</summary>

Identified: the causal effect $P(Y|do(X))$ can be computed from observed data — all backdoor paths from $X$ to $Y$ are blocked by observed variables or by the research design.

**Can be identified:** Price elasticity — with an instrumental variable (input cost shocks) or a randomised price experiment, the backdoor path via unobserved demand is blocked.

**Cannot be identified:** Long-run brand equity contribution to base sales — there is no valid instrument or experiment that isolates decades of advertising investment from all other factors that built the brand's current equity position. This remains an associative claim, not a causal one.
</details>

---

**Exercise 2 — Direct application.** A Surf Excel MMM team reports: "We ran the model with more data (5 years instead of 3) and the price elasticity estimate improved from −1.2 to −1.35." A senior analyst says: "That's not an improvement — you just estimated the same biased number more precisely." Using today's framework, evaluate this exchange.

Who is right? Under what condition would the senior analyst be wrong?

<details>
<summary>Reference answer</summary>

**The senior analyst is right** if the underlying identification problem (price endogeneity via unobserved demand) is unchanged across the two datasets. Adding 2 more years of data reduces the standard error of the estimate (more observations → more precise). But if the demand-price correlation is present in all 5 years (which it is, as a structural feature of how Unilever sets prices), the bias is present in all years equally. The estimate converges to a wrong number with higher precision.

**The senior analyst would be wrong** if the additional 2 years included a natural experiment — e.g., a period where Unilever held price constant due to regulatory constraints, or a supply shock that forced price changes independent of demand. In that case, the additional data contains genuinely exogenous variation in price that reduces the endogeneity bias. More data helps *if and only if* it contains more identification variation.
</details>

---

**Exercise 3 — Stretch (callbacks to Days 1 and 9).** Day 1 listed "long-run brand equity contribution to base" as something MMM cannot estimate causally. Day 9 discussed the distribution coefficient as partially identified. Using today's framework, explain the identification status difference between these two cases — why is distribution "approximately identified" but brand equity "unidentified"?

<details>
<summary>Reference answer</summary>

**Distribution (approximately identified):** The confound (Market Growth) is partially observable — category value trend and population growth are proxies. The remaining unobserved confound is relatively small in magnitude (most reasonable Market Growth proxies account for 70–80% of the confounding). Sensitivity analysis shows the coefficient sign is robust to plausible levels of residual confounding. Result: the direction and approximate magnitude of the distribution effect are defensible causal claims.

**Brand equity (unidentified):** The confound (history of all past advertising, product quality decisions, competitive dynamics over decades) is not even partially observable in a weekly time series. There is no valid instrument for "all past advertising investment" — you cannot find an external shock that affected decades of Dove advertising without also affecting other aspects of the brand. The only way to separate brand equity from current marketing effects requires either a very long time series with structural modelling (separating permanent from transitory effects) or consumer-level brand tracking longitudinal data — neither of which is a standard MMM input. The base term in the decomposition captures *the current level of brand equity* but cannot estimate *what caused it* to be that level.
</details>

---

**Transfer — apply it:**

> Look at the most important causal claim in your current or recent work. Which identification level (Level 1: observable confounder not controlled; Level 2: unobservable but fixable by design; Level 3: unblockable) applies to it? Write one sentence on what you would need to move it to a higher identification level.

---

## Connect It Back

Identification is a binary property of research design. Days 16 and 17 give you the two most powerful tools for moving Level 2 problems to identified status: Difference-in-Differences (for geo-level effects) and Instrumental Variables (for price endogeneity). If you found today's page unsatisfying because it told you what you cannot do without yet telling you how to fix it — that discomfort is intentional. Tomorrow it resolves.

**Sharp question to carry forward:** DiD (Day 16) will require a "parallel trends" assumption. IV (Day 17) will require an "exclusion restriction." Both are *untestable in the post-treatment period*. How is this different from the identification problem you just described — where the failure was that an unobserved confounder exists?

*(Answer: DiD and IV replace one unverifiable assumption (no confounding) with a different, but narrower and more defensible unverifiable assumption. The goal is not to eliminate all assumptions — it is to replace broad, implausible assumptions with specific, testable-in-pre-period assumptions.)*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:** Huntington-Klein (2021), *The Effect*, Chapter 8 (Identification) — the first 12 pages. Huntington-Klein's plain-English treatment of identification is the best available for non-economists; it directly extends today's intuition into worked examples.

**If you want the deep version:**
- Angrist & Pischke (2009), Chapter 2, Section 2.1 — "The Experimental Ideal." The identification ideal is a randomised controlled trial; Angrist & Pischke set this up as the benchmark against which all observational methods are measured. Read this to understand why DiD and IV are valuable: they approximate the experimental ideal without running an experiment.
- Chan & Perry (2017), "Challenges and Opportunities in Media Mix Modeling" [VERIFY] — the section on identification challenges in MMM. The authors are honest about which MMM outputs are and are not identified. Compare their list to the table produced today.

---

## Navigation

← **Previous:** [Day 14 — DAGs for MMM](./day-14-dags-for-mmm.md)
→ **Next:** [Day 16 — Difference-in-Differences: Geo-Tests as Causal Ground Truth](./day-16-difference-in-differences.md)
