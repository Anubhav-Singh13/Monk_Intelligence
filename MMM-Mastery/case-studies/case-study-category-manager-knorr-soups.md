# Case Study: Category Manager — Knorr Soups UK (Tesco Category Review)

> **Role lens:** This case study is written from the perspective of the Unilever Category Manager responsible for the Total Soups category at Tesco — not the Knorr brand manager. The P&L here is total category value at Tesco. Every analytical choice flows from that difference.
>
> **Estimated working time:** ~90 min end-to-end (read + exercises)
> **MMM days exercised:** Days 7, 8, 9, 10, 11, 13, 14, 15, 16, 20, 23, 24, 28

---

## Preamble: The Category Manager Role

The Knorr brand manager optimises Knorr volume and margin. The Category Manager at Unilever optimises the *total soups category at Tesco*. That includes Heinz, Batchelors, Campbell's, private label, and premium chilled brands — not just Knorr.

This distinction is not administrative. It changes every analytical question:

| Question | Brand Manager asks | Category Manager asks |
|---|---|---|
| Is the promo working? | Did Knorr volume lift? | Did total soups volume lift, or did Knorr simply pull from Heinz? |
| Should we cut range? | Which Knorr SKUs have low RoS? | Which SKUs in the full 280 bring new shoppers to the category? |
| What is the price elasticity? | Knorr own-price elasticity | Category demand elasticity + cross-elasticities between segments |
| What does Tesco want? | Knorr share of soups | Sales per linear metre across all soups |

The Category Manager's commercial leverage with Tesco is this: Tesco allocates shelf space to categories based on space productivity (£ per linear metre per week) and category growth trajectory. A supplier who can demonstrate *category growth* — not just brand growth — gets more space, more promotional slots, and more influence over the range architecture. Knorr's defence in a range review is therefore not "Knorr is big" but "Knorr grows the category."

---

## The Situation

Tesco is conducting a category review. Headline facts:

- Total soups (ambient + chilled) has been value-flat for 18 months at Tesco: ~£480M annualised
- Tesco category buyer is considering cutting range from 280 to 220 SKUs (21% reduction) to improve gondola-end productivity
- Space pressure from adjacent categories (meal kits, deli, ready meals) which are growing 8–12% YoY
- Knorr holds 34% value share of ambient soups at Tesco; private label holds 28%; Heinz 22%
- The Unilever Category Manager must produce a Category Growth Plan for the Tesco Joint Business Plan (JBP) review in 6 weeks

The Category Manager must answer three commercial questions:

1. **Why is the category flat?** (diagnostic)
2. **What range architecture unlocks growth?** (structural)
3. **What is the mutual commercial commitment Tesco should accept?** (JBP)

---

## Data Available

| Source | What it contains | Cadence |
|---|---|---|
| Nielsen Category Scan | Total soups weekly value + volume by segment, by brand, by SKU; ND, WD, ASP, RoS | Weekly |
| Nielsen Brand Scan | Knorr, Heinz, Batchelors, Campbell's, PL — volume, ASP, ND, WD, RoS | Weekly |
| Kantar Shopper Panel | Category penetration, frequency, spend per buyer by retailer, by Mosaic geodemographic cluster | 12-weekly rolling |
| Internal | Knorr planogram compliance scores, distribution by SKU by account, trade promotional calendar | Monthly |
| Macro | UK CPI food, consumer confidence index, YouGov sentiment | Monthly |

---

## Step 1: Category Health Diagnostic

### The diagnostic question

Before building a growth plan, the Category Manager must establish whether Tesco's flat performance is a *Tesco problem* (execution, range, pricing vs competitors) or a *category problem* (UK households are lapsing from soup as an occasion).

The three headline metrics from Kantar are:

- **Penetration:** % of UK households buying soup in the last 12 weeks
- **Frequency:** average purchase occasions per buyer per quarter
- **Spend per occasion:** average basket ring on a soup purchase trip

A flat category value can decompose as:

```
Value = Penetration × Frequency × Spend per occasion × Household universe
```

If penetration is falling but frequency is rising, the category is polarising toward a smaller, more committed core — a structural risk. If frequency is flat but spend per occasion is rising (premiumisation), the opportunity is to convert more households at the lower price tier.

### Python: Category health scorecard

