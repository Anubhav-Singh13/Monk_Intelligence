# Module 4 — Pipeline Build

**Days 20–24 · ~3.5 hours**

## What This Module Earns You

By the end of Day 24 you can fit a full Bayesian MMM in PyMC-Marketing, validate it against external causal benchmarks (DiD/IV calibration), perform competitor cross-price elasticity analysis and own-portfolio cannibalization modelling, produce a white space CDI/BDI opportunity map, and translate all model outputs into uncertainty-aware business decisions. You no longer report point estimates; you carry uncertainty as posterior distributions and communicate it in terms a commercial director can act on.

## The Story of This Module

Module 3 ended with you knowing which effects are identified and which are not. Module 4 asks: now that you have that knowledge, can you build and trust a model?

The arc has a deliberate shape. Day 20 resolves the most common source of confusion about Bayesian modelling — the prior — by reframing it as encoded business knowledge rather than a subjective guess. Day 21 builds the full pipeline from raw inputs to posterior samples. Day 22 introduces two tests that most practitioners run only one of: fit quality (MAPE) and causal validity (DiD/IV calibration). They are not the same test, and a model can pass one while failing the other. Day 23 extends the model outward to competitors and inward to own-portfolio substitution, then maps investment gaps using CDI/BDI. Day 24 is the gym: the rate-limiting sub-skill is not fitting the model but translating a posterior into a defensible business recommendation under uncertainty.

The module completes the analyst toolkit. Module 5 applies it to each of the five business decision types.

## Days in This Module

| Day | Title | Load-Bearing Idea | Key Python | Prereqs |
|-----|-------|-------------------|------------|---------|
| [20](./days/day-20-bayesian-priors.md) | Bayesian Priors as Business Knowledge | A prior is encoded domain expertise not a guess; `Normal(-1.5, 0.5)` on price elasticity encodes "I would be surprised if this is positive or worse than -2.5" | PyMC prior specification | Days 1–19 |
| [21](./days/day-21-mmm-pipeline.md) | Building the MMM Pipeline in Python | PyMC-Marketing turns the MMM equation into a probabilistic model where every coefficient becomes a distribution | PyMC-Marketing full fit | Days 4–5, 20 |
| [22](./days/day-22-model-validation.md) | Model Validation | Fit quality (MAPE) and causal validity (DiD calibration) are different tests; a model can score well on both or on neither | Posterior predictive check, holdout MAPE | Days 16–17, 21 |
| [23](./days/day-23-competitor-portfolio-whitespace.md) | Competitor Analysis, Portfolio Cross-Elasticity and White Space | Cross-price elasticity extends MMM outward to competitors and inward to own-portfolio substitution; white space maps CDI/BDI gaps to investable growth opportunities | System of equations, CDI/BDI grid | Days 7, 9, 10 |
| [24](./days/day-24-drill-decision-translation.md) | **Drill — Decision Translation** | The rate-limiting sub-skill is not model fitting but translating a posterior distribution into a defensible uncertainty-aware business recommendation | None (drill day) | Days 20–23 |

## Navigation

← **Previous module:** [03-causal-structure](../03-causal-structure/overview.md)  
→ **Next module:** [05-business-decisions](../05-business-decisions/overview.md)
