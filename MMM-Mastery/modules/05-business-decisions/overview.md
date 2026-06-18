# Module 5 — Business Decisions

**Days 25–30 · ~3.5 hours**

## What This Module Earns You

By Day 30 you can take any MMM output from Modules 1–4 and convert it into a defensible commercial recommendation. Specifically, you can run each of the five FMCG decision types with causal rigour:

1. **Media budget optimisation** — marginal ROI equalisation across channels using Hill-derived response curves and `scipy.optimize`
2. **Pricing under uncertainty** — a probability distribution over outcomes (not a point estimate) via Monte Carlo over the IV posterior and break-even arithmetic
3. **Promotion net ROI** — gross ROI corrected for post-promo dip and base erosion, yielding the number a finance director will not dispute
4. **Distribution and people decisions** — RoS threshold gating paired with a DiD-validated elasticity, so investment only flows where the retailer hurdle rate clears
5. **Pack and portfolio rationalisation** — net incremental revenue accounting for cannibalization and complexity cost, giving a defensible SKU range position

The capstone (Day 30) assembles all five into a 10-slide CMO deliverable anchored on the Surf Excel South Asia brief.

## The Story of This Module

The first four modules taught you to measure. This module teaches you to decide.

The arc moves through decision types in order of analytical complexity. Budget optimisation (Day 25) is the most mechanical — once you have credible response curves, the algebra of equalising marginal ROIs is straightforward. Pricing (Day 26) raises the stakes: a price recommendation requires causal identification (IV from Module 3), not just a coefficient, and the deliverable must be a distribution, not a point. Promotion (Day 27) is the most commonly mis-analysed decision in FMCG; virtually every gross promotional ROI calculation in practice overstates the true return by ignoring the post-promo dip and base erosion. Place and people decisions (Day 28) are most often skipped by analysts because they require a DiD pilot design to be credible — but that is exactly why they are undervalued when done well. Pack and portfolio (Day 29) is the most cross-functional decision type: it requires coordinating MMM coefficients, finance cost data, and commercial range policy. Day 30 is the capstone — a full-brief exercise where you build the pipeline and produce the deck.

The through-line is a single idea: **every decision type has a correct decision criterion, and that criterion involves a causal effect estimate with uncertainty, not a raw model coefficient read off a table.** Days 25–29 each deliver one such criterion. Day 30 asks you to apply all five simultaneously.

## Days in This Module

| Day | Title | Load-Bearing Idea | Key Technique | Prereqs |
|-----|-------|-------------------|---------------|---------|
| [25](./days/day-25-roi-curves-budget-optimisation.md) | ROI Curves and Budget Optimisation | Marginal ROI equalisation finds the optimal allocation; average ROI misleads because the last pound in any channel returns less than the first | `scipy.optimize`, Hill-derived ROI curves | Days 21–22 |
| [26](./days/day-26-pricing-decisions-under-uncertainty.md) | Pricing Decisions Under Uncertainty | A pricing recommendation needs a probability distribution over outcomes, not a point estimate; the CMO needs P(profitable), not expected volume loss | Monte Carlo over IV posterior, break-even with uncertainty | Days 17, 21–22 |
| [27](./days/day-27-promotion-decisions.md) | Promotion Decisions | Gross promotional ROI almost always overstates true ROI because it ignores the post-promo dip; for storable categories net ROI is often near zero or negative | `net_promo_roi()`, base erosion model | Day 8 |
| [28](./days/day-28-place-and-people-decisions.md) | Place and People Decisions | Distribution investment only creates sustainable value when RoS clears the retailer hurdle rate AND a DiD-validated coefficient exists | RoS threshold screening, DiD pilot design | Days 9, 11, 16 |
| [29](./days/day-29-pack-and-portfolio-decisions.md) | Pack and Portfolio Decisions | A SKU earns its range position only when net incremental revenue (gross gain minus cannibalization minus complexity cost) is positive | `net_incrementality()`, portfolio price architecture audit | Days 10, 23 |
| [30](./days/day-30-capstone.md) | Capstone — Surf Excel South Asia | Build the MMM pipeline end-to-end and produce the 10-slide CMO deck | All techniques | All days |

## Navigation

← Previous module: [04-causal-structure](../04-causal-structure/overview.md)