```python
import pandas as pd
import numpy as np

def category_health_scorecard(
    nielsen_weekly: pd.DataFrame,   # cols: week, segment, brand, value, volume, ND, WD, RoS
    kantar_quarterly: pd.DataFrame, # cols: quarter, retailer, penetration_pct, freq_per_buyer, spend_per_occasion
    retailer: str = "Tesco",
    rolling_weeks: int = 52,
) -> pd.DataFrame:
    """
    Returns a one-row-per-metric scorecard comparing latest period vs year-ago.
    YoY change signals whether each driver is contributing to or dragging on category value.
    """
    # --- Nielsen: total category, latest 52w vs prior 52w ---
    cat = nielsen_weekly.copy()
    cat["week"] = pd.to_datetime(cat["week"])
    latest_end = cat["week"].max()
    latest_start = latest_end - pd.Timedelta(weeks=rolling_weeks)
    prior_end = latest_start - pd.Timedelta(weeks=1)
    prior_start = prior_end - pd.Timedelta(weeks=rolling_weeks)

    def period_sum(df, start, end):
        mask = (df["week"] >= start) & (df["week"] <= end)
        return df.loc[mask, ["value", "volume"]].sum()

    l = period_sum(cat, latest_start, latest_end)
    p = period_sum(cat, prior_start, prior_end)

    nielsen_metrics = {
        "total_value_latest_52w": l["value"],
        "total_value_prior_52w": p["value"],
        "value_yoy_pct": (l["value"] / p["value"] - 1) * 100,
        "total_volume_latest_52w": l["volume"],
        "volume_yoy_pct": (l["volume"] / p["volume"] - 1) * 100,
        "implied_ASP_latest": l["value"] / l["volume"],
        "implied_ASP_prior": p["value"] / p["volume"],
        "ASP_yoy_pct": (l["value"] / l["volume"]) / (p["value"] / p["volume"]) * 100 - 100,
    }

    # --- Kantar: penetration / frequency / spend trend at target retailer ---
    k = kantar_quarterly[kantar_quarterly["retailer"] == retailer].sort_values("quarter")
    latest_q = k.iloc[-1]
    prior_q = k.iloc[-2]  # one quarter back; use [-5] for YoY

    kantar_metrics = {
        "penetration_latest_pct": latest_q["penetration_pct"],
        "penetration_prior_pct": prior_q["penetration_pct"],
        "penetration_chg_pp": latest_q["penetration_pct"] - prior_q["penetration_pct"],
        "frequency_latest": latest_q["freq_per_buyer"],
        "frequency_prior": prior_q["freq_per_buyer"],
        "frequency_chg_pct": (latest_q["freq_per_buyer"] / prior_q["freq_per_buyer"] - 1) * 100,
        "spend_per_occasion_latest": latest_q["spend_per_occasion"],
        "spend_per_occasion_prior": prior_q["spend_per_occasion"],
        "spend_yoy_pct": (latest_q["spend_per_occasion"] / prior_q["spend_per_occasion"] - 1) * 100,
    }

    # --- CDI: Tesco over/under-index vs total UK category ---
    # CDI = (Tesco soups value share of total UK Tesco sales)
    #       / (Tesco total food value share of total UK food sales) * 100
    # Values > 100 mean Tesco over-indexes in soups; < 100 = under-indexed
    # Pass CDI separately as a scalar if available
    # Placeholder:
    kantar_metrics["tesco_CDI_note"] = "Requires Kantar total-market vs Tesco retailer split"

    scorecard = pd.DataFrame(
        list(nielsen_metrics.items()) + list(kantar_metrics.items()),
        columns=["metric", "value"]
    )
    return scorecard
```

### Reading the scorecard: decision tree

```
Category value flat YoY
│
├── Penetration FALLING, frequency STABLE → Lapsed buyers problem
│   └── Action: Recruit via price, pack size, or occasion messaging
│
├── Penetration STABLE, frequency FALLING → Loyalty/habit erosion
│   └── Action: Execution, range breadth at mid-tier, subscription/meal-plan adjacent
│
├── Both FALLING → Structural category decline
│   └── Action: Occasion redefinition, NPD in growing segments (chilled, premium)
│
└── Both STABLE, value flat → Pure ASP decline (deflation or mix shift to PL)
    └── Action: Premiumisation, pack architecture, reduce reliance on deep discount
```

**In this case:** Kantar shows penetration -1.8 pp YoY (41.2% → 39.4%) with frequency stable at 4.1 occasions/quarter. The category is losing occasional buyers — people who bought soup 1–3 times in winter but have not returned. This is a *recruitment and re-engagement* problem, not a frequency problem among committed buyers.

