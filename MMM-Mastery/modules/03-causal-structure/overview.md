# Module 3 — Causal Structure

**Days 13–19 · ~4 hours**

## What This Module Earns You

By Day 19 you can do something most MMM practitioners cannot: you can draw the causal graph behind any MMM problem *before* fitting anything, identify which effects are identified and which are not, design a geo-test to recover a causal estimate, and instrument away price endogeneity. These are not theoretical capabilities — they are the specific tools that prevent confident-but-wrong recommendations.

## The Story of This Module

Module 2 ended with a quiet warning: every coefficient you just learned to interpret could be biased. Module 3 makes that warning precise and actionable.

The module has a tight internal logic. Day 13 shows *that* the problem exists (confounding, endogeneity). Day 14 gives you the language to describe *what* the problem is (DAGs). Day 15 tells you *when* the problem is fixable and when it is not (identification). Days 16 and 17 give you the two main tools to fix it (DiD, IV). Day 18 is the gym — six exercises applying all of the above. Day 19 consolidates.

The rate-limiting sub-skill appears here for the first time under full pressure: causal structure specification. Most learners can follow the logic of a DAG they are shown. The bottleneck is drawing one from scratch for a new problem — knowing which arrows to include, which common causes to worry about, and which backdoor paths to block. Day 18 drills exactly this.

## Days in This Module

| Day | Title | One Idea |
|-----|-------|----------|
| [13](./days/day-13-confounding-problem.md) | The Confounding Problem | Naïve MMM produces wrong decisions because price is endogenous |
| [14](./days/day-14-dags-for-mmm.md) | DAGs for MMM | A DAG makes every causal assumption explicit before fitting |
| [15](./days/day-15-identification-limits.md) | Identification: What You Can and Cannot Estimate | Some effects are structurally unidentifiable — no amount of data helps |
| [16](./days/day-16-difference-in-differences.md) | Difference-in-Differences: Geo-Tests | DiD gives causal estimates from staggered geographic rollouts |
| [17](./days/day-17-instrumental-variables.md) | Instrumental Variables: Solving Price Endogeneity | A valid instrument recovers causal elasticity from endogenous data |
| [18](./days/day-18-drill-causal-structure.md) | **Drill — Causal Structure** | Six progressive exercises |
| [19](./days/day-19-rest-synthesize-2.md) | **Rest & Synthesize II** | Consolidation — no new material |

← **Previous module:** [02-five-ps-as-drivers](../../02-five-ps-as-drivers/overview.md)
→ **Next module:** [04-pipeline-build](../../04-pipeline-build/overview.md)
