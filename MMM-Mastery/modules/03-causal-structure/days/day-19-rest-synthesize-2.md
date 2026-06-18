# Day 19 — Rest & Synthesize II: Causal Structure Arc

> **Today:** No new concepts. Only consolidation of the Causal Structure arc (Days 13–18).
> **Format:** Re-state → Re-derive → Full run → Quiz
> **Reading time:** ~35 min (longer if gaps surface — that's the point)
> **Prereqs:** [Days 13–18](../../../README.md)

Module 3 gave you the single most important capability in this course: you can now look at any MMM coefficient and ask whether it is identified — and if not, what specific design would identify it. The pipeline build (Module 4) starts tomorrow and it builds directly on this foundation. A Bayesian prior set on a biased coefficient is a precisely wrong answer.

**Close all previous pages. Work from memory first.**

---

## Step 1 — Re-state the 6 Causal Arc Ideas (5 min)

> Scrambled order below — no two adjacent rows are from consecutive days.

Write one sentence for each without looking anything up.

| Concept | Your sentence |
|---------|--------------|
| Instrumental Variables — two conditions (Day 17) | |
| The Confounding Problem — mechanism for price endogeneity (Day 13) | |
| Identification — what Level 3 means (Day 15) | |
| DAGs — the four node structures (Day 14) | |
| DiD — the parallel trends assumption (Day 16) | |
| Identification — Level 1 vs Level 2 (Day 15) | |

<details>
<summary>Compare to these</summary>

1. **IV (Day 17):** A valid instrument must satisfy relevance ($\text{Cov}(Z, \text{Price}) \neq 0$, testable) and the exclusion restriction ($\text{Cov}(Z, \epsilon) = 0$, untestable — requires economic argument); 2SLS uses the instrument to extract only the exogenous variation in price for the second-stage elasticity estimate.

2. **Confounding (Day 13):** Price is set high in high-demand periods; unobserved demand correlates with both Price (positive) and Volume (positive), creating a positive bias in the OLS elasticity estimate — the naïve coefficient is less negative (or positive) relative to the true causal elasticity.

3. **Level 3 identification (Day 15):** An unobservable confounder for which no valid instrument or experiment exists; the causal effect cannot be estimated from observational data regardless of sample size — must be flagged as "associative claim only" in the CMO deck.

4. **Four DAG structures (Day 14):** Chain (A→B→C: mediator — conditioning blocks mediation); Fork (A←B→C: confounder — conditioning deconfounds); Collider (A→B←C: conditioning on collider opens a spurious path — do not condition); Direct (A→C: nothing to do).

5. **Parallel trends (Day 16):** In the absence of treatment, the treated group would have followed the same trend as the control group; testable in the pre-treatment period (no pre-trend divergence), untestable in the post-treatment period; required for DiD to produce a causal estimate.

6. **Level 1 vs Level 2 (Day 15):** Level 1 = observable confounder omitted from the model (fix by adding control variable); Level 2 = unobservable confounder but fixable by design (use IV for endogenous price; use DiD/geo-test for geographic confounds). Both are solvable — unlike Level 3.
</details>

---

## Step 2 — Re-derive the Causal Toolkit Decision Tree (10 min)

Fill in the blanks in the causal diagnosis and remediation decision tree. Every blank is labelled.

```
START: You have an MMM coefficient you want to interpret causally.
↓
Step 1: Draw the [A: ___]. 
        List all paths between your driver X and outcome Y.
↓
Step 2: Identify all [B: ___] paths — paths that create spurious
        associations between X and Y without X causing Y.
↓
Step 3: For each backdoor path, ask: is the confounding variable 
        [C: ___] (observed) or [D: ___] (unobserved)?
        ↓                              ↓
    OBSERVED:                      UNOBSERVED:
    Add to regression              Does a valid [E: ___] exist?
    → Level [F: ___] fixed         ↓              ↓
                                  YES            NO
                                  Use [G: ___]   Level [H: ___] — 
                                  or [I: ___]    flag as [J: ___]
↓
Step 4: Check for [K: ___] in your control variables.
        Conditioning on these [L: ___] (opens/closes?) spurious paths.
↓
Step 5: Validate the identified coefficient with:
        - [M: ___] for geographic effects
        - [N: ___] for price effects
```

<details>
<summary>Filled-in decision tree</summary>

- **A:** DAG (Directed Acyclic Graph)
- **B:** Backdoor
- **C:** Observable
- **D:** Unobservable
- **E:** Instrument (IV) or natural experiment
- **F:** 1 (Level 1 — add observable control)
- **G:** IV (Instrumental Variables) for price/continuous endogenous variables
- **H:** 3 (Level 3 — structurally unidentified)
- **I:** DiD (for geographic/time-varying treatments)
- **J:** Associative claim only (not causal)
- **K:** Colliders
- **L:** Opens (conditioning on a collider opens a spurious path)
- **M:** DiD / geo-test (with parallel trends validation)
- **N:** IV with strong first stage (F > 10) and defensible exclusion restriction
</details>

**Questions to answer without looking:**

1. The palm oil price index satisfies IV relevance. Name the *specific* argument you would make for the exclusion restriction — and the *specific* violation you would flag as a caveat.
2. In a DiD design, what is the consequence of a significant pre-trend (Yorkshire growing faster than controls for 6 months before the initiative)?
3. A backdoor path runs through an unobserved demand variable. You cannot find a valid instrument. What is your professional obligation in the CMO deck?

<details>
<summary>Answers</summary>

1. Exclusion argument: palm oil prices are driven by Southeast Asian agricultural conditions and global commodity markets — not by UK consumer demand for laundry. Violation caveat: if palm oil affects the price of cooking oil and other household products simultaneously, the palm oil spike may reduce overall household FMCG spending through an income effect — directly affecting Surf Excel volume without going through Surf Excel price.

2. Significant pre-trend means the parallel-trends assumption is violated for the base DiD. The DiD estimate will be biased. Possible remediation: regression-adjust for the pre-existing trend (add a "treated region × time" interaction term to absorb the differential growth rate); or narrow the control group to regions with demonstrably parallel pre-trends.

3. Professional obligation: state explicitly in the CMO deck that the relevant coefficient is an associative estimate, not a causal one; provide the range of sensitivity (how wrong would the decision be if the true effect is 30%, 50%, 100% stronger/weaker than estimated); recommend a validation experiment before making large budget commitments based on this coefficient.
</details>

---

## Step 3 — Run / Apply It (10 min)

If you have access to any MMM dataset or model output (from work or the earlier hypotheticals), work through this checklist. Otherwise, apply it mentally to the Surf Excel Pakistan scenario.

- [ ] **Price coefficient:** What is the OLS estimate? Does an IV exist? If not, estimate the direction and approximate magnitude of the bias using the omitted variable bias formula.
- [ ] **Promotion coefficient:** Are post-promo lags included? If not, is the stated promotional ROI likely an overstatement or understatement?
- [ ] **Distribution coefficient:** What controls are included to address selection endogeneity? Is there a geo-test that could validate the coefficient?
- [ ] **Media coefficients (TV, digital):** Is there a DiD-validated estimate available? If not, note this as a limitation.
- [ ] **Base share:** Is it stable, growing, or declining? What is the business implication of the trend?
- [ ] **Overall identification status:** Write one sentence on how many of the five Ps are approximately identified in this model, and what fraction of the model's outputs you would describe as causal claims vs. associative claims.

Document your answers — this checklist becomes the "limitations" section of your CMO deck on Day 29.

---

## Step 4 — Quiz (5 min)

> Scrambled order. Q5 requires combining two arc concepts.

1. What is the direction of the bias in the distribution coefficient when distribution is always expanded in high-growth markets — and what data would you add to the regression to reduce this bias?

2. An MMM team claims: "We used Bayesian priors on the price coefficient centred at −1.8, so the endogeneity problem is solved." Using today's consolidation, write the one-sentence rebuttal.

3. Describe a scenario where a DiD design would give you a *more negative* price elasticity estimate than OLS — i.e., DiD would suggest price is more harmful to volume than OLS. Under what conditions does this happen?

4. You have three proposed instruments for price: palm oil index (F = 45), Ariel Germany price (F = 28), UK electricity price (F = 6). Which instruments do you use, which do you drop, and what test do you run when using two instruments together?

5. **Cross-concept:** A DAG (Day 14) and the parallel trends assumption (Day 16) are both about causal assumptions that are made *before* seeing the data and cannot be fully tested. Explain the key structural difference between what each assumption rules out — and why this means a DAG alone is not sufficient to design a valid DiD study.

<details>
<summary>Answers</summary>

1. **Upward bias** (distribution looks more effective than it is because it was deployed in already-growing markets). To reduce bias: add category volume growth by geography as a control; add population growth by region; add pre-period volume trend as a covariate. These observable proxies for market growth partially block the selection backdoor path.

2. "Bayesian priors shift the posterior toward the prior mean, which constrains the estimate against implausible values — but they do not fix the identification problem. The bias is in the likelihood (the data generation process), not the prior. With enough data, the posterior will converge toward the biased OLS estimate regardless of the prior."

3. DiD would give a more negative elasticity if OLS suffered from *positive* endogeneity bias that DiD removed. Specifically: if prices were raised in high-demand periods (standard FMCG scenario), OLS underestimates volume loss. A DiD design where a price change was rolled out to some geographies but not others for *exogenous* reasons (e.g., regulatory price caps in some regions) would isolate the volume response without the demand-driven price-setting behaviour — revealing the true, more negative elasticity. This scenario is unusual for price (most price changes are national) but possible in regulated or multi-market contexts.

4. Use: palm oil (F = 45, strong) and Ariel Germany (F = 28, good). Drop UK electricity (F = 6, weak instrument). With two instruments, run the **Sargan-Hansen J-test (overidentification test)** to check whether both instruments give consistent elasticity estimates. A significant J-statistic suggests one instrument violates the exclusion restriction.

5. **Structural difference:** A DAG specifies which arrows exist (causal relationships) — it rules out specific direct causal connections (no arrow = no direct cause). The parallel trends assumption rules out a specific *trend pattern* — that the treated group was on a diverging trajectory before treatment began, independent of any arrow in the DAG. A DAG can be correct (all causal relationships properly specified) while the parallel trends assumption still fails, because the treated group could be trending differently from the control group due to selection (which actors you chose to treat) rather than a causal mechanism. **Why DAG is not sufficient:** A correct DAG tells you that the treatment is the cause you want to measure. But if you selected treated and control units that were already diverging before treatment, the DiD estimate is biased even if the DAG is correctly specified — you have a selection bias problem that the DAG describes but DiD cannot fix without the parallel trends assumption. The DAG and the parallel trends assumption answer different questions: the DAG maps the causal structure; the parallel trends assumption validates the comparability of your selected units.
</details>

---

## What's Ahead

| Day | Topic | What the reader will build |
|-----|-------|--------------------------|
| 20 | Bayesian Priors as Business Knowledge | Encode domain knowledge into prior distributions for each MMM parameter |
| 21 | Building the Pipeline in Python | Fit a full Bayesian MMM on Dove data using PyMC-Marketing |
| 22 | Model Validation | Out-of-sample MAPE, posterior predictive checks, DiD calibration |

The causal arc gave you the critical lens. The pipeline build arc gives you the implementation. Starting Day 20, you will build a model that encodes what you now know — not just a regression, but a probabilistic model with explicit priors, validated against external causal estimates.

---

## Connect It Back

The causal arc covered two fundamental skills: diagnosing whether an MMM coefficient is identified (Days 13–15) and recovering causal estimates when it is not (Days 16–17). Day 18 stress-tested these skills under pressure. What you carry into the pipeline build: scepticism about every coefficient, plus specific remediation tools for the most important P (Price), the most geographic P (Place), and the most temporally complex P (Promotion). The Bayesian model you build on Day 21 will be better because you know what to trust and what to doubt.

**The question you carry into Module 4:** In the Bayesian MMM you are about to build, where do the priors come from — and are any of them encoding a biased OLS estimate as if it were true?

---

## Navigation

← **Previous:** [Day 18 — Drill: Causal Structure](./day-18-drill-causal-structure.md)
→ **Next:** [Day 20 — Bayesian MMM: Priors as Business Knowledge](../../04-pipeline-build/days/day-20-bayesian-priors.md)