**Tesco-specific CDI:** Tesco CDI for soups = 94 (under-indexed). Tesco shoppers buy proportionally less soup than their share of total grocery would predict. Asda CDI = 108, Sainsbury's CDI = 102. Conclusion: Tesco has *more to gain* from category growth than the average UK retailer — this is the hook for the JBP commercial conversation.

---

## Step 2: Segment and Occasion Analysis

### Segment growth matrix

| Segment | 52w value (£M) | YoY growth | Tesco value share | Tesco CDI vs UK | Opportunity size |
|---|---|---|---|---|---|
| Ambient convenience (Knorr, Heinz, Batchelors) | 318 | -1.2% | 34% | 91 | Medium: defend + execute |
| Ambient premium (Covent Garden ambient, branded stocks) | 48 | +3.8% | 29% | 85 | High: distribution gap |
| Chilled premium (Covent Garden, The Savse, fresh) | 72 | +7.4% | 31% | 97 | High: space constrained |
| Cooking-adjacent (stock pots, bouillon) | 31 | +1.1% | 36% | 103 | Low: mature |
| Private label across all | 89 | +2.1% | 38% | 106 | Cannibalisation risk |

**The structural insight:** ambient convenience — the largest segment — is declining. Chilled premium is growing at 7.4% YoY but is space-constrained at Tesco (Tesco CDI 97, i.e. broadly in line with market, but physical facings are insufficient to meet that demand). The growth opportunity is not in fighting for ambient share but in *migrating the category mix* toward chilled/premium while defending the ambient base.

### Occasion analysis (Kantar usage data)

