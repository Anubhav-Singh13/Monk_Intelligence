# Day 11 — People: Trade Execution, Shelf Compliance, and Consumer Segments (Dove)

> **Today's one idea:** Salesforce effectiveness and shelf compliance are measurable MMM drivers that explain why two identical Price/Promotion/Place/Pack strategies produce different volume outcomes in different accounts — and consumer segmentation reveals why elasticity is not the same everywhere.
> **Reading time:** ~35 min · **Prereqs:** Days 6–10
> **Primary source for today:** Charan, A. *The Marketing Analytics Practitioner's Guide* — the trade execution and salesforce chapters
> **Before you start:** Recall Day 10's load-bearing idea — one sentence: what is the trial-loyalty tradeoff in pack size decisions, and why can't the MMM alone answer which side to optimise for?

---

## The Hook

Dove Body Wash runs the same national promotion in Week 14: 20% off across all major grocery accounts. The brand expects a uniform ~40% uplift based on the price elasticity estimated in the national MMM.

The uplift by account:

| Account | Expected uplift | Actual uplift |
|---------|----------------|--------------|
| Tesco (field sales team, weekly visits) | 40% | 44% |
| Sainsbury's (joint business plan, dedicated resource) | 40% | 47% |
| Asda (call centre account management) | 40% | 23% |
| Morrisons (reduced field coverage) | 40% | 19% |
| Discounters (no dedicated resource) | 40% | 31% |

The promotion was identical. The price was identical. The pack was identical. But execution quality varied enormously by account.

In Tesco and Sainsbury's, the Dove field sales team ensured the promotional gondola ends were built before the promotion started, the shelf was fully stocked at the promotional price on Day 1, and the price was scanned correctly at checkout. In Asda and Morrisons, the promotion launched three days late, two SKUs were out of stock in the first week, and one store still had the old price on the shelf in Week 2.

The People P — specifically, field sales execution quality — generated a 25-percentage-point swing in promotional return between the best and worst accounts. No MMM that omits execution quality can explain this.

---

## Building the Intuition

### The two dimensions of People in FMCG MMM

**Dimension 1 — Trade execution (the supply-side People P):**
The quality of the interface between the brand and the retailer. Measured by:
- **Shelf compliance:** Was the promoted price on the shelf on Day 1? Was the full range stocked? Was the planogram followed?
- **Promotional compliance:** Did the retailer display the gondola end? Was the feature ad placed correctly?
- **Call frequency:** How often does the field sales team visit each account? Higher call frequency → better compliance on average.
- **Joint business planning (JBP) quality:** Do key accounts have a dedicated Unilever resource who proactively troubleshoots execution issues?

**Dimension 2 — Consumer segmentation (the demand-side People P):**
The heterogeneity of consumer response across geographies, demographics, or life stages. Dove Body Wash has different price elasticity for:
- Young urban professionals (less price-sensitive; high brand affinity)
- Young families (moderately price-sensitive; value-for-size important)
- Older consumers (loyal to existing brand; low elasticity)
- Switchers (high price sensitivity; promotional response dominates)

A national MMM produces a *weighted average* of these segment responses. If segment composition differs by geography (urban vs. rural, North vs. South), the national elasticity coefficient is correct "on average" but wrong everywhere specifically.

### Why People creates omitted variable bias (recall Day 6)

In the Day 6 framework, we identified People as the P most likely to be omitted from the model — because execution data is hard to collect systematically. When People is omitted:

1. The Place coefficient (distribution) will be inflated — because distribution gains achieved by a strong field sales team will appear to be explained by distribution alone, when the execution quality that makes distribution valuable is unmeasured.

2. The Promotion coefficient will be noisy — because identical promotions with different compliance scores will show different volume responses, which appears as unexplained variance rather than a systematic execution effect.

3. Geographic heterogeneity in the residuals will be unexplained — which means the model's predictions will be systematically worse in low-execution geographies.

### Constructing a proxy for execution quality

Since direct compliance data is often unavailable, practitioners use proxies:

**Proxy 1 — The WD/ND ratio** (from Day 9):
```
WD/ND ratio = Weighted Distribution / Numeric Distribution
```
High ratio → brand is in large, well-managed stores with good category presence.
Low ratio → brand is in small or poorly-managed stores.

**Proxy 2 — Velocity index:**
```
Velocity = Brand sales per stocking store = (Brand volume) / (Stores stocking × average store size)
```
Low velocity relative to category benchmark → possible execution issue (poor shelf position, out-of-stocks, wrong price).

