# Day 8 — Promotion: Trade Mechanics and Borrowed Demand (Surf Excel)

> **Today's one idea:** Promotions create volume spikes that look like demand growth but are often demand borrowed from the future — failing to model this makes promotional ROI look far better than it is.
> **Reading time:** ~35 min · **Prereqs:** Days 4, 6–7
> **Primary source for today:** Charan, A. *The Marketing Analytics Practitioner's Guide* — the promotional effectiveness chapters
> **Before you start:** Recall Day 7's load-bearing idea — one sentence: what is price elasticity, and what are the three main entanglements that corrupt its estimate in FMCG data?

---

## The Hook

The Surf Excel trade marketing team runs a "3-for-2" promotion at Tesco in Week 20. Volume spikes 140% above the baseline. The team reports a promotional ROI of 2.4× — for every pound spent on the promotion, Surf Excel generated £2.40 in incremental revenue.

Weeks 21 and 22 are unusually quiet. Sales run 35% below the pre-promotion baseline.

The team attributes the post-promotion dip to "normal volatility." The MMM, fit on the raw data, assigns the 140% spike to the promotion coefficient and ignores the subsequent dip entirely — because the model has no variable for "weeks after a promotion." The reported ROI of 2.4× is wrong. The true net ROI, accounting for borrowed demand, may be negative.

This is the most common error in promotional analysis. Promotions don't create demand — in categories like laundry detergent, they *move* demand: from future periods into the present (stockpiling), and from full-price sales into discounted ones. The MMM must account for both effects to give an honest estimate.

---

## Building the Intuition

### Three types of promotional volume

When a Surf Excel promotion runs, the volume spike contains at least three components:

```
PROMOTIONAL VOLUME SPIKE (Week 20):
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│  Component 1: TRUE INCREMENTAL        (~30% of spike)          │
│  Consumers who wouldn't have bought Surf Excel at full          │
│  price — trial buyers, brand switchers from Ariel               │
│                                                                 │
│  Component 2: CATEGORY EXPANSION     (~10% of spike)           │
│  Consumers who buy detergent earlier than planned               │
│  (accelerated purchase) but would have bought anyway            │
│                                                                 │
│  Component 3: DEMAND PULL-FORWARD    (~60% of spike)           │
│  Loyal Surf Excel buyers who stock up at the discount —         │
│  they were going to buy in Weeks 21-23 anyway                   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

Only Component 1 is genuine incremental demand. Components 2 and 3 are borrowed from future periods — they reduce future sales and net out to zero (or negative, once promotion costs are included).

The post-promotion dip in Weeks 21–22 is the visible signature of Components 2 and 3 returning to baseline. A model that ignores this dip overestimates promotional effectiveness.

### The mechanics of common trade promotions

| Mechanic | Depth | Typical FMCG Category | Stockpiling propensity |
|----------|-------|----------------------|----------------------|
| % off shelf price (e.g., 20% off) | Moderate | Most FMCG | Medium |
| Multibuy (3-for-2, buy 2 get 1 free) | High effective discount | Laundry, personal care | High |
| Price marked pack (PMP) | Fixed discount, store-specific | Impulse, convenience | Low |
| Feature + display (aisle end) | Moderate, plus visibility | Food, beverage | Medium |
| BOGOF (buy one get one free) | 50% effective discount | Household, personal care | Very high |

For Surf Excel, which is a *storable* category (consumers can stockpile detergent for months), pull-forward demand is the dominant component of any deep promotion. For a perishable category like fresh produce, pull-forward is minimal (you can't stockpile strawberries).

**This is why pack size matters for promotion modelling:** large packs facilitate stockpiling; small packs (sachets, trial sizes) do not. The promotional response of a 5kg Surf Excel box will contain far more pull-forward than a 500g trial pack. Failing to distinguish these creates incorrect promotional ROI by pack.

### Modelling the post-promotion dip

The standard approach is to add **post-promotion dummy variables** to the model:

```python
import numpy as np
import pandas as pd

def create_promo_features(df, promo_col, post_weeks=3):
    """
    Create promotion flag and post-promotion dip dummies.
    df:          weekly dataframe
    promo_col:   column name for promotion indicator (0/1)
    post_weeks:  number of weeks after promo to model dip
    """
    df = df.copy()
    df['promo_flag'] = df[promo_col]
    
    for lag in range(1, post_weeks + 1):
        df[f'post_promo_lag{lag}'] = df[promo_col].shift(lag).fillna(0)
    
    return df

