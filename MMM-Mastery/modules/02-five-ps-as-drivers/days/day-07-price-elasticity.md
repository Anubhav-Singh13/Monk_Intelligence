# Day 7 — Price: Elasticity in MMM (Surf Excel)

> **Today's one idea:** Price elasticity in MMM measures the % volume change per 1% price change — but in FMCG data, this number is entangled with promotions, pack size, and competitor pricing, and untangling it is the hard part.
> **Reading time:** ~35 min · **Prereqs:** Days 3–6
> **Primary source for today:** Charan, A. *The Marketing Analytics Practitioner's Guide* — the pricing and demand chapters
> **Before you start:** Recall Day 6's load-bearing idea — one sentence: what are the 5 Ps and what does each become in the MMM?

---

## The Hook

Surf Excel's brand manager in Pakistan is considering a 7% price increase. The category is growing 4% in value but flat in volume — consumers are trading up. The CFO wants the margin. The sales director is worried about volume loss.

The brand manager turns to the MMM: "What is our price elasticity?" The model says −1.8. She concludes: a 7% price rise will reduce volume by $7 \times 1.8 = 12.6\%$.

She raises the price. Volume falls 21%.

What went wrong? The elasticity of −1.8 was estimated from data where price promotions were frequent, pack size was changing, and a competitor (Ariel) had been selectively cutting price in large-pack formats. The model conflated all of these price signals into one coefficient. The coefficient of −1.8 was not the causal elasticity; it was a weighted average of several different price effects at different points in the volume distribution.

Today we establish what price elasticity *should* measure, how to enter price into the MMM, and where the estimate typically breaks down. Days 13–17 fix the break.

---

## Building the Intuition

### What elasticity means

Price elasticity answers the question: *if price rises by 1%, by what percentage does volume change?*

For a normal good (one where demand falls when price rises), elasticity is negative. The scale tells you how sensitive:

| Elasticity | Category type | FMCG example |
|-----------|--------------|-------------|
| 0 to −0.5 | Inelastic — consumers barely respond | Table salt, basic staples |
| −0.5 to −1.0 | Mildly elastic | Premium personal care (Dove) |
| −1.0 to −2.0 | Elastic | Mainstream laundry (Surf Excel) |
| −2.0 to −4.0 | Highly elastic | Commoditised categories, private label |
| Below −4.0 | Very highly elastic | Rarely seen for branded FMCG; usually a modelling error |

Surf Excel in Pakistan sits in the −1.5 to −2.0 range — a significant response to price, typical for a category where private label and unbranded competition are viable alternatives.

### Why FMCG price elasticity is messy

In a laboratory, you change price and observe volume. In FMCG retail data, "price" is the Nielsen Average Selling Price — a weekly average across all promotions, pack sizes, and retailer formats. This means:

1. **Price and promotions are inseparable in the data.** A week where Surf Excel's ASP is £3.50 instead of £4.20 is almost always a promotional week — the ASP drop is the promotional discount. If you put both ASP and a "promo flag" in the model, they will be collinear.

2. **Pack mix affects ASP.** If Surf Excel sells more large packs one week (which have a lower ASP per kg), the average ASP falls — not because the price of any given pack changed, but because the mix shifted. A naïve model attributes this ASP decline to a "price cut" when it was actually a "pack mix shift."

3. **Competitor pricing affects your volume.** If Ariel cuts price the same week Surf Excel holds price, Surf Excel's volume will fall — but this looks in the data like Surf Excel's volume fell at a high ASP, which may look like a *negative* demand shock rather than a competitive response.

### Own-price vs. cross-price elasticity

**Own-price elasticity:** how Surf Excel volume responds to Surf Excel price changes.
**Cross-price elasticity:** how Surf Excel volume responds to *competitor* (Ariel) price changes.

```
Own-price:    ΔQ(Surf Excel) / Q(Surf Excel)    < 0 (own price up → own volume down)
              ΔP(Surf Excel) / P(Surf Excel)

Cross-price:  ΔQ(Surf Excel) / Q(Surf Excel)    > 0 (competitor price up → own volume up)
              ΔP(Ariel) / P(Ariel)
```

Most MMM models include own-price but omit competitor price — which biases the own-price coefficient. If Surf Excel and Ariel move prices in tandem (common in oligopolistic retail), omitting Ariel's price creates a confound. Day 23 (Competitor Analysis) addresses this directly.