**Proxy 3 — Call frequency data from CRM:**
If the brand has a field force CRM (e.g., Salesforce, Repsly), weekly call frequency by account can be extracted and used directly as a model variable.

```python
# Construct a simple execution quality index
df['execution_index'] = (
    0.4 * df['wd_nd_ratio'].rank(pct=True) +
    0.4 * df['velocity_index'].rank(pct=True) +
    0.2 * df['call_frequency'].rank(pct=True)
)
# Scale to 0–100
df['execution_index'] = df['execution_index'] * 100
```

This composite index can enter the model as a continuous variable. Its coefficient will absorb the execution quality effect that Place and Promotion variables cannot explain.

---

## The Formal Picture

### People in the MMM equation

The execution quality index enters the model as an interaction term with promotional activity — because execution matters most during campaigns:

```math
\text{Sales}_t = \ldots + \beta_{\text{Promo}} \cdot d_t + \beta_{\text{Compliance}} \cdot c_t + \beta_{\text{Promo} \times c} \cdot (d_t \cdot c_t) + \ldots
```

where:
- $d_t$ = promotional flag (0/1)
- $c_t$ = execution/compliance index (0–100)
- $\beta_{\text{Promo} \times c}$ = interaction term: how much each compliance point amplifies the promotional response

A significant positive $\beta_{\text{Promo} \times c}$ says: "Better execution makes promotions more effective." This coefficient directly quantifies the value of salesforce investment.

### Consumer segmentation in MMM: the heterogeneous elasticity problem

The national price elasticity $\hat{\eta} = -1.8$ for Dove is a weighted average. If the brand is unevenly distributed across consumer segments:

| Segment | Share of volume | Segment elasticity |
|---------|----------------|-------------------|
| Urban premium (Dove loyalists) | 45% | −0.8 |
| Suburban families | 35% | −2.1 |
| Price-driven switchers | 20% | −3.8 |

Weighted average: $0.45 \times (-0.8) + 0.35 \times (-2.1) + 0.20 \times (-3.8) = -1.85$

This matches the national MMM estimate. But:
- A price increase targeted at Urban Premium consumers will see −0.8 elasticity (safe)
- The same price increase affects Suburban Families at −2.1 (risky)
- Applied to the full brand, the −1.85 average masks both the safe opportunity and the dangerous exposure

**Implication for the CMO deck (Day 29):** Any pricing recommendation must state which consumer segment it primarily affects, not just the brand-average elasticity. This requires Kantar segmentation data overlaid on the MMM.

### What People tells you that other Ps don't

| Business question | Which P answers it |
|------------------|-------------------|
| What would happen if we raised price 5%? | Price |
| Which promotion mechanics drive the most real uplift? | Promotion |
| Which geographies have unmet demand? | Place |
| Which pack sizes are profitable to maintain? | Pack |
| Why does the same promotion perform differently in different accounts? | **People** |
| Why does the price elasticity vary by geography? | **People** (consumer segmentation) |
| Why is distribution growth not translating to sales in some regions? | **People** (execution quality) |

The People P is the *diagnostic* layer — it explains why the other Ps are performing differently from their average estimates. It is not a lever the brand manager can pull directly (unlike price or media spend), but it is a lever the sales director can pull through salesforce deployment, call frequency decisions, and JBP investment.

---

## Where It Breaks / What It Is Not

**"People = headcount."** People in this framework is not about how many sales reps there are — it is about the *quality and focus* of their work. A sales team making 1,000 unfocused store visits per week delivers less value than one making 400 targeted visits to high-execution-gap accounts.

**"Execution quality is too hard to measure — omit it."** The cost of omitting it is bias in every other coefficient that correlates with execution. If your best-executed accounts also have the highest promotional compliance and highest distribution, omitting execution quality inflates the Place and Promotion coefficients for those accounts. The recommendation: build the best proxy you can, even if it is imperfect.

**"Consumer segmentation means different models for each segment."** Not necessarily — it means testing whether model coefficients are heterogeneous across segments. Start with a single national model. If segment-level residuals are systematically signed (positive residuals in urban premium, negative in suburban families), consider a segmented model or interaction terms. Don't add complexity before evidence demands it.

**The measurement ceiling.** For the People P, you will almost always have imperfect data. This is not a modelling failure — it is an honest limitation to state in the CMO deck. "We believe execution quality explains 10–15% of the unexplained variance in this model, but we could not measure it directly. Improving execution data collection would reduce the model's confidence intervals."

---

## Try It Yourself

> Close the page now before attempting Exercise 1.

**Exercise 1 — Retrieval.** Without looking: name the two dimensions of the People P. For each, give one example of a variable you could include in the MMM, and one example of a business decision it would inform.