# Example output structure:
# Week | promo_flag | post_promo_lag1 | post_promo_lag2 | post_promo_lag3
# 19   |     0      |       0         |        0        |        0
# 20   |     1      |       0         |        0        |        0    ← promotion week
# 21   |     0      |       1         |        0        |        0    ← dip week 1
# 22   |     0      |       0         |        1        |        0    ← dip week 2
# 23   |     0      |       0         |        0        |        1    ← dip week 3
```

The coefficients on `post_promo_lag1`, `lag2`, `lag3` will be negative — they measure how much volume was borrowed into the promotion week from each subsequent week.

The **net promotional ROI** is then:

```math
\text{Net promo uplift} = \hat{\beta}_{\text{promo}} + \hat{\beta}_{\text{lag1}} + \hat{\beta}_{\text{lag2}} + \hat{\beta}_{\text{lag3}}
```

If $\hat{\beta}_{\text{promo}} = 0.8$ (80% uplift) and the lag coefficients sum to −0.65, net uplift is only 0.15 (15%). The promotion moved 65% of its apparent benefit from future periods.

---

## The Formal Picture

### The promotional variable taxonomy

A complete promotional specification for Surf Excel might include:

```math
\text{PromoEffect}_t = \beta_1 \cdot \text{PromoDepth}_t + \beta_2 \cdot \text{FeatureDisplay}_t + \beta_3 \cdot \text{PostLag1}_t + \beta_4 \cdot \text{PostLag2}_t
```

where:
- $\text{PromoDepth}_t = \frac{\text{List Price} - \text{ASP}_t}{\text{List Price}}$ — continuous discount depth (0 to 1)
- $\text{FeatureDisplay}_t$ — binary indicator for aisle-end display or feature ad
- $\text{PostLag}_{1,2}$ — post-promotion dip terms

Note the distinction between depth and mechanic: a 20% depth has the same `PromoDepth` whether it is a shelf price reduction or a multibuy, but the stockpiling behaviour differs. If data allows, code mechanic type separately.

### Promotional elasticity vs. price elasticity

A subtle but important distinction:

**Price elasticity** ($\eta$): the response to a *permanent* price change. If Surf Excel permanently raises price, the long-run volume response is governed by $\eta$.

**Promotional price elasticity** ($\eta_p$): the response to a *temporary* price reduction. Consumers know the promotion ends, so they stock up more aggressively than they would respond to a permanent price cut. Typically $|\eta_p| > |\eta|$ — promotions generate more volume per percent price change than permanent cuts.

```
Typical magnitudes for a mainstream laundry brand:
  Price elasticity (permanent):     η  ≈ −1.5 to −2.0
  Promotional elasticity (temp):    η_p ≈ −3.0 to −5.0
```

This is another reason using ASP (which includes promotions) as the sole price variable conflates two different demand responses — which Day 7 flagged and Day 17 will fix.

### Baseline vs. promoted volume: what this tells the brand

After modelling the full promotional dynamics (spike + dip), the model gives you:

```
Surf Excel — Promotional Analysis (52 weeks):

Gross promo uplift:     +18.4% of base  (what the promotion appeared to generate)
Post-promo dip:         −11.2% of base  (what was borrowed from future periods)
────────────────────────────────────────────────────────
Net promo uplift:        +7.2% of base  (genuine incremental demand)