---

## The Formal Picture

### Entering price into the MMM

The standard specification is a **log-log** regression for the price term:

```math
\ln(\text{Volume}_t) = \alpha + \eta \cdot \ln(\text{ASP}_t) + \ldots + \epsilon_t
```

In this specification, $\eta$ is the price elasticity directly — it is the coefficient on log-price when the dependent variable is log-volume. Log-log has two advantages:
1. Elasticity is constant across the price range (a simplifying assumption, but widely used)
2. The residuals are more normally distributed for multiplicative demand models

An alternative is the **semi-log** specification (log volume, level price):

```math
\ln(\text{Volume}_t) = \alpha + \gamma \cdot \text{ASP}_t + \ldots + \epsilon_t
```

Here, $\gamma$ is not elasticity directly — elasticity at mean price $\bar{P}$ is $\eta = \gamma \cdot \bar{P}$. The semi-log specification allows elasticity to vary with price level (elasticity is larger in absolute terms at high prices), which is more realistic for FMCG.

### The relative price index — a better specification

Rather than using ASP in isolation, many practitioners use a **relative price index**:

```math
\text{RelPrice}_t = \frac{\text{ASP}_t^{\text{Surf Excel}}}{\text{ASP}_t^{\text{category average}}}
```

This has three advantages:
1. It automatically controls for category-wide price movements (inflation, commodity cost pass-through)
2. It captures competitive position, not absolute price level
3. It is more stable across geographies and time periods

```python
import pandas as pd

# Construct relative price index from Nielsen data
df['rel_price'] = df['surf_excel_asp'] / df['category_asp']
df['log_rel_price'] = np.log(df['rel_price'])

# Expected coefficient: negative (Surf Excel volume falls when 
# relatively more expensive than category average)
```

### What the coefficient tells you

If the fitted coefficient on log relative price is $\hat{\eta} = -1.8$:

- A 1% increase in Surf Excel's price relative to the category average reduces volume by 1.8%
- A 7% price rise reduces volume by approximately $7 \times 1.8 = 12.6\%$ *holding everything else constant*
- "Holding everything else constant" means: competitor prices, promotional activity, distribution, and all other Ps are unchanged — a condition that rarely holds in practice

The business translation: at $\eta = -1.8$, Surf Excel has significant **pricing power** (elasticity > −2.0) but is not inelastic. There is room for a small price increase if margin gains outweigh volume loss, but only if competitors do not respond.

### The margin-weighted decision rule

Whether a price increase improves profit depends on the margin structure:

```math
\text{Break-even volume loss} = \frac{\text{Price increase \%}}{\text{Price increase \%} + \text{Gross margin \%}}
```

For a 7% price increase with 40% gross margin:

```math
\text{Break-even} = \frac{7\%}{7\% + 40\%} = 14.9\%
```

If the model predicts a 12.6% volume loss, the price increase is profitable (12.6% < 14.9% break-even). But this calculation ignores competitive response, distribution reactions, and long-run brand equity effects — all topics for Days 20–26.

---

## Where It Breaks / What It Is Not

**"The MMM price elasticity IS the true price elasticity."** It is the *observed* elasticity from the historical data, which includes all the entanglement described above. The causal price elasticity — what would happen to volume if only price changed — requires either an instrumental variable (Day 17) or a controlled experiment. These can differ substantially.

**"A coefficient of −1.8 applies across all price ranges."** Log-log models assume constant elasticity. In practice, elasticity is typically more negative at high absolute prices (consumers switch to private label) and less negative at low prices (the brand is already accessible). For large price moves, use the semi-log specification.

**"Price is the most important P."** In many FMCG categories, distribution (Place) and promotional activity (Promotion) have larger coefficients than Price. Price is often the most *actionable* P from the finance team's perspective, but it is not always the largest volume driver.

**Endogeneity warning (preview of Day 13).** Price is set by humans who have access to demand information you do not have in the model. In high-demand periods, brands sometimes *raise* price or run fewer promotions. This means the error term $\epsilon_t$ correlates with ASP — and the OLS estimate of $\eta$ is biased. The direction of bias is typically toward zero (the coefficient is less negative than the true elasticity) or even positive in extreme cases. Day 17 (Instrumental Variables) fixes this.

