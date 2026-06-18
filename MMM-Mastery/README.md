# MMM Mastery: From ML Practitioner to Marketing Mix Modeller

> **The promise:** By Day 30 you will deliver two artefacts a real CMO can act on — a validated Bayesian MMM pipeline in Python and a causal-evidence pricing deck — and you will know exactly what your analysis can and cannot claim.

---

## Who This Course Is For

You are an ML practitioner comfortable with regression, regularisation, and cross-validation. You have heard of Bayesian methods and causal inference but have not worked with them deeply. You are moving into marketing analytics — specifically Marketing Mix Modelling — and you want to do it properly: not just fit a model, but understand what the coefficients mean, where they lie, and how to translate a posterior distribution into a recommendation a Chief Marketing Officer will act on.

This course targets **L2 — Builder** depth. You will implement a working MMM pipeline yourself, debug it at the internals level, and explain every modelling choice to a non-technical stakeholder. If you want to read academic MMM literature or extend the methodology, this course is a launchpad, not a destination. If you only want to interpret someone else's MMM outputs, the first two modules are sufficient and the rest is optional depth.

The single outcome this course optimises for: **you can produce a 10-slide CMO deck with causal evidence justifying a pricing decision, backed by a Python pipeline you built and validated yourself.**

---

## The Arc at a Glance

On Day 1 you will be able to describe what an MMM output means and, critically, what it cannot mean — the honest frame that separates practitioners who earn trust from those who lose it. By Day 12 you have the full 5Ps taxonomy in your hands: you understand how Price, Promotion, Place, Pack, and People each enter the model as a distinct coefficient, why each has its own data challenge, and what the base/incremental decomposition implies for a brand like Surf Excel competing on price in a promotionally saturated category. You have the vocabulary; you do not yet have the tools to trust the numbers.

The middle arc gives you those tools. By Day 19 you can draw a DAG for any real MMM problem, diagnose whether a given coefficient is causally identified, design a geo-test to validate a claim, and instrument away price endogeneity — capabilities that most MMM practitioners skip entirely and that produce the confident-but-wrong outputs that erode CMO trust in the discipline. The final arc turns this rigour into artefacts. You will produce a running Python pipeline generating posterior distributions over all 5Ps, a ranked white-space opportunity list, a geo-expansion priority score with confidence ranges, and a CMO slide deck in which uncertainty is expressed in pound-sterling revenue outcomes, not statistical intervals. Every recommendation explicitly states what the model can support and what it cannot — and that honesty is what makes the deck credible.

---

## How to Use This Course

**Rhythm.** One page per day, 30–45 minutes of focused reading. Do not read ahead. The course is sequenced so that each day's intuition is the prerequisite for the next day's formalism.

**Exercises.** For L2 depth these are non-optional. Exercise 1 on every page requires you to close the page and retrieve the load-bearing idea from memory before you do anything else. This is the single highest-value activity on each page — more valuable than re-reading.

**Bibliography.** Consult `bibliography.md` when a page's "Suggested readings" entry interests you. Do not detour into the bibliography mid-page; finish the page first.

**Rest & Synthesize days.** Days 12 and 19 have no new material. Work from memory first — no re-reading before you attempt the recall table. These days are load-bearing for retention, not optional review.

**Drill days.** Days 18 and 24 are gym sessions, not lectures. Expect discomfort. That discomfort is the signal the drill is working.

**If life gets in the way.** Missing a day is fine; missing a week means re-doing the most recent Rest & Synthesize day before continuing. Never skip a Drill day.

---

## The Learning Path