Promotion cost:          £3.2M (trade spend + margin forgone)
Net revenue on uplift:   £2.8M
────────────────────────────────────────────────────────
Net promotional ROI:     −£0.4M  (negative)
```

The promotion looked like a 2.4× ROI before modelling the dip. It is actually value-destructive. This finding — which is common for storable FMCG categories — is one of the most commercially significant outputs an MMM can produce.

---

## Where It Breaks / What It Is Not

**"The post-promotion dip is always visible in the data."** In categories with weekly or bi-weekly purchase cycles (laundry, personal care), the dip is measurable. In categories with monthly or longer cycles (premium spirits, large appliances), the dip may be too diffuse to detect in weekly data — you might need 3–4 month lags. Choose lag length based on category purchase frequency, not model fit.

**"All promotional mechanics are equivalent."** As noted above, a multibuy in a storable category has fundamentally different demand dynamics than a price reduction in a perishable one. Model them separately when you have enough observations of each mechanic.

**"High promotional frequency makes the model better."** The opposite is often true. If Surf Excel is on promotion 40% of weeks, the "off-promotion" baseline is barely observed. The model has little clean variation in the non-promoted state to estimate true base sales — and the post-promotion dip bleeds into the next promotion's ramp-up, making them inseparable.

**Endogeneity of promotion timing (preview of Day 13).** Promotions are not randomly scheduled. They tend to run in slow periods (to boost sales) or in competitive response to Ariel activity. This timing correlation means the promotion flag is correlated with the error term — and the promotional coefficient is biased. The direction of bias is typically toward zero (the model underestimates the spike because it was run in a systematically weak period).

---

## Try It Yourself

> Close the page now before attempting Exercise 1.

**Exercise 1 — Retrieval.** Without looking: name the three components of a promotional volume spike. Which is the only genuinely incremental component? What is the typical post-promotion pattern for a storable FMCG brand?

<details>
<summary>Reference answer</summary>

Three components: (1) **True incremental** — trial buyers and brand switchers who wouldn't have bought at full price; (2) **Category expansion** — accelerated purchases by current buyers; (3) **Demand pull-forward** — loyal buyers stocking up.

Only Component 1 is genuinely incremental. Components 2 and 3 borrow from future periods.

Post-promotion pattern for storable FMCG (Surf Excel): sales dip below baseline for 2–4 weeks as stocked-up households delay their next purchase. The depth of dip depends on promotion depth and pack size — deeper promotions and larger packs produce deeper dips.
</details>

---

**Exercise 2 — Direct application.** Your Surf Excel MMM returns these coefficients:

- `promo_flag`: +0.72 (72% volume uplift on promo weeks)
- `post_promo_lag1`: −0.31
- `post_promo_lag2`: −0.22
- `post_promo_lag3`: −0.08

Surf Excel runs promotions 18 weeks per year. Base weekly volume is 45,000 units.

(a) Calculate net promotional uplift per promoted week.
(b) Calculate total net incremental volume from promotions over 52 weeks.
(c) If promotions cost £1.50 per incremental net unit, what is the total annual promotion cost vs. net volume gained?

<details>
<summary>Reference answer</summary>

(a) Net uplift per promoted week = 0.72 − 0.31 − 0.22 − 0.08 = **+0.11** (11% net uplift)

(b) Net incremental volume per promoted week = 45,000 × 0.11 = 4,950 units
Total across 18 promo weeks = 4,950 × 18 = **89,100 units**

(c) Annual promotion cost = 89,100 × £1.50 = **£133,650**. You need to compare this to the margin on 89,100 incremental units. If gross margin per unit is £0.80, gross margin contribution = £71,280 — less than the promotion cost. The promotion is net negative even with the conservative cost assumption.
</details>

---

**Exercise 3 — Stretch (callback to Day 3).** In the Dove MMM from Day 3, Promotions drove 8% of £120M = £9.6M in attributed sales. Using today's framework, explain why this figure almost certainly overstates true incremental promotional value — without knowing the specific post-promotion dip data.

<details>
<summary>Reference answer</summary>

The Day 3 decomposition attributed 8% of annual sales to promotions based on the gross uplift during promotional weeks. But the 8% figure does not subtract the post-promotion dip — the volume borrowed from adjacent weeks that shows up as negative residuals in non-promo weeks and is absorbed into the baseline or unexplained variance, not credited back against the promotion.

If the post-promo dip terms were modelled explicitly (and their negative coefficients summed into the net uplift), the 8% would shrink. For a storable personal care category like Dove Body Wash, typical pull-forward is 40–60% of the gross spike — suggesting the true net promotional share is closer to 3–5% rather than 8%.
</details>

---

**Transfer — apply it:**

> Think of a "campaign" or intervention in your own work that produced a short-term spike in a metric. Is there evidence of a post-spike dip? If you were modelling this, how would you decide how many lag periods to include for the dip?

---

## Connect It Back

Promotion interacts with Price (through depth), Pack (through stockpiling propensity), and Place (a promotion in a well-distributed SKU reaches more consumers). The entanglement deepens. Tomorrow we examine Place — the driver that is often the quietest in the boardroom but the largest in the model.

**Sharp question to carry forward:** A brand with 60% numeric distribution runs a national promotion. A brand with 30% numeric distribution runs the same promotion. Which brand gets more gross promotional uplift, and which gets more *net* incremental uplift? Are they the same?

*(Gross: the 60% brand, because more stores carry it and more consumers see the deal. Net: not necessarily — if the 60% brand has more loyal, stockpiling customers, its pull-forward proportion may be higher and net lift may be similar to or lower than the 30% brand.)*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:** Charan, A. *The Marketing Analytics Practitioner's Guide* — the promotional effectiveness chapter. Pay particular attention to Charan's discussion of how to measure promotional ROI net of cannibalization in FMCG contexts.

**If you want the deep version:**
- Robyn documentation — the "paid media vs. organic variables" section includes a discussion of how promotional flags are handled differently from media spend. Note that Robyn does not natively model the post-promo dip — this is a gap you must address manually.
- Jin et al. (2017) — read the footnote on "event variables" (non-media drivers). Their brief treatment confirms that promotional flags enter as contemporaneous variables without adstock transformation.

---

## Navigation

← **Previous:** [Day 7 — Price: Elasticity in MMM](./day-07-price-elasticity.md)
→ **Next:** [Day 9 — Place: Distribution as a Growth Driver](./day-09-place-distribution.md)