---

## Try It Yourself

> Close the page now before attempting Exercise 1.

**Exercise 1 — Retrieval.** Without looking: write the log-log price elasticity formula — what are the two variables, what is the coefficient, and what does it mean in words?

<details>
<summary>Reference answer</summary>

$\ln(\text{Volume}_t) = \alpha + \eta \cdot \ln(\text{Price}_t) + \ldots$

The coefficient $\eta$ is the price elasticity: a 1% increase in price leads to an $\eta$% change in volume. For a normal good, $\eta < 0$. For Surf Excel (elastic category), $\eta \approx -1.5$ to $-2.0$.
</details>

---

**Exercise 2 — Direct application.** Surf Excel Pakistan has the following data for a recent 13-week period:
- Average ASP: PKR 285/kg
- Gross margin: 38%
- MMM-estimated price elasticity: −1.65 (from historical log-log model)
- Proposed price increase: +5%

(a) Predicted volume change
(b) Break-even volume loss
(c) Is the price increase profitable, per the MMM?
(d) Name one assumption in (c) that is most likely to break in practice.

<details>
<summary>Reference answer</summary>

(a) Predicted volume change = −1.65 × 5% = −8.25%

(b) Break-even = 5% / (5% + 38%) = 11.6%

(c) Yes — 8.25% predicted loss < 11.6% break-even → the price increase is expected to be profitable.

(d) The assumption most likely to break: competitor response. The break-even calculation assumes Ariel holds price. If Ariel does not follow the increase (or actively cuts), Surf Excel's volume loss will exceed the model prediction and may exceed the break-even threshold.
</details>

---

**Exercise 3 — Stretch.** You have two MMM estimates of Surf Excel price elasticity: one from a model using ASP (−1.4), another using a relative price index (−1.9). The models are otherwise identical. Which estimate should you trust more, and why? What might explain the difference?

<details>
<summary>Reference answer</summary>

Trust the relative price index estimate more. ASP conflates Surf Excel's own price changes with category-level price inflation — in periods of overall price inflation, ASP rises for all brands simultaneously, reducing apparent own-price sensitivity. The relative price index isolates Surf Excel's price *position* in the category, which is what consumers compare when making purchase decisions.

The difference (−1.4 vs −1.9) likely reflects inflationary periods where category ASP and Surf Excel ASP rose together, making the brand's volume decline look smaller than it actually was relative to the category. The relative price model correctly assigns this volume loss to competitive position, not brand-level demand.
</details>

---

**Transfer — apply it:**

> In your domain, what is the "price" of whatever you are selling or modelling — the variable that, when it increases, reduces demand? Write one sentence on whether you believe the observed coefficient on this variable is causal, and why or why not.

---

## Connect It Back

Price is the most actionable lever and the most treacherous to estimate. Today established what elasticity should mean, how to enter it in the model, and the three entanglements that corrupt the estimate. Tomorrow: Promotion — the P that most directly interacts with Price in FMCG data, and the one most prone to the "borrowed demand" illusion.

**Sharp question to carry forward:** If Surf Excel runs a heavy promotion (50% of weeks in the year), what effect does this have on the precision of the price elasticity estimate — not the bias, but the *precision*?

*(Answer: It reduces precision. Price variation that is almost entirely promotional makes it hard to estimate the non-promotional price effect — there isn't enough variation in non-promotional price to identify the coefficient separately.)*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:** Charan, A. *The Marketing Analytics Practitioner's Guide* — the pricing analytics chapter. Focus on how Charan frames the distinction between absolute price and relative price index in FMCG contexts.

**If you want the deep version:**
- Angrist & Pischke (2009), Chapter 3, Section 3.1 — the omitted variable bias formula applied to price: what happens to the price coefficient when competitor price is omitted.
- Varian, H.R. (2016). "Causal inference in economics and marketing." *PNAS* — the section on price endogeneity in marketing data (pp. 7312–7313).

---

## Navigation

← **Previous:** [Day 6 — The 5Ps Framework](../../01-foundations/days/day-06-five-ps-framework.md)
→ **Next:** [Day 8 — Promotion: Trade Mechanics & Borrowed Demand](./day-08-promotion-mechanics.md)