| Day | Title | One Idea | Module |
|-----|-------|----------|--------|
| [1](./modules/01-foundations/days/day-01-what-mmm-measures.md) | What MMM Actually Measures (and What It Can't) | Attribution ≠ causation | Foundations |
| [2](./modules/01-foundations/days/day-02-data-universe.md) | The Data Universe: Nielsen, Kantar, and Internal | Market data vs. internal data answer different questions | Foundations |
| [3](./modules/01-foundations/days/day-03-base-vs-incremental.md) | Sales Decomposition: Base vs. Incremental | The base/incremental partition is the whole point of MMM | Foundations |
| [4](./modules/01-foundations/days/day-04-adstock-carryover.md) | Adstock & Carryover | Ad effects decay geometrically — the input is a weighted sum of past spend | Foundations |
| [5](./modules/01-foundations/days/day-05-saturation-hill-function.md) | Saturation: The Hill Function | Doubling spend never doubles sales | Foundations |
| [6](./modules/01-foundations/days/day-06-five-ps-framework.md) | The 5Ps Framework | Price, Promotion, Place, Pack, People each become a distinct MMM coefficient | Foundations |
| [7](./modules/02-five-ps-as-drivers/days/day-07-price-elasticity.md) | Price: Elasticity in MMM | Price elasticity is entangled — untangling it is the hard part | 5Ps as Drivers |
| [8](./modules/02-five-ps-as-drivers/days/day-08-promotion-mechanics.md) | Promotion: Trade Mechanics & Borrowed Demand | Promo spikes are often borrowed future demand, not growth | 5Ps as Drivers |
| [9](./modules/02-five-ps-as-drivers/days/day-09-place-distribution.md) | Place: Distribution as a Growth Driver | Numeric and weighted distribution drive base sales independently of advertising | 5Ps as Drivers |
| [10](./modules/02-five-ps-as-drivers/days/day-10-pack-size-elasticity.md) | Pack: Size Elasticity & the Trial-Loyalty Tradeoff | Pack size shifts price-per-use and drives either trial or loyalty | 5Ps as Drivers |
| [11](./modules/02-five-ps-as-drivers/days/day-11-people-trade-execution.md) | People: Trade Execution & Consumer Segments | Shelf compliance and consumer segmentation are measurable MMM drivers | 5Ps as Drivers |
| [12](./modules/02-five-ps-as-drivers/days/day-12-rest-synthesize-1.md) | **Rest & Synthesize I** | Consolidation — no new material | 5Ps as Drivers |
| [13](./modules/03-causal-structure/days/day-13-confounding-problem.md) | The Confounding Problem | Naïve MMM produces wrong decisions because price is endogenous | Causal Structure |
| [14](./modules/03-causal-structure/days/day-14-dags-for-mmm.md) | DAGs for MMM | A DAG makes every causal assumption explicit before fitting anything | Causal Structure |
| [15](./modules/03-causal-structure/days/day-15-identification-limits.md) | Identification: What You Can and Cannot Estimate | Some effects are structurally unidentifiable — no amount of data helps | Causal Structure |
| [16](./modules/03-causal-structure/days/day-16-difference-in-differences.md) | Difference-in-Differences: Geo-Tests | DiD gives causal estimates when rollout is staggered across geographies | Causal Structure |
| [17](./modules/03-causal-structure/days/day-17-instrumental-variables.md) | Instrumental Variables: Solving Price Endogeneity | A valid instrument recovers causal price elasticity from endogenous data | Causal Structure |
| [18](./modules/03-causal-structure/days/day-18-drill-causal-structure.md) | **Drill — Causal Structure** | Six progressive exercises on DAGs, identification, DiD, and IV | Causal Structure |
| [19](./modules/03-causal-structure/days/day-19-rest-synthesize-2.md) | **Rest & Synthesize II** | Consolidation — no new material | Causal Structure |
| [20](./modules/04-pipeline-build/days/day-20-bayesian-priors.md) | Bayesian MMM: Priors as Business Knowledge | Priors are where domain knowledge enters the model formally | Pipeline Build |
| [21](./modules/04-pipeline-build/days/day-21-python-pipeline.md) | Building the Pipeline in Python | Adstock + Hill compose into a probabilistic model fit with MCMC | Pipeline Build |
| [22](./modules/04-pipeline-build/days/day-22-model-validation.md) | Model Validation | Out-of-sample fit + external calibration — no shortcuts | Pipeline Build |
| [23](./modules/04-pipeline-build/days/day-23-competitor-white-space.md) | Competitor Analysis & White Space | Competitor data reveals cross-elasticity; plotting penetration gaps reveals white space | Pipeline Build |
| [24](./modules/04-pipeline-build/days/day-24-drill-decision-translation.md) | **Drill — Decision Translation** | Six exercises converting posteriors to business recommendations | Pipeline Build |
| [25](./modules/05-business-decisions/days/day-25-roi-curves-budget.md) | ROI Curves & Budget Optimisation | Marginal ROI equalised across channels = optimal allocation | Business Decisions |
| [26](./modules/05-business-decisions/days/day-26-pricing-decisions.md) | Pricing Decisions Under Uncertainty | IV-corrected elasticity + margin data = profit distribution over price points | Business Decisions |
| [27](./modules/05-business-decisions/days/day-27-promotion-decisions.md) | Promotion Decisions: Trade ROI & Cannibalization | Most promotions that look profitable are not once cannibalization is modelled | Business Decisions |
| [28](./modules/05-business-decisions/days/day-28-place-people-decisions.md) | Place & People Decisions | Distribution coefficient + salesforce coverage = ranked expansion list | Business Decisions |
| [29](./modules/05-business-decisions/days/day-29-pack-decisions.md) | Pack Decisions: Portfolio Pruning | Pack elasticity + margin + distribution cost = right size for the right market | Business Decisions |
| [30](./modules/05-business-decisions/days/day-30-capstone.md) | **Capstone** | Full pipeline + CMO pricing deck | Business Decisions |

---

## The Course Shelf (Top 5)

| # | Source | Why It Matters |
|---|--------|---------------|
| 1 | Angrist & Pischke, *Mostly Harmless Econometrics* (2009) | The clearest practical treatment of DiD and IV — spine for Days 16–17 |
| 2 | Gelman, Hill & Vehtari, *Regression and Other Stories* (2021) | Best bridge from frequentist to Bayesian thinking — spine for Days 20–22 |
| 3 | Charan, A., *The Marketing Analytics Practitioner's Guide* | FMCG-grounded practitioner lens — spine for 5Ps taxonomy and decision modules |
| 4 | Jin et al. (2017), "Bayesian Methods for MMM with Carryover and Shape Effects" | Defines the adstock and Hill parameterisations used throughout this course |
| 5 | Pearl & Mackenzie, *The Book of Why* (2018) | Non-technical DAG intuition — read before or alongside Days 13–15 |

Full annotated shelf: [bibliography.md](./bibliography.md)

---

## The Capstone

On Day 30 you receive a brief: *Surf Excel is considering a 7% price increase in three South Asian markets. Competitor Ariel has held price. Nielsen data shows category volume declining 2% YoY. Internal data covers 3 years of weekly sales, spend by channel, distribution by account, and salesforce coverage by territory.* Your deliverable is two artefacts: **(1)** a Python notebook producing a fully validated Bayesian MMM with IV-corrected price elasticity, white space analysis across the three markets, pack portfolio recommendation, and posterior distributions over volume and profit at the proposed price point; **(2)** a 10-slide CMO deck with a clear recommendation, the three strongest causal evidence items, explicit statements of what the analysis cannot tell you, and all uncertainty expressed as revenue ranges. Done means a colleague who has not taken this course can run the notebook and make the decision from the deck.

---

## Glossary

[glossary.md](./glossary.md)

---

## Meta

| | |
|--|--|
| **Depth level** | L2 — Builder |
| **Estimated total hours** | 17–22 hours (30 × 35–45 min) |
| **Unilever brands used** | Dove (pipeline), Surf Excel (pricing/promo/pack), Knorr (distribution/geo) |
| **Primary language** | Python (PyMC-Marketing) |
| **Last updated** | June 2026 |
| **Feedback** | Raise issues or suggest corrections by annotating pages directly in your wiki |