Kantar usage data (when available through Kantar's Usage module or Worldpanel Plus) allows segmentation by consumption occasion. Proxy from purchase timing and basket complementarity:

- **Winter weekday lunch (solo):** largest occasion, ambient dominant, declining -2.1% penetration YoY. Over-indexed in Mosaic groups E (Urban Professionals), F (Suburban Semis). Key driver of current decline.
- **Weekend family meal starter:** stable, chilled over-indexed, higher ASP. Penetration 12% of soup buyers.
- **Summer cold occasion (gazpacho, chilled):** small but growing +18% from low base. Tesco under-ranged here.
- **Evening comfort (ambient premium):** growing, under-served by current Tesco range.

**Underdeveloped occasion with highest potential:** Evening comfort / ambient premium. Current penetration 8% of soup buyers at Tesco vs 14% at Waitrose (the benchmark). Gap of 6 pp × 4.1M Tesco soup households × £2.80 ASP = ~£7M incremental opportunity. This is a *distribution and ranging* problem — the SKUs exist, Tesco doesn't stock them sufficiently.

---

## Step 3: Brand Contribution to Category Health

### What "Category CDI at brand level" means

A brand's category CDI answers: *does this brand recruit shoppers who would not otherwise be in the soups category, or does it only attract existing category shoppers from competitor brands?*

Measurement approach using Kantar panel:
- Track the purchase sequence of households who *first* buy Brand X in a quarter
- Of those first-buy households, what share had *zero* soups purchases in the prior quarter?
- That share is the "new-to-category" rate for Brand X

A brand with a high new-to-category rate is a *category builder*. A brand where almost all purchasers were already buying a competitor brand is a *share stealer* — valuable for the brand P&L but zero-sum for the category and therefore less valuable to Tesco.

### Category contribution matrix

| Brand | Vol share (Tesco) | New-to-cat % | RoS vs segment benchmark | PPU tier | Category CDI | Assessment |
|---|---|---|---|---|---|---|
| Knorr ambient | 34% | 22% | +8% vs benchmark | Mid (£1.20–£1.80/serve) | 108 | Category builder — defend |
| Heinz | 22% | 11% | -3% vs benchmark | Mid-low (£0.90–£1.40/serve) | 96 | Mostly internal switching |
| Batchelors | 9% | 8% | -12% vs benchmark | Value (£0.70–£1.00/serve) | 88 | Share stealer from Heinz/PL |
| Campbell's | 5% | 31% | +14% vs benchmark | Premium-mid (£1.60–£2.20/serve) | 124 | Strong category builder — *protect* |
| Covent Garden chilled | 6% | 38% | +22% vs benchmark | Premium (£2.40–£3.50/serve) | 141 | Highest category CDI — under-ranged |
| Private label | 24% | 6% | -8% vs benchmark | Value (£0.60–£0.90/serve) | 82 | Pure share stealer, no recruitment |

**Key insight for the JBP:** Covent Garden chilled has the highest category CDI (141) — every 100 buyers it recruits, 38 are new to the total soups category. Private label CDI is 82 — it recruits almost no new category buyers and cannibalises branded recruitment. The range rationalisation should protect high-CDI brands disproportionately and consolidate private label SKUs where duplication is highest.

This is the counter-argument to Tesco's instinct to grow private label share: private label *takes value from the category* without growing it. A 1% shift from branded to private label reduces total category value by ~15p/transaction (ASP gap) with no compensating volume from new buyers.

---

## Step 4: Assortment Rationalisation Proposal

### The incrementality framework (from Day 10)

An SKU is *incremental* to the category if removing it causes some buyers to either:
(a) substitute within-category to another SKU (low incrementality — the volume stays in the category), or
(b) leave the category entirely on that trip or more permanently (high incrementality — the volume is lost).

Empirically, incrementality is estimated by:

```
Incremental volume of SKU_i = total volume_i × (1 - cross-purchase overlap with nearest sibling SKU)
```

If SKU A and SKU B have 80% buyer overlap (80% of people who buy A also buy B in the same quarter), delisting A causes ~80% of A's volume to transfer to B. Only 20% is at risk of category exit. SKU A has low incrementality.

If SKU C has 15% overlap with any other SKU — it serves a distinct shopper — delisting C puts 85% of C's volume at risk. SKU C has high incrementality.

### Python: Assortment incrementality scoring

```python
def score_sku_incrementality(
    purchase_panel: pd.DataFrame,  # cols: household_id, sku_id, quarter, volume
    sku_meta: pd.DataFrame,        # cols: sku_id, brand, segment, ppu_tier, ros, ND
    ros_hurdle: float = 3.0,       # units per point of distribution per week
    ppu_gap_threshold: float = 0.08,  # delist if within 8% PPU of a better-supported sibling
) -> pd.DataFrame:
    """
    Score each SKU on four incrementality criteria.
    Returns delist_flag, protect_flag, and composite score per SKU.
    """
    results = []

    for sku in sku_meta["sku_id"].unique():
        meta = sku_meta[sku_meta["sku_id"] == sku].iloc[0]

        # Criterion 1: RoS vs hurdle
        ros_flag = meta["ros"] < ros_hurdle  # True = candidate for delist

        # Criterion 2: Buyer overlap with nearest sibling in same segment + PPU tier
        buyers_sku = set(
            purchase_panel[purchase_panel["sku_id"] == sku]["household_id"]
        )
        same_segment = sku_meta[
            (sku_meta["segment"] == meta["segment"]) &
            (sku_meta["ppu_tier"] == meta["ppu_tier"]) &
            (sku_meta["sku_id"] != sku)
        ]["sku_id"].tolist()

        max_overlap = 0.0
        for sibling in same_segment:
            buyers_sibling = set(
                purchase_panel[purchase_panel["sku_id"] == sibling]["household_id"]
            )
            if len(buyers_sku) > 0:
                overlap = len(buyers_sku & buyers_sibling) / len(buyers_sku)
                max_overlap = max(max_overlap, overlap)

        high_overlap_flag = max_overlap > 0.70  # high duplication with a sibling

        # Criterion 3: PPU gap to best-supported sibling
        if same_segment:
            sibling_meta = sku_meta[sku_meta["sku_id"].isin(same_segment)]
            # "best-supported" = highest ND
            best_sibling_ppu = sibling_meta.loc[sibling_meta["ND"].idxmax(), "ppu"]
            ppu_gap = abs(meta["ppu"] - best_sibling_ppu) / best_sibling_ppu
            ppu_gap_flag = ppu_gap < ppu_gap_threshold
        else:
            ppu_gap_flag = False

        # Composite delist score (higher = stronger delist case)
        delist_score = sum([ros_flag, high_overlap_flag, ppu_gap_flag])

        results.append({
            "sku_id": sku,
            "brand": meta["brand"],
            "segment": meta["segment"],
            "ros": meta["ros"],
            "ros_below_hurdle": ros_flag,
            "max_buyer_overlap": round(max_overlap, 2),
            "high_duplication": high_overlap_flag,
            "ppu_gap_flag": ppu_gap_flag,
            "delist_score": delist_score,
            "delist_flag": delist_score >= 2,   # delist if fails 2+ criteria
        })

    return pd.DataFrame(results).sort_values("delist_score", ascending=False)
```

### Protect criteria (override delist score)

Even if a SKU scores 2+ on the delist criteria, protect it if:

1. New-to-category rate > 25% (from Kantar panel — this SKU is recruiting new buyers)
2. Unique occasion served with no close substitute in the range (e.g. the only chilled premium in that flavour tier)
3. Planogram compliance > 85% (well-executed SKU; poor RoS may be a distribution problem, not a demand problem)
4. Category CDI contribution: the SKU is disproportionately purchased by the Mosaic segments with the lowest current soup penetration (growth potential, not just current rate)

### Proposed range architecture: 220 SKUs

The 220-SKU range should be structured as a grid:

```
                  VALUE          MID           PREMIUM-MID     PREMIUM
                  (< £1.00/s)   (£1.00-1.80)  (£1.80-2.40)    (> £2.40)
---------------------------------------------------------------------------
AMBIENT CONV.     PL (12 SKUs)  Knorr (28)    Knorr+ (8)      -
                                Heinz (14)
                                Batchelors(8)

AMBIENT PREMIUM   -             -             Campbell's (6)  Cov.Gdn amb (4)

CHILLED           -             -             -               Cov.Gdn ch (18)
                                                              Savse (6)
                                                              Other (4)

STOCK/BOUILLON    PL (6)        Knorr (10)    Marigold (4)    Organic (3)

COOKING ADJ.      PL (4)        Knorr (6)     Knorr (4)       Premium (3)
---------------------------------------------------------------------------
TOTAL:            22            66            22              38  = 148 branded
                                                              + 22 PL anchor = 170
                  + 50 long-tail ambient including regional and seasonal = 220
```

Versus current 280: the 60 delisted SKUs are predominantly:
- 24 ambient convenience SKUs with RoS below 2.1 (Tesco hurdle) and >70% overlap with a Knorr/Heinz sibling
- 18 private label SKUs with high duplication within the PL range
- 12 ambient mid-tier with PPU within 5% of a better-distributed neighbour
- 6 discontinued NPD that Tesco has not yet formally delisted

---

## Step 5: Category Growth Plan for the Tesco JBP

### Headline ambition

Total soups category at Tesco: +6% value in 12 months (+£28.8M on a £480M base).

This requires recovering the 1.8 pp penetration loss (+~750K buying households at Tesco) and growing average transaction value +2% through mix shift toward premium.

### Lever 1: Convert lapsed buyers

**Context:** Kantar identifies 3.2M UK households who bought soup at Tesco in the prior 12 months but not in the most recent 12-week period. These are *lapsed occasional buyers*, not category exits — they still have soup equity, they have just not been triggered.

**MMM evidence:** From the Knorr MMM (Days 7–9), the price elasticity of ambient convenience soup is approximately -1.4 in the value-seeking consumer segment (Mosaic groups C, D). At current pricing (~£1.50/serve average), a 15% price promotion generates +21% volume uplift (= -1.4 × -15%). However, the MMM also shows that **promotional frequency above 4 events per quarter generates diminishing returns** — the 5th and 6th promotional events in a quarter produce only 40% of the uplift of the first two (saturation in the Hill function: alpha ≈ 0.6, K = 3 events/quarter).

Recommendation: 2 targeted promotional events per quarter for lapsed-buyer segments (not blanket price reduction), supplemented by digital personalised offers via Tesco Clubcard to lapsed-buyer households identified in Kantar panel. Target: recover 400K lapsed households in 12 months.

**Volume estimate:**
```
Lapsed households targeted: 3.2M × 25% reach (realistic for Clubcard targeted) = 800K
Conversion rate from targeted offer: 50% (conservative benchmark from Tesco Clubcard trials)
Recovered buyers: 400K
Average annual spend per recovered buyer: £18.40 (from Kantar spend-per-buyer)
Incremental value: 400K × £18.40 × Tesco share = 400K × £18.40 × 0.34 = £2.5M at Tesco
```

### Lever 2: Develop the premium chilled occasion

**Context:** Chilled premium soups CDI at Tesco = 97 (broadly in line with market) but only 14% category penetration vs 41% for ambient. This is not a demand problem — the CDI tells us Tesco shoppers buy chilled at the same rate as the average UK shopper. It is a *supply/distribution* problem: chilled soups are under-ranged (18 SKUs proposed vs current 12) and under-spaced (currently allocated 1.2 linear metres of chilled bay vs Sainsbury's 1.8m equivalent).

**MMM/distribution evidence:** From Day 9 (distribution elasticity), the distribution elasticity formula:

```
distribution_elasticity = beta_WD × WD_mean / y_mean
```

Using the Covent Garden chilled MMM (hypothetical but realistic calibration):
- beta_WD = 0.85 (log-log model coefficient on weighted distribution)
- WD_mean = 42% (current weighted distribution at Tesco — many stores not ranging the full chilled line)
- y_mean = £6.2M current chilled soups value at Tesco

Distribution elasticity = 0.85 × 42/100 = 0.357

A 20 pp increase in WD (from 42% to 62%, achievable by listing the 6 additional SKUs and improving planogram compliance) implies:

```
Volume uplift = distribution_elasticity × (delta_WD / WD_mean)
             = 0.357 × (20/42)
             = +17.0%
```

Incremental chilled value: £6.2M × 17% = +£1.1M at Tesco.

### Lever 3: Execution quality investment (People/RoS)

**Context:** Day 11 (People — field sales and execution) and Day 28 (execution quality modelling) show that the gap between top-quartile and bottom-quartile planogram compliance accounts for an estimated 18% RoS difference across the Tesco estate. Knorr has a planogram compliance score of 71% (weighted across all Tesco stores). The top-quartile Tesco stores score 89%.

Bringing all stores to the 75th percentile compliance (81%) would close approximately half the gap.

**Volume estimate:**
```
Ambient Knorr value at Tesco: £163M (34% of £480M total)
Compliance improvement effect (half the 18% gap = +9% RoS, assuming 12% of stores move):
Incremental value = £163M × 9% × 12% stores impacted = £1.8M
```

This requires deploying field sales resource (2 additional field reps covering 180 under-performing stores) and a digital shelf audit tool fed by Tesco store compliance data via the JBP data-sharing agreement.

### Summary of levers

| Lever | MMM mechanism | £M incremental (12 months) | Key assumption |
|---|---|---|---|
| Lapsed buyer conversion | Price/promo elasticity + Clubcard targeting | £2.5M | 50% conversion on targeted offer |
| Premium chilled distribution | Distribution elasticity (+20pp WD) | £1.1M | Tesco agrees to 6 additional chilled SKUs + space |
| Execution quality uplift | RoS gap closure via compliance | £1.8M | Compliance moves from 71% to 81% in 12% of stores |
| Mix shift to premium (passive) | ASP uplift as range architecture improves | £1.4M | No additional activation required |
| **Total** | | **£6.8M** | **Exceeds £6% target (£28.8M × Unilever share ≈ £5.5M) |

Note: the total category target is £28.8M; Unilever brands represent ~45% of category value, so Unilever's contribution to the growth plan needs to cover approximately £13M. The remaining £15M+ comes from competitor growth (especially chilled premium) and private label mix management — the Category Manager advocates for these in the JBP even though they are not Unilever brands.

---

## Step 6: The Tesco JBP Presentation Structure

### Page 1: Category opportunity

**Title:** "£28M of category value Tesco is currently leaving on the table"

Content:
- Tesco CDI = 94 vs Sainsbury's 102, Asda 108. Tesco under-indexes in soups relative to its share of total grocery.
- Penetration has declined 1.8 pp in 12 months. Each percentage point of penetration = ~£5.3M category value.
- Chilled premium is growing at 7.4% nationally; Tesco's range and space allocation are not keeping pace.
- The 21% range cut (280→220 SKUs) risks removing high-CDI SKUs that are the category's recruitment engine.

Framing: "We are not asking Tesco to maintain 280 SKUs. We are proposing a 220-SKU range that performs better than the current 280 because it is architected around occasions and buyer segments rather than brand share."

### Page 2: Shopper diagnosis

**Title:** "Who is leaving the category and why"

Content:
- Kantar waterfall: 3.2M lapsed households profiled by Mosaic cluster — predominantly C2 (Comfortable Communities) and D (Aspiring Homemakers)
- Lapse drivers (from Kantar usage survey proxy): price sensitivity (ambient convenience up +8% ASP in 18 months due to input cost pass-through), occasion disruption (hybrid working shifted lunch patterns), perceived lack of innovation ("same products as 5 years ago")
- Benchmarks: soup penetration at Waitrose = 52%, Lidl = 39%. Tesco at 39.4% has significant headroom.

### Page 3: Range architecture proposal

**Title:** "220 SKUs, four segments, four PPU tiers — every cell earns its space"

Content:
- The segment × occasion × PPU tier grid (from Step 4)
- For each of the 60 delisted SKUs: reason for delist, estimated lost volume (buyer overlap metric), and which remaining SKU captures the demand
- For the 6 new chilled additions: CDI evidence, WD requirement, space requirement
- Planogram illustration (simplified, not full retail planogram): chilled bay expansion from 1.2m to 1.6m, ambient rebalancing to reduce Batchelors duplication

### Page 4: Activation calendar

**Title:** "Three category-building occasions, not three brand promotions"

| Occasion window | Mechanic | Category frame | Expected uplift |
|---|---|---|---|
| September back-to-school (Week 36–40) | "Weekday lunch sorted" — cross-category promotion with bread/crackers | Positions soup as the default weekday lunch in Tesco mission | +£1.8M for the 5-week window |
| January New Year wellness (Week 1–4) | Premium chilled feature: "Better for you" gondola end, no price cut | Occasion: healthier eating, not discount | +£0.9M, +14% chilled penetration |
| March Mother's Day (Week 10–11) | Premium occasion: "Restaurant-quality at home" | Chilled + ambient premium, meal kit adjacency | +£0.4M, trade-up occasion |

Key distinction: **category-building mechanic vs brand promotion**. A price promotion on Knorr grows Knorr's sales but may simply pull forward purchases or cannibalise Heinz — both zero-sum for the category. A cross-category meal occasion promotion (soup + crusty bread + cheese) grows the occasion value, bringing in new trips to the soups aisle that would not have occurred otherwise.

### Page 5: Mutual commitments

| Unilever commits | Tesco commits |
|---|---|
| Category value growth target: +6% (£28.8M) in 12 months | Maintain 220-SKU range per the agreed architecture for 12 months |
| RoS guarantee: any SKU falling below 2.5 units/point WD/week for 12 consecutive weeks will be voluntarily delisted by Unilever | List 6 additional chilled SKUs (Covent Garden, Knorr premium chilled) |
| Deploy 2 additional field reps to bring compliance from 71% to 81% in underperforming stores | Expand chilled soups bay by 0.4 linear metres in 350 stores (top volume) |
| Execute 3 category-building activation events (not brand-only promotions) | Co-invest in one Tesco Finest adjacent premium occasion event |
| Monthly category health reporting (Nielsen + Kantar) shared with Tesco buyer | Share planogram compliance data via JBP portal quarterly |

---

## What the Category Manager Knows That the Brand Manager Does Not

The five pieces of knowledge that are structurally inaccessible to a brand-only P&L:

**1. Total category demand elasticity**
The brand manager knows Knorr own-price elasticity (-1.4 ambient). The category manager knows that total soups category demand elasticity is approximately -0.6 (category-level demand is less elastic than brand-level demand because consumers are making a category choice, not just a brand choice). This means a 10% price increase across the entire ambient segment reduces category volume by ~6%, but a 10% Knorr price increase (with Heinz holding) reduces Knorr volume by ~14% with most of that switching to Heinz, not leaving the category.

**2. Competitor RoS and category growth contribution**
The brand manager sees Nielsen competitor data but does not routinely analyse whether Heinz's promotional activity is growing the category or stealing from Knorr. The category manager tracks competitor new-to-category rates and can identify that Batchelors heavy promotions are predominantly inter-brand switching with near-zero category recruitment.

**3. Space profitability metrics**
Tesco reports to category suppliers on sales per linear metre and margin per facing. The category manager has visibility of where soups sits in the retailer's space productivity ranking across all categories — ambient soups currently generates £840/linear metre/year vs the store average of £720 for ambient grocery. Soups over-indexes on space productivity; this is the strongest argument against a range cut.

**4. Cross-category basket complementarity (Kantar)**
Kantar's basket data shows that soup purchases correlate with bread (+0.68 basket correlation), cheese (+0.43), wine (+0.31), and premium crackers (+0.38). This means every incremental soup shopper trip also tends to include these categories. The total basket value of a soup-triggered shop is ~£38 vs a non-soup shop of ~£24. Tesco's interest in soups is not just soups value — it is the basket halo.

**5. Full range architecture including private label**
The brand manager does not have commercial responsibility for recommending private label listing or delisting. The category manager does. The decision to consolidate private label from 30 SKUs to 22 SKUs (removing the highest-duplication ambient PL SKUs) is only possible from the category P&L seat.

---

## What MMM Can and Cannot Tell the Category Manager

| What MMM tells the Category Manager | What requires additional data |
|---|---|
| **Own-brand volume elasticities** for each P lever (price, promo, distribution, media, pack) — from the Knorr, Heinz, Batchelors brand-level MMMs run separately | **Competitor intentions:** MMM uses historical competitor activity. It cannot tell you what Heinz will do in H2 |
| **Promotional uplift by brand and by mechanic** — e.g. Knorr multi-buy +19% vs Knorr price-marked pack +11% in the equivalent promotional slot | **Consumer switching attribution:** MMM tells you total brand volume moved; Kantar panel is needed to decompose how much was new category buyers vs inter-brand switching |
| **Cross-price elasticity proxies:** if Heinz ASP is included as a regressor in the Knorr MMM, the coefficient gives a rough cross-elasticity — but this is partial, not a full demand system | **Category-level demand elasticity:** requires a demand system (Almost Ideal Demand System or Rotterdam model) estimated on category-level data, not a single-brand MMM |
| **Distribution elasticity per brand:** beta_WD coefficients show how much a 1pp ND/WD gain lifts volume for each brand — directly actionable for the ranging decision | **Long-run penetration effects:** MMM's short-run elasticities (4–12 week window) do not capture whether a promotion *recruits new buyers* who return next quarter. Kantar panel is the only data source for this |
| **Execution quality effect (compliance → RoS):** if planogram compliance is included as a regressor, MMM can isolate the RoS lift attributable to execution vs underlying demand | **Retailer profitability per SKU:** margin per facing is a Tesco internal metric. MMM cannot derive this; it requires the Tesco JBP data-sharing agreement |
| **Media halo effects across brands:** if a Knorr media campaign lifts total soups consideration, category-level media response can be estimated by running MMM on total category volume with Knorr GRP as a regressor | **Consumer exit vs switching:** when a shopper stops buying Knorr, MMM cannot distinguish "bought Heinz instead" from "left the category." This decomposition requires panel data |

---

## Days Referenced

Each day that provides a specific analytical tool used in this case study:

| Day | Title (approximate) | Application in this case study |
|---|---|---|
| Day 7 | Price elasticity | Knorr own-price elasticity (-1.4) underpins Lever 1 (lapsed buyer conversion) and the JBP price architecture recommendation |
| Day 8 | Promotional response and adstock | Diminishing returns to promotional frequency (Hill saturation, alpha=0.6) justifies 2 events/quarter not 6 |
| Day 9 | Distribution and weighted distribution | Distribution elasticity formula (beta_WD × WD_mean / y_mean) quantifies the chilled distribution lever (+17% volume on +20pp WD) |
| Day 10 | Pack and assortment | Incrementality framework (buyer overlap as a proxy for substitutability) drives the 60-SKU delist criteria |
| Day 11 | People and execution | Planogram compliance gap (71% → 81%) and RoS differential between execution quartiles underpins Lever 3 |
| Day 13 | DAGs and confounding | Category CDI concept requires understanding that brand share and category recruitment are caused by different variables — a causal framing, not a correlation |
| Day 14 | OLS bias and endogeneity | Price endogeneity in the category MMM: brands lower price when volume is already weak, biasing OLS price coefficients; IV strategy (palm oil cost as instrument) corrects this |
| Day 15 | Instrumental variables | Palm oil price as instrument for ambient soup price in the category demand model |
| Day 16 | DiD and geo-tests | The WD uplift estimate is ideally validated by a geo-test: expand chilled in matched Tesco store clusters and measure category volume lift vs control |
| Day 20 | Demand systems and cross-elasticities | Full category demand model (Almost Ideal Demand System) is the correct framework for estimating total category elasticity and brand-to-brand switching vs category exit |
| Day 23 | Portfolio and assortment modelling | Segment × occasion × PPU tier grid architecture; range incrementality scoring |
| Day 24 | Scenario simulation and optimisation | Scenario modelling the three JBP levers to arrive at +£6.8M estimate; scipy.optimize for optimal promotional event timing |
| Day 28 | Execution quality and field sales ROI | The compliance → RoS relationship and field sales deployment (2 reps, 180 stores) investment case |

---

## A Final Note on the Category Manager's Analytical Edge

The brand manager's MMM asks: *"What drives Knorr volume?"*

The category manager's question is: *"What drives total soups category value at Tesco, and where is Unilever's role in growing that pool rather than redistributing it?"*

This distinction matters because Tesco's buyer is not interested in Unilever's internal market share optimisation. They are interested in whether soups will earn more space on the shelf next year. The Category Manager's job is to make that case with data — and to ensure that the data is category-level, not brand-level, wherever the commercial question demands it.

The MMM toolkit (price elasticity, promotional response, distribution elasticity, execution quality) applies at both levels. The category manager runs it on aggregate category data *and* on individual brand data, then interprets the results through the lens of total category value creation. That is the analytical translation this case study demonstrates.

---

*Return to course:* [Learning Path](../learning-path.md) | [Glossary](../glossary.md)
