# Learning Path — MMM Mastery

Full day-by-day table with load-bearing idea, rationale, prerequisites, and spaced-retrieval callbacks.

---

| Day | Title | Load-Bearing Idea | Why It Comes Now | Prereqs | Callbacks |
|-----|-------|------------------|-----------------|---------|-----------|
| [1](./modules/01-foundations/days/day-01-what-mmm-measures.md) | What MMM Actually Measures (and What It Can't) | Attribution is not causation — and confusing the two produces decisions that backfire | Sets the honest frame; everything else builds on knowing the limits first | — | — |
| [2](./modules/01-foundations/days/day-02-data-universe.md) | The Data Universe: Nielsen, Kantar, and Internal | Market data tells you what happened to the category; internal data tells you what you did | Every model lives on this data; a misread data architecture means a misread model | Day 1 | — |
| [3](./modules/01-foundations/days/day-03-base-vs-incremental.md) | Sales Decomposition: Base vs. Incremental | Every MMM output is a partition of sales into base and incremental — this partition is the whole point | Establishes the core mental model before any equations | Days 1–2 | — |
| [4](./modules/01-foundations/days/day-04-adstock-carryover.md) | Adstock & Carryover | Ad effects decay geometrically — the input is a weighted sum of past spend, not current spend | First transformation required; without it all media coefficients are wrong | Day 3 | — |
| [5](./modules/01-foundations/days/day-05-saturation-hill-function.md) | Saturation: The Hill Function | Doubling spend never doubles sales; where you sit on the saturation curve determines ROI | Second transformation; pairs with adstock to make media inputs model-ready | Day 4 | — |
| [6](./modules/01-foundations/days/day-06-five-ps-framework.md) | The 5Ps Framework | Price, Promotion, Place, Pack, and People each become a distinct MMM coefficient with its own identification challenge | Frames all subsequent days; every P gets its own driver day and decision day | Days 1–5 | Day 1 (what can MMM claim about each P?) |
| [7](./modules/02-five-ps-as-drivers/days/day-07-price-elasticity.md) | Price: Elasticity in MMM | Price elasticity in MMM is entangled with pack, promo, and competitor pricing — untangling it is the hard part | Pricing is the primary CMO output; establish the concept before confounding explains why it is so hard | Days 3–6 | Day 1 (can MMM claim causal elasticity?) |
| [8](./modules/02-five-ps-as-drivers/days/day-08-promotion-mechanics.md) | Promotion: Trade Mechanics & Borrowed Demand | Promotions create volume spikes that are often demand borrowed from the future, not genuine growth | Most misread P in naïve MMM; sets up the confounding discussion | Days 4, 6–7 | Day 3 (is promo volume base or incremental?) |
| [9](./modules/02-five-ps-as-drivers/days/day-09-place-distribution.md) | Place: Distribution as a Growth Driver | Numeric and weighted distribution drive base sales independently of all above-the-line investment | Distribution is often the largest single lever; Knorr's foodservice expansion makes it intuitive | Days 3, 6 | Day 4 (distribution gains also have carryover) |
| [10](./modules/02-five-ps-as-drivers/days/day-10-pack-size-elasticity.md) | Pack: Size Elasticity & the Trial-Loyalty Tradeoff | Pack size shifts price-per-use and drives either trial or loyalty — each has a different elasticity signature | Pack is a key portfolio decision variable; trial-loyalty distinction recurs in white space and decision modules | Days 6–8 | Day 5 (saturation differs by pack size cohort) |
| [11](./modules/02-five-ps-as-drivers/days/day-11-people-trade-execution.md) | People: Trade Execution & Consumer Segments | Salesforce effectiveness and shelf compliance are measurable MMM drivers that explain distribution quality beyond numeric distribution | Closes the 5Ps; Dove's tiered retail structure makes shelf compliance differences visible | Days 6–10 | Day 6 (People was the implicit P in the framework) |
| [12](./modules/02-five-ps-as-drivers/days/day-12-rest-synthesize-1.md) | **Rest & Synthesize I** | Consolidation — no new material | The 5Ps must be clear as a taxonomy before causal machinery arrives | Days 1–11 | Days 3, 4, 6, 9, 10 (scrambled recall) |
| [13](./modules/03-causal-structure/days/day-13-confounding-problem.md) | The Confounding Problem | Naïve MMM produces wrong decisions because price is set endogenously — high when demand is already high | Motivates the entire causal arc; without this day, learners don't see why the tools are necessary | Days 1–11 | Day 5 (saturation creates its own spurious correlations), Day 8 (promo timing is also endogenous) |
| [14](./modules/03-causal-structure/days/day-14-dags-for-mmm.md) | DAGs for MMM | A DAG makes every causal assumption explicit before any model is fit | First causal tool; forces the learner to externalise implicit assumptions | Day 13 | Day 7 (draw the DAG for Surf Excel price elasticity) |
| [15](./modules/03-causal-structure/days/day-15-identification-limits.md) | Identification: What You Can and Cannot Estimate | Some MMM effects are structurally unidentifiable regardless of sample size — this is a design problem, not a data problem | Grounds the DAG in practical limits; prevents overconfidence in any coefficient | Day 14 | Day 9 (is the Knorr distribution coefficient identified?), Day 1 (honest limits resurface) |
| [16](./modules/03-causal-structure/days/day-16-difference-in-differences.md) | Difference-in-Differences: Geo-Tests as Causal Ground Truth | DiD gives a causal estimate when a marketing action rolls out to some geographies but not others | First causal estimation method; directly actionable for geography-expansion decisions | Days 14–15 | Day 3 (DiD fills the causal gap MMM alone cannot close) |
| [17](./modules/03-causal-structure/days/day-17-instrumental-variables.md) | Instrumental Variables: Solving Price Endogeneity | A valid instrument recovers causal price elasticity even when price is set endogenously | Solves the hardest identification problem in pricing; closes the loop opened on Day 7 | Days 15–16 | Day 7 (re-examine Surf Excel elasticity with IV lens), Day 11 (trade data as instrument) |
| [18](./modules/03-causal-structure/days/day-18-drill-causal-structure.md) | **Drill — Causal Structure** | Six progressive exercises: DAGs, backdoor paths, identification, DiD design, IV design, model diagnosis | Rate-limiting sub-skill Part 1; discomfort is the signal | Days 13–17 | Days 13, 14, 15 |
| [19](./modules/03-causal-structure/days/day-19-rest-synthesize-2.md) | **Rest & Synthesize II** | Consolidation — no new material | Shaky causal foundation produces a confident but wrong pipeline | Days 13–18 | Days 13, 16, 17, 14 (scrambled recall) |
| [20](./modules/04-pipeline-build/days/day-20-bayesian-priors.md) | Bayesian MMM: Priors as Business Knowledge | Priors are where domain knowledge enters the model formally — they prevent absurd posteriors | Conceptual shift from frequentist regression; sets up the Python build | Days 4–5, 14–15 | Day 3 (base/incremental is now a posterior), Day 6 (priors exist for each of the 5Ps) |
| [21](./modules/04-pipeline-build/days/day-21-python-pipeline.md) | Building the Pipeline in Python: PyMC-Marketing | Adstock and Hill compose inside a probabilistic generative model fit with MCMC | First hands-on build day; all prior content feeds into this code | Days 4–5, 20 | Days 4, 5 (transformations appear as Python code) |
| [22](./modules/04-pipeline-build/days/day-22-model-validation.md) | Model Validation: Does Your MMM Actually Work? | Out-of-sample MAPE on holdout + calibration against at least one external causal estimate | No output should be trusted before this; immediately follows the build | Days 16, 21 | Day 16 (DiD as external calibrator), Day 15 (identification limits reappear as validation checks) |
| [23](./modules/04-pipeline-build/days/day-23-competitor-white-space.md) | Competitor Analysis & White Space | Competitor data reveals cross-elasticity; plotting penetration against category demand reveals where to grow | Requires all driver estimates plus market context — now available | Days 2, 7, 9, 22 | Day 9 (Knorr distribution gaps as white space), Day 7 (cross-price elasticity) |
| [24](./modules/04-pipeline-build/days/day-24-drill-decision-translation.md) | **Drill — Decision Translation** | Six exercises converting posteriors to business recommendations with uncertainty in revenue terms | Rate-limiting sub-skill Part 2; the translation bottleneck | Days 20–23 | Days 20, 22 |
| [25](./modules/05-business-decisions/days/day-25-roi-curves-budget.md) | ROI Curves & Budget Optimisation | Marginal ROI equalised across all 5Ps channels = theoretically optimal allocation | First major business output; synthesises the modelling arc | Days 5, 21–22 | Day 5 (saturation curve becomes a decision tool), Day 20 (posterior → revenue distribution) |
| [26](./modules/05-business-decisions/days/day-26-pricing-decisions.md) | Pricing Decisions Under Uncertainty | IV-corrected elasticity posterior + margin data = distribution over profit outcomes at each price point | Primary CMO deck output | Days 7, 17, 25 | Days 7, 17 (elasticity becomes a profit curve with uncertainty bands) |
| [27](./modules/05-business-decisions/days/day-27-promotion-decisions.md) | Promotion Decisions: Trade ROI & the Cannibalization Trap | Most promotions that look profitable are not once cannibalization is modelled | Closes the Promo P from Day 8; borrowed demand becomes a quantified cost | Days 8, 13, 25 | Day 8 (borrowed demand is now a decision risk), Day 13 (promo endogeneity) |
| [28](./modules/05-business-decisions/days/day-28-place-people-decisions.md) | Place & People Decisions: Distribution Expansion & Salesforce | Distribution coefficient posterior + category demand + salesforce coverage = ranked market entry list | Closes both Place and People Ps; same analysis at different scales | Days 9, 11, 16, 23, 25 | Days 9, 11 (coefficients become decisions), Day 16 (DiD design for new geo launch) |
| [29](./modules/05-business-decisions/days/day-29-pack-decisions.md) | Pack Decisions: Portfolio Pruning & Right-Sizing | Pack elasticity + margin + distribution cost = right size for the right market; portfolio complexity has a hidden cost | Closes the Pack P from Day 10; trial-loyalty tradeoff becomes portfolio optimisation | Days 10, 23, 25 | Day 10 (trial-loyalty becomes a portfolio recommendation) |
| [30](./modules/05-business-decisions/days/day-30-capstone.md) | **Capstone**: Full Pipeline + CMO Pricing Deck | Synthesise all 30 days into a validated pipeline and a CMO deck with causal evidence and explicit uncertainty | — | All | All |

---

## Rate-Limiting Sub-Skill

The compound bottleneck for ML practitioners entering MMM is threefold:

1. **Decision-translation under uncertainty** — converting a credible interval into a revenue recommendation without either overstating certainty or hiding behind "it depends"
2. **Causal structure specification** — drawing the right DAG before fitting anything, not after
3. **Surfacing real business drivers** — knowing which P actually drives the outcome in a given category vs. which one looks significant due to confounding

**Drill days targeting this bottleneck:** Day 18 (causal structure), Day 24 (decision translation). Both Ps 2 and 3 also appear in the Day 18 exercises. The capstone (Day 30) is the ultimate test of all three.

---

## Spaced Retrieval Summary

Every concept revisited at approximately +5 and +14 days from introduction:

| Concept | Introduced | First Revisit (~+5) | Second Revisit (~+14) |
|---------|-----------|--------------------|-----------------------|
| MMM limits | Day 1 | Day 6 | Day 15 |
| Base/incremental | Day 3 | Day 8 | Day 17 |
| Adstock | Day 4 | Day 9 | Day 18 (R&S II) |
| Saturation | Day 5 | Day 10 | Day 19 (R&S II) |
| 5Ps Framework | Day 6 | Day 11 | Day 20 |
| Price elasticity | Day 7 | Day 12 (R&S I) | Day 21 |
| Promo mechanics | Day 8 | Day 13 | Day 22 |
| Distribution | Day 9 | Day 14 | Day 23 |
| Pack elasticity | Day 10 | Day 15 | Day 24 |
| People/trade | Day 11 | Day 16 | Day 25 |
| Confounding | Day 13 | Day 18 | Day 27 |
| DAGs | Day 14 | Day 19 (R&S II) | Day 28 |
| Identification | Day 15 | Day 20 | Day 29 |
| DiD | Day 16 | Day 21 | Day 30 |
| IV | Day 17 | Day 22 | Day 26 |