<details>
<summary>Reference answer</summary>

**Dimension 1 — Trade execution:**
- Variable: shelf compliance score or call frequency
- Decision: where to deploy additional field sales resource (accounts with low compliance → high opportunity)

**Dimension 2 — Consumer segmentation:**
- Variable: WD/ND ratio as proxy for consumer quality, or Kantar segment share by geography
- Decision: which geographies can absorb a price increase without significant volume loss (based on segment composition)
</details>

---

**Exercise 2 — Direct application.** Dove Body Wash runs a 20% off promotion nationally. The execution index (0–100) has the following values by region:
- London: 78 | South East: 71 | Midlands: 52 | North: 41

The MMM interaction coefficient is $\hat{\beta}_{\text{Promo} \times c} = 0.004$ (per compliance point per promo week).

The base promotional uplift ($\hat{\beta}_{\text{Promo}} = 0.38$).

Calculate the expected total promotional uplift in each region.

<details>
<summary>Reference answer</summary>

Total uplift = $\hat{\beta}_{\text{Promo}} + \hat{\beta}_{\text{Promo} \times c} \cdot c$

- London: 0.38 + 0.004 × 78 = 0.38 + 0.312 = **69.2% uplift**
- South East: 0.38 + 0.004 × 71 = 0.38 + 0.284 = **66.4% uplift**
- Midlands: 0.38 + 0.004 × 52 = 0.38 + 0.208 = **58.8% uplift**
- North: 0.38 + 0.004 × 41 = 0.38 + 0.164 = **54.4% uplift**

The execution gap between London (78) and North (41) accounts for a 14.8 percentage point difference in promotional return — before any spending change. The implication: improving Northern execution to the London standard would add ~14.8% volume uplift on every promoted week, at zero additional promotional cost.
</details>

---

**Exercise 3 — Stretch (callback to Day 6).** The Day 6 framework identified People as the P most likely to create omitted variable bias in the other coefficients. Using the Dove example from today, explain specifically *which* coefficient would be most biased if execution quality were omitted — and in which direction.

<details>
<summary>Reference answer</summary>

The Promotion coefficient ($\hat{\beta}_{\text{Promo}}$) would be most biased if execution quality is omitted.

High execution accounts (London, South East) both run promotions more effectively *and* happen to have higher execution quality. When the model observes "promotion ran + high volume uplift" in these accounts, it attributes the full uplift to the promotion flag. But some of the uplift was due to execution quality, not the promotion itself.

Direction of bias: **upward** — the promotion coefficient is inflated because it absorbs the execution quality effect. The brand then over-invests in promotions expecting national uplift of 69% (London level) when the true average (accounting for poor North execution) is closer to 62%.

The secondary bias: the Place coefficient will also be slightly upward-biased, because high-execution accounts also tend to have better distribution quality (WD/ND ratio higher).
</details>

---

**Transfer — apply it:**

> In your domain, what is the "execution quality" variable — the factor that determines whether identical strategies produce different results in different contexts? Is it currently in your model? If not, what proxy could you construct from data you already have?

---

## Connect It Back

Module 2 is complete. You have walked all five Ps: Price (elasticity, entanglement), Promotion (mechanics, borrowed demand), Place (distribution, carryover), Pack (price-per-use, trial vs. loyalty), People (execution, segmentation). Tomorrow is Rest & Synthesize I — your job is to hold all five in your head simultaneously, because Module 3 will examine why each of them is harder to estimate than it appears.

**Sharp question to carry forward into Module 3:** Which of the 5 Ps has the *highest* endogeneity risk — that is, the highest probability that the error term in the regression correlates with the variable's value? And which direction does this bias the coefficient?

*(Price: positive endogeneity (set high when demand is high), biasing the coefficient toward zero or positive. Day 13 makes this precise.)*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:** Charan, A. *The Marketing Analytics Practitioner's Guide* — the salesforce effectiveness and trade investment chapters. Charan's framing of the "return on sales investment" parallels today's execution quality model.

**If you want the deep version:**
- Angrist & Pischke (2009), Chapter 3, Section 3.1 — the omitted variable bias formula. Today's analysis of how omitting execution quality inflates the Promotion coefficient is a direct application of this formula. Work through the algebra for the two-variable case.

---

## Navigation

← **Previous:** [Day 10 — Pack: Size Elasticity & the Trial-Loyalty Tradeoff](./day-10-pack-size-elasticity.md)
→ **Next:** [Day 12 — Rest & Synthesize I](./day-12-rest-synthesize-1.md)
