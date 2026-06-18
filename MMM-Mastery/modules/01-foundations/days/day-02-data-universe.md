# Day 2 — The Data Universe: Nielsen, Kantar, and Internal

> **Today's one idea:** Market data tells you what happened to the category; internal data tells you what you did — they answer different questions and must be joined carefully.
> **Reading time:** ~35 min · **Prereqs:** Day 1
> **Primary source for today:** Charan, A. *The Marketing Analytics Practitioner's Guide* — the data and measurement chapters
> **Before you start:** Recall Day 1's load-bearing idea — in one sentence, without looking: what is the difference between attribution and causation in MMM?

---

## The Hook

You have just been handed a project: build an MMM for Knorr Chicken Stock Cubes in the UK. Your project manager emails you a folder containing:

- A spreadsheet from the finance team: weekly net revenue and volume by SKU
- A Nielsen file: weekly value sales, volume, price per unit, numeric distribution, weighted distribution — by account (Tesco, Asda, Sainsbury's, etc.)
- A Kantar Worldpanel file: quarterly household penetration, repeat purchase rate, and purchase frequency by demographic segment
- A media agency file: weekly GRPs by TV daypart, digital impressions by platform, OOH spend
- A trade file from the sales team: weekly promotional depth and mechanic by account

Five sources. Five different granularities. Five different definitions of "sales." Before you fit a single regression, you need to understand what each source is actually measuring — because joining them carelessly will produce a model that appears to work but is measuring something no one intended.

---

## Building the Intuition

Think of these data sources as three different vantage points on the same market:

```
VANTAGE POINT 1 — The Category View (Nielsen, Kantar)
"What is the market doing, independent of what any one brand does?"
→ Category volume, value, price index, total distribution, competitor actions

VANTAGE POINT 2 — The Brand View (Internal: Finance, Sales, Trade)
"What did Knorr do, and at what cost?"
→ Brand revenue, volume, promotional spend, trade investment, media spend

VANTAGE POINT 3 — The Consumer View (Kantar Worldpanel, Brand Tracker)
"Who is buying, how often, and why?"
→ Penetration, frequency, loyalty, switching, awareness
```

The MMM primarily combines Vantage Points 1 and 2. Vantage Point 3 is used for context, white space analysis (Day 23), and validating that model-estimated demand shifts are consistent with consumer behaviour changes.

### The four questions each source answers

| Source | Granularity | Answers | Does NOT answer |
|--------|-------------|---------|-----------------|
| Nielsen Retail Audit | Weekly, by account/geography | What sold through retail? At what price? How widely distributed? | Why consumers bought; what competitors spent on media |
| Kantar Worldpanel | Weekly/Quarterly, by household panel | Who bought? How often? Switching patterns? | What happened in-store; what brands spent |
| Internal Finance | Weekly, by SKU | Net revenue and volume after trade spend | What the market did; competitor actions |
| Media Agency | Weekly, by channel | Gross/net spend, GRPs, impressions | Whether the media drove sales (that's what the model estimates) |
| Trade/Promo | Weekly, by account | Promotional depth, mechanic, compliance | Consumer response to the promotion |

The most common mistake in MMM data preparation is using internal shipment data as the dependent variable when Nielsen sell-through data is available. Shipments reflect what the brand *shipped to retailers*; sell-through reflects what *consumers bought*. In the short run, these diverge — a promotional stock-up by a retailer inflates shipments without reflecting consumer demand. **Always use sell-through (Nielsen value/volume) as your dependent variable when available.**

### The data join problem

Consider the following scenario. Nielsen reports Knorr stock cubes value sales in "Total Grocery" at £X per week. Your finance team reports net revenue at £Y per week. These will differ because:

1. **Nielsen is sell-through, finance is net revenue after trade spend** — Nielsen captures consumer price × consumer volume; finance captures the brand's realised revenue after trade discounts.
2. **Coverage differs** — Nielsen may cover 85% of the market (major multiples + convenience); internal data covers all channels including foodservice.
3. **Timing lags** — retailer scan data reaches Nielsen with a 1–2 week lag; internal data is real-time shipments.

For MMM purposes:
- Use **Nielsen volume** (not value) as the dependent variable. Volume removes the confounding between price changes and revenue changes — you want to model unit demand, not revenue.
- Use **internal price** data (net selling price to trade) for the price driver, calibrated against Nielsen average selling price.
- Use **Nielsen distribution** (numeric and weighted) as your distribution driver — internal data rarely captures this reliably.

### What Nielsen and Kantar actually measure

**Nielsen Retail Audit** sends auditors into stores (or uses retailer scan data via direct feeds) to record what is on the shelf and what is being sold. The key metrics:

- **Value Sales:** consumer spend (units × shelf price)
- **Volume Sales:** units sold
- **Average Selling Price (ASP):** value ÷ volume — the price consumers actually paid, not the list price
- **Numeric Distribution (ND):** % of stores stocking the SKU
- **Weighted Distribution (WD):** % of category value accounted for by stores stocking the SKU

**Kantar Worldpanel** is a household panel of ~30,000 UK households who scan everything they bring home. This gives:
- **Penetration:** % of households that bought at least once in a period
- **Purchase Frequency:** average number of buy occasions per buying household
- **Spend per buyer:** average value per buying household
- **Switching:** which brands buyers also buy / switched from

A useful identity:
```
Brand Sales = Penetration × Frequency × Spend per occasion × Universe households
```
This identity decomposes brand sales growth into its components — penetration gain vs. frequency gain vs. spend per occasion change. It is a diagnostic tool, not a model input.

---

## The Formal Picture

In the MMM data matrix, each row is a time period $t$ and each column is a variable. For a weekly UK Knorr MMM the matrix looks approximately like:

```
t  | Nielsen_vol | ASP   | ND    | WD    | TV_GRP | Digital | Promo_depth | Season | Trend
---|-------------|-------|-------|-------|--------|---------|-------------|--------|------
1  | 142,300     | 0.89  | 71.2  | 83.4  | 450    | 2.1M    | 0%          | 0.92   | 1
2  | 138,900     | 0.88  | 71.8  | 83.9  | 380    | 1.9M    | 0%          | 0.94   | 2
...
```

Key preparation steps before this matrix can be used in a model:

1. **Align to the same time grain.** Kantar data is quarterly; trade data is weekly. Quarterly data must be interpolated or used at a different level of analysis.
2. **Align geography.** Nielsen "Total UK" ≠ Knorr's actual distribution footprint. Use the Nielsen universe that matches your brand's channel coverage.
3. **Deflate value to volume.** If using value as the dependent variable, include a price index to avoid the model treating price rises as volume gains.
4. **Check for breaks.** Distribution losses, pack size changes, or product reformulations create structural breaks in the time series that must be modelled explicitly (typically as dummy variables).

---

## Where It Breaks / What It Is Not

**"Nielsen data IS the market."** Nielsen covers major grocery retailers well but often underweights discounters (Aldi, Lidl), convenience (forecourt, local shops), and e-commerce. For a brand with significant online or discounter sales, the Nielsen series systematically understates volume and overstates ASP. Know your coverage gap before using it as ground truth.

**"More data sources = better model."** Each additional source adds a potential join error. A clean, well-understood two-source model (Nielsen volume + internal media) typically outperforms a noisy five-source model. Add sources only when they measure a genuine driver you are currently missing.

**"Weekly data is always better than monthly."** Weekly data has more observations but also more noise (day-of-week effects, delivery cycles, scan timing artifacts). For long-horizon TV effects, monthly data often produces more stable adstock estimates. Match grain to the decision cycle.

---

## Try It Yourself

> Close this page now before attempting Exercise 1.

**Exercise 1 — Retrieval.** Without looking: name the three vantage points on market data, one source for each, and one question each answers that the others cannot.

<details>
<summary>Reference answer</summary>

- **Category view (Nielsen):** What did the market sell, at what price, through what distribution?
- **Brand view (Internal finance/trade):** What did Knorr actually earn and spend?
- **Consumer view (Kantar):** Who bought, how often, and are they switching?

Each is blind to what the others see. The model joins them — but the join is where errors enter.
</details>

---

**Exercise 2 — Direct application.** You receive a Knorr dataset where the dependent variable is internal net revenue (£) and the price driver is list price from the finance system. Identify two specific problems this will cause in the model, and propose a fix for each.

<details>
<summary>Reference answer</summary>

**Problem 1:** Net revenue conflates volume changes and price changes. If Knorr raises price and volume drops by less than the price rise, revenue goes up — and the model may attribute this revenue increase to "price driving growth," which is backwards.
*Fix: Use Nielsen volume as the dependent variable.*

**Problem 2:** List price is the price Knorr charges the retailer; consumers pay shelf price, which varies by retailer and is heavily affected by promotions. Using list price instead of Nielsen ASP misrepresents the price signal consumers respond to.
*Fix: Use Nielsen Average Selling Price as the price driver.*
</details>

---

**Exercise 3 — Stretch.** Kantar data shows Knorr's household penetration declining 3 percentage points over two years while the MMM shows stable "base sales." Are these findings consistent? What might explain the apparent contradiction, and what does it imply for the model?

<details>
<summary>Reference answer</summary>

These findings can be consistent if: (1) frequency per buyer increased enough to offset the penetration loss (fewer households buying more often), or (2) the brand gained in higher-value channels (e.g., premium multiples) offsetting lower volume from mass channels.

For the model, declining penetration with stable base suggests the base is fragile — it may be propped up by a shrinking, loyal core rather than broad household reach. This matters because: (a) the base may erode faster going forward; (b) white space analysis (Day 23) should flag the penetration gap as an explicit opportunity.

The implication: add a Kantar penetration series as a covariate or diagnostic, and flag the penetration trend explicitly in the CMO deck.
</details>

---

**Transfer — apply it:**

> Think of a model or analysis you have built in your own work. Write one sentence naming the dependent variable and one sentence naming the biggest "vantage point gap" — what aspect of the truth does your data not capture?

---

## Connect It Back

Yesterday established that MMM produces associations, not causes. Today established that the quality of those associations depends entirely on the integrity of the data join — which source you use for each variable, at what granularity, and what each source actually measures. Tomorrow we use this data to build the first conceptual output: the base vs. incremental decomposition that all business decisions ultimately read from.

**Sharp question to carry forward:** If someone hands you a Knorr MMM with "TV drove 22% of sales" — which dependent variable would change that number most dramatically if you swapped it, and in which direction?

*(Answer: switching from internal net revenue to Nielsen volume would typically reduce the attributed TV share, because revenue-based models absorb price increases as marketing-driven gains.)*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:** Charan, A. *The Marketing Analytics Practitioner's Guide* — the data and measurement chapters. Focus on the discussion of retail audit data and what market share and distribution metrics represent in an FMCG context.

**If you want the deep version:**
- Nielsen IQ Knowledge Centre (Nielsen IQ website) — the methodology section explaining how retail audit data is collected and weighted. Understanding the sampling methodology prevents naive trust in the numbers.
- Gelman, Hill & Vehtari (2021), *Regression and Other Stories*, Chapter 2 (Measurement) — the general principle that measurement error in predictors biases regression coefficients toward zero (attenuation bias), which is the formal reason list price underperforms ASP as a driver.

---

## Navigation

← **Previous:** [Day 1 — What MMM Actually Measures](./day-01-what-mmm-measures.md)
→ **Next:** [Day 3 — Sales Decomposition: Base vs. Incremental](./day-03-base-vs-incremental.md)
