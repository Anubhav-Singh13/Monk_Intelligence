# Case Study: Brand Manager — Surf Excel South Asia Annual Growth Plan

---

## Preamble: the Brand Manager role

The Brand Manager owns the Surf Excel brand P&L across Pakistan and Bangladesh. Their goal is brand volume and value growth within the constraints of the portfolio strategy. They use MMM outputs to justify budget decisions, defend pricing to finance, and prioritise distribution investment. They present to the VP Marketing quarterly and to the CMO annually.

Unlike the category manager, they are measured on **own brand performance**, not total category. That distinction matters for every analysis in this case study: the brand manager asks "what grows Surf Excel?", not "what grows the laundry category?". The two questions diverge whenever a lever that benefits Surf Excel comes at the expense of OMO or when a Surf Excel price move causes consumers to exit the category entirely.

**Data the brand manager holds that the category manager does not:**
- Consumer-level Kantar Worldpanel data: which consumer segments are most loyal to Surf Excel vs most at risk to Ariel
- True gross margin by SKU and by channel
- Media agency relationships and spend efficiency benchmarks
- Internal cost structure and NPD pipeline
- Consumer segmentation scores from brand tracking surveys

---

## The Situation

Annual planning round. Surf Excel Pakistan faces a competitive threat: Ariel has launched a new economy 400 g pack at PKR 65, against Surf Excel's current pack ladder:

| Pack | Price (PKR) |
|------|-------------|
| 200 g sachet | 38 |
| 500 g box | 85 |

Ariel's new SKU occupies the PKR 60–70 tier with no Surf Excel equivalent. They are activating with trial-focused promotions in semi-urban markets where Surf Excel has weaker distribution and lower brand equity scores.

**Pakistan brand volume:** flat year-on-year.
**Bangladesh brand volume:** +4% YoY, but gross margins are under pressure from rising palm oil costs.
**Total brand budget for the next 12 months:** £11.2 M across both markets and all 5Ps.

The brand manager must walk into the VP Marketing meeting with a defensible annual plan. This case study is that plan being built from the MMM outputs up.

---

## Data Available

| Source | Granularity | Coverage |
|--------|-------------|----------|
| Nielsen retail audit | Weekly, store-level aggregate | 156 weeks, Pakistan and Bangladesh separately — ND, WD, ASP, RoS by SKU |
| Media investment | Weekly, by channel | 156 weeks — TV GRPs, digital spend, OOH, in-store |
| Kantar Worldpanel | 4-weekly | 52 waves — penetration, frequency, repeat rate, consumer segment by market |
| Internal finance | Monthly | Gross margin by SKU, trade investment by account |
| Media agency contracts | Quarterly | CPM benchmarks, planned vs actual GRP delivery |
| IV instrument | Weekly | Palm oil price index (Rotterdam spot) |

---

## Step 1: Where Did Volume Come From? — Decomposition Review

Before allocating a single pound of next year's budget, the brand manager reviews the Surf Excel MMM decomposition for the last 52 weeks. The decomposition answers: of the total volume sold, how much would have sold anyway (base) and how much was driven by each marketing lever?

### Decomposition table — Surf Excel Pakistan, last 52 weeks (illustrative)

| Component | Units ('000 tons) | % of total | Notes |
|-----------|-------------------|------------|-------|
| Base | 42.1 | 56% | Underlying brand equity + distribution residual |
| TV adstock | 11.3 | 15% | Includes carryover effect (lambda ~0.55) |
| Digital | 5.9 | 8% | Shorter adstock (lambda ~0.3) |
| Price effect | −3.7 | −5% | Volume lost vs counterfactual flat-price scenario |
| Trade promotions | 8.4 | 11% | Gross promotional lift; net ROI assessed separately |
| Distribution (WD gains) | 4.2 | 6% | Weighted distribution expansion in semi-urban |
| Pack mix | 2.8 | 4% | Sachet range contribution |
| Seasonality | 4.6 | 6% | Eid + monsoon peak |
| **Total** | **75.6** | **100%** | |

### Interpretation guide

**Declining base** (Day 3 — Decomposition foundations): The base captures everything not explained by the measured levers. A declining base signals brand equity erosion — consumers are switching or exiting the category even when prices and promotions are held constant. For Surf Excel Pakistan, the base is 56% of volume. If the base percentage is falling over rolling 52-week windows, that is a leading indicator of structural share loss that no promotional spend can reverse.

**High promotional contribution with negative net ROI** (Day 27 — Promotion depth and ROI): Promotions contribute 11% of volume. But gross promotional units do not account for the margin given away or the post-promotion dip. A net ROI below 1.0 means the promotion is destroying value even while boosting shipments. The brand manager must check: is the 8,400-ton promotional contribution generating incremental revenue, or is it pulling forward purchases from loyal buyers who would have bought anyway?

**Flat distribution contribution given RoS data** (Day 9 — Distribution fundamentals): If WD is expanding (numerically more stores stocking the SKU) but volume contribution from distribution is flat, the implied Rate of Sale per outlet is falling. That means new distribution is being gained in low-velocity stores. This is a warning that further WD investment without a velocity activation plan will dilute average RoS further.

### Key diagnostic questions before touching the budget

1. Is base growing or declining on a trailing 12-month vs prior 12-month comparison?
2. What is the incremental-to-base ratio? Industry norm for a mature FMCG brand is 40–50% incremental. Above 60% suggests over-reliance on short-term activation.
3. Which P has the highest ROI, and is that ROI causally identified or a correlation estimate?
4. Are promotional volumes preceded and followed by normal purchase rates (no pantry-loading dip), or is the post-promotion dip eroding the apparent lift?

---

## Step 2: The 5P ROI Table with Posterior Uncertainty

The MMM does not return a point estimate of ROI. It returns a posterior distribution. The brand manager must present not just the mean ROI but the uncertainty, and must be explicit about which effects are causally identified and which are correlational estimates that could be confounded.

### 5P ROI table — Surf Excel Pakistan

| Lever | ROI or elasticity (mean) | HDI 94% | Identification status | Validation source | Last-year investment |
|-------|--------------------------|---------|-----------------------|-------------------|----------------------|
| TV | 2.3× | [1.6, 3.1] | Partially identified | No DiD validation run | £3.8 M |
| Digital | 1.9× | [1.2, 2.7] | Partially identified | No DiD validation run | £1.4 M |
| Price | −2.1 elasticity | [−2.7, −1.6] | **Identified** via palm oil IV | IV first stage F = 31.4 | — |
| Trade promotions | 1.4× net ROI | [0.8, 2.1] | Identified with post-promo lag controls | Internal promo calendar match | £2.1 M |
| Distribution | 2.7× | [1.9, 3.6] | Mostly identified | Geo controls applied; DiD planned | £1.2 M |
| Pack (sachet range) | +11% volume | [+6%, +17%] | Partially identified | No holdout test | £0.7 M |

**Reading this table:**

- **TV near saturation signal:** A 2.3× ROI sounds strong, but the HDI [1.6, 3.1] is wide. If the response curve has flattened (Hill function alpha < 1 suggests diminishing returns set in early), the marginal ROI of the next £1 M in TV is below the mean ROI of the current £3.8 M. The brand manager should request the marginal ROI at the current spend level, not the average ROI.
- **Distribution is the highest mean ROI and the tightest HDI.** At 2.7× with HDI [1.9, 3.6], this is where every £1 of incremental investment is most likely to pay back. The geo control identification makes this more credible than the media estimates.
- **Promotions HDI spans 1.0.** The lower bound of [0.8] is below breakeven. This means there is meaningful posterior probability that the current promotional depth is destroying value. The brand manager needs to reduce promo depth or confirm with a geo-split test.
- **Price elasticity is the most important identified parameter.** At −2.1, Surf Excel buyers are relatively sensitive. A 10% price increase reduces volume by ~21%. This is the anchor for all pricing decisions in Steps 3 and 4.

**Budget guidance from this table:**

| Action | Lever | Rationale |
|--------|-------|-----------|
| Grow | Distribution | Highest ROI, mostly identified, white space exists |
| Grow | Digital | Lower cost-per-reach than TV in semi-urban; validate with geo test |
| Maintain | TV | Near saturation; do not increase before marginal ROI confirmed |
| Reduce | OOH (if measured low in any geo test) | Reallocate to distribution |
| Validate | TV ROI | Run a DiD pilot: pull TV in one region for 8 weeks, hold all else constant |
| Validate | Promo depth | Test shallower discount (15% vs 25%) in matched city pair |

---

## Step 3: Responding to the Ariel 400 g Threat

### Framing the decision

Ariel's 400 g pack at PKR 65 creates a value-tier gap in the Surf Excel pack ladder. The current gap between sachet (PKR 38) and box (PKR 85) is PKR 47. Ariel's new SKU sits squarely in that gap and gives trial-focused consumers a mid-format entry point that Surf Excel does not offer.

The risk is not only volume — it is consumer recruitment. Kantar data will show whether Ariel is pulling lapsed Surf Excel buyers back into the category at a lower price point (bad: they were Surf Excel consumers), or recruiting light users who were previously unaffiliated (neutral to Surf Excel if Surf Excel was not addressing them anyway).

Three strategic options:

---

### Option A: Hold price on 500 g, accept share risk

**Tool:** Cross-price elasticity estimate from Day 23.

Let the cross-price elasticity of Surf Excel 500 g demand with respect to Ariel 400 g price be denoted $\varepsilon_{cross}$. If Ariel's launch reduces the effective price ratio of a competitive wash (PKR 65 / 40 g = PKR 1.63/g vs Surf Excel PKR 85 / 500 g = PKR 1.70/g), Surf Excel is now modestly more expensive per gram.

```
Cross-price elasticity (estimated from Nielsen data):
  epsilon_cross ~ +0.4  [HDI: +0.2, +0.7]

Ariel price reduction effect on Surf Excel volume:
  Ariel 400g launch is a ~24% effective price reduction vs nearest prior Ariel 500g equivalent
  Estimated Surf Excel volume impact = +0.4 * (−0.24) = −9.6% 
  [range: −4.8% to −16.8%]
```

At current Pakistan volume of 75,600 tons, this implies a risk range of 3,600 to 12,700 tons over the next 12 months if Ariel achieves distribution parity.

**Option A verdict:** Defensible if Ariel's distribution remains below 40% WD (semi-urban only). If Ariel achieves 60%+ WD in the next 12 months (Ariel has the distribution muscle), Option A becomes untenable. The brand manager must set a WD tripwire: if Ariel 400 g exceeds 45% WD by Q2, trigger Option B or C immediately.

---

### Option B: Repackage 500 g to 400 g at PKR 68

**Tool:** Break-even formula (Day 7) and IV price elasticity.

The repackage reduces pack size by 20% while reducing price by ~20%. From the consumer's perspective the price-per-gram is approximately maintained. From the P&L perspective the cost-of-goods per pack falls (less product) but the fixed packaging cost per unit may rise.

**Break-even analysis:**

```python
# Break-even: what price increase % is needed to offset volume loss?
# Day 7 formula: break_even = price_inc% / (price_inc% + margin%)

# Surf Excel 500g gross margin assumption: 38%
# Repackage scenario: effective price increase = 0% (PKR 85 -> 68, size 500g -> 400g, 
# price per gram held constant)
# But if the repackage is perceived as a price cut (lower sticker price = trial benefit):

price_inc_pct = -0.20   # sticker price falls 20%
margin_pct = 0.38

# Volume needed to maintain gross profit at current absolute level:
# New GM per unit = 0.38 * (1 - 0.20) * price_per_unit = 0.304 * price_per_unit
# Volume uplift required = original_GM / new_GM_per_unit - 1

volume_uplift_required = (0.38 / (0.38 * 0.80)) - 1
# = 1/0.80 - 1 = 0.25 = 25% volume uplift needed to hold absolute gross profit

# Using IV elasticity of -2.1:
# A 20% effective price cut -> +42% volume response
# Net: volume increases ~42%, required 25% -> gross profit improves if IV estimate holds

expected_volume_uplift = abs(-2.1 * -0.20)   # = 0.42
print(f"Expected volume uplift: {expected_volume_uplift:.0%}")   # 42%
print(f"Required to break even: {volume_uplift_required:.0%}")   # 25%
print(f"Headroom: {(expected_volume_uplift - volume_uplift_required):.0%}")  # 17%
```

**Option B verdict:** On the IV elasticity estimate alone, Option B looks accretive — a 42% volume response well above the 25% break-even. However, the IV elasticity HDI lower bound is −1.6, implying a volume response of only 32%, still above break-even. **The key assumption:** that the price reduction on the 500 g is perceived as a price cut rather than a shrinkflation (pack size reduction). Kantar brand perception tracking must be monitored for "value for money" scores in the 4 weeks following launch.

---

### Option C: Launch new Surf Excel 400 g counter-SKU at PKR 62

**Tool:** Assortment incrementality test (Day 10), Kantar duplication rates, cross-SKU elasticity.

Before launching a new SKU, the brand manager must answer: does the new 400 g SKU recruit Ariel trial buyers (incremental volume) or cannibalise existing Surf Excel 500 g buyers (volume neutral, margin dilutive)?

```
Kantar duplication rate: % of Surf Excel 500g buyers who also bought Ariel in last 13 weeks
  Surf Excel Pakistan: 18% duplication with Ariel (i.e. 18% of Surf Excel buyers also buy Ariel)
  Target for counter-SKU: buyers who are Ariel-primary but Surf Excel-secondary (12% of Ariel buyers)

Cross-SKU elasticity (500g -> 400g internal):
  If both SKUs are on shelf simultaneously, what % of 500g buyers switch to 400g?
  Estimated from analogous Unilever pack-ladder launches in similar markets: 25-35% of 500g buyers
  trade down to 400g within 6 months.

Net incrementality calculation:
  Recruited Ariel trial buyers: +X new households
  Cannibalised 500g buyers: -0.30 * (current 500g volume)
  Net volume: X_recruited - 0.30 * 500g_volume
  
  For the SKU to be incremental: X_recruited > 0.30 * 500g_volume
```

At current 500 g volume of approximately 75,600 tons, the 30% cannibalisation risk is ~22,700 tons. The new 400 g SKU must recruit at least that many tons from competitive buyers to be volume-incremental. Given Ariel's current WD of ~35% in semi-urban, this is only plausible if the counter-SKU is distributed specifically in Ariel-stronghold outlets rather than universally.

**Option C verdict:** Viable only with selective distribution targeting Ariel-heavy trade areas. A universal launch risks heavy cannibalisation with insufficient competitive recruitment. The launch should be tested as a geo-split experiment in two Ariel-high WD cities before national rollout.

---

### Recommended option

**Option B: Repackage 500 g to 400 g at PKR 68**, with the following conditions:
1. Maintain the 200 g sachet to anchor the lower price tier.
2. Phase out 500 g in semi-urban within 12 months; retain 500 g in urban modern trade where larger pack sizes index high.
3. Run Kantar "value for money" tracking weekly for 8 weeks post-launch to catch negative perception signals early.

**Explicit assumption being made:** The IV price elasticity of −2.1 generalises from historical price variation to a pack-size repackage. This is untested. If the consumer reads the 400 g launch as "smaller pack, same quality" rather than "better price", the volume response will be below the IV estimate. This assumption must be validated with a price perception survey before committing to a national repackage.

---

## Step 4: Budget Allocation — £11.2 M Optimised

### Marginal ROI framework (Day 25)

The correct allocation criterion is not average ROI but marginal ROI — the return on the last pound spent in each lever. At diminishing returns, average ROI overstates the value of incremental spend in high-spend levers and understates the value in under-spent levers.

### Current vs optimised allocation

| Lever | Current allocation (£M) | Current % | Optimised allocation (£M) | Optimised % | Change | Evidence quality |
|-------|--------------------------|-----------|---------------------------|-------------|--------|-----------------|
| TV | 3.8 | 34% | 3.2 | 29% | −£0.6 M | Partially identified; marginal ROI below mean ROI |
| Digital | 1.4 | 13% | 1.9 | 17% | +£0.5 M | Partially identified; lower saturation point than TV |
| Trade promotions | 2.1 | 19% | 1.6 | 14% | −£0.5 M | HDI spans 1.0; reduce depth before cutting frequency |
| Distribution | 1.2 | 11% | 2.0 | 18% | +£0.8 M | Mostly identified; highest ROI, white space confirmed |
| Pack investment | 0.7 | 6% | 0.9 | 8% | +£0.2 M | Option B repackage cost — contingent on Step 3 go-ahead |
| Bangladesh (media) | 1.2 | 11% | 1.1 | 10% | −£0.1 M | Maintain; margin pressure limits upside |
| Bangladesh (distribution) | 0.4 | 4% | 0.3 | 3% | −£0.1 M | OMO overlap risk — category manager input required |
| Validation pilots | 0.4 | 4% | 0.2 | 2% | −£0.2 M | Reinvested into distribution |
| **Total** | **11.2** | **100%** | **11.2** | **100%** | | |

### Rationale for each material shift

**TV: −£0.6 M.** TV is near its saturation point on the Hill response curve. The mean ROI of 2.3× is the average across all spend; the marginal ROI at £3.8 M is estimated at approximately 1.4×. Reducing to £3.2 M moves spend closer to the inflection point while freeing capital for higher-marginal-return levers. This shift is a model extrapolation, not a causally validated decision — it requires a DiD TV blackout pilot (8 weeks in one region) to confirm before full commitment.

**Digital: +£0.5 M.** Digital has a lower estimated saturation threshold than TV (shorter adstock, more granular targeting). The HDI [1.2, 2.7] means the lower bound is still above 1.0 at current spend, suggesting there is headroom before saturation. The reallocation is contingent on running a geo-split digital test in H1 to confirm.

**Trade promotions: −£0.5 M.** The HDI lower bound of 0.8 is below breakeven. The reduction should come from promotional depth (reduce average discount from ~22% to ~16%) rather than from the number of promotional events. A shallower discount with the same media support may maintain volume lift while improving net ROI.

**Distribution: +£0.8 M.** The single highest-confidence reallocation. Distribution ROI is the highest in the table, the HDI is tightest, and the white space analysis in Step 5 confirms under-served geographies. This reallocation is backed by the closest thing to causal evidence in the plan.

**Pack: +£0.2 M.** Contingent on Option B approval. Covers tooling, packaging design, and trade sell-in for the 400 g repackage.

### Expected volume uplift from reallocation

```
Estimated incremental volume from reallocation (Pakistan only):
  Distribution expansion: +3,200 tons [HDI: +1,900, +4,600]
  Digital increase:       +900 tons   [HDI: +400, +1,500]
  TV reduction:           −600 tons   [HDI: −1,100, −200]
  Promo depth reduction:  −400 tons   [HDI: −800, +100]
  Net reallocation uplift: +3,100 tons [HDI: +400, +6,000]
```

The HDI is wide because TV and promo effects are only partially identified. The central estimate is a +4% volume uplift from reallocation alone before the pack repackage benefit.

**The one reallocation the brand manager should validate before fully committing:** The TV reduction. Pulling £0.6 M from TV based on a model extrapolation of diminishing returns carries execution risk — if the Hill curve parameters are mis-specified or if TV has unmodelled brand equity effects (long-run base building), the volume impact could be larger than −600 tons. Run an 8-week TV blackout in one matched region before committing to a full-year reduction.

---

## Step 5: Distribution Expansion Decision

### RoS threshold screening (Day 28) applied to Surf Excel Pakistan

The Rate of Sale screen asks: before investing in gaining a new outlet, is the current RoS in existing outlets high enough to justify a further WD push, or is the brand velocity already too low to make a new outlet economic for the retailer?

**Pakistan division screening:**

| Division / City | Current WD (%) | Current RoS (units/outlet/week) | RoS threshold met? | Action |
|-----------------|-----------------|----------------------------------|--------------------|--------|
| Lahore urban | 78% | 4.2 | Yes (threshold: 3.0) | Maintain; selective upgrades |
| Karachi urban | 82% | 3.8 | Yes | Maintain |
| Faisalabad | 61% | 3.5 | Yes | **Priority 1: WD expansion** |
| Multan | 54% | 3.3 | Yes (marginal) | **Priority 2: WD expansion with velocity watch** |
| Peshawar | 49% | 3.1 | Yes (marginal) | **Priority 3: WD expansion + activation** |
| Gujranwala | 43% | 2.4 | No (below threshold) | Velocity improvement first — activation spend before WD |
| Hyderabad (semi-urban) | 38% | 2.1 | No | Velocity improvement first |
| Structural white space: Mirpur Khas rural cluster | 22% | 1.6 | No | Consumer activation required before distribution investment |

**Three divisions for immediate WD investment:** Faisalabad, Multan, Peshawar — all above RoS threshold, WD below 65%, confirmed by CDI/BDI white space analysis showing Category Development Index > Brand Development Index (category is present but Surf Excel is under-indexed).

**Two divisions requiring velocity improvement first:** Gujranwala and Hyderabad. Adding distribution here before fixing RoS will dilute average outlet productivity and likely trigger de-listing within 6 months. The intervention is a 12-week activation programme (sampling, below-the-line activation, in-store visibility) before the trade sell-in push.

**Structural white space — Mirpur Khas rural cluster:** Category penetration is low (CDI low), not just Surf Excel brand development. Distribution without category activation is premature. The brand manager should flag this to the category manager — if this white space is worth developing, it requires a joint investment in category building (hand-washing habit messaging, entry-pack visibility) that sits above the individual brand budget.

### DiD pilot design for Faisalabad, Multan, Peshawar

```
Design: geo-split difference-in-differences
Treatment: WD expansion from current to ~75% in treatment cities
Control: matched cities on pre-period volume trend, baseline WD, demographic profile

Matching candidates:
  Faisalabad treated — Sargodha control (WD 62%, similar urban population, RoS 3.4)
  Multan treated — Bahawalpur control (WD 51%, similar income profile, RoS 3.1)
  Peshawar treated — Abbottabad control (WD 47%, similar RoS 3.0)

Pre-period: 13 weeks (verify parallel trends assumption)
Treatment period: 26 weeks (allow time for distribution to compound into RoS)
Primary outcome: weekly Nielsen volume (WD * RoS * outlets)
Secondary: household penetration from Kantar if sample permits

DiD estimand:
  tau_DiD = (treated_post - treated_pre) - (control_post - control_pre)
  
Threat to validity: if media spend differs between treated and control cities during the
  treatment period, the distribution effect is confounded with media. 
  Mitigation: hold media spend constant across all six cities during the pilot.

Expected timeline: H1 pilot results available by week 30, budget reallocation for H2 confirmed.
```

### Bangladesh: modified approach

Bangladesh has Nielsen coverage but lower geographic granularity than Pakistan — division-level data only, no city-level split. The RoS screen is applicable but the DiD design must use divisions as the unit of randomisation rather than cities. With only 8 divisions, the statistical power of a 3-treated / 3-control split is limited (minimum detectable effect is larger). The brand manager should:
1. Apply the RoS screen at division level (same threshold logic applies).
2. Treat the DiD pilot as indicative rather than confirmatory — support with a synthetic control approach (Day 19) to extract more signal from the limited number of units.
3. Prioritise Dhaka division for any WD investment given the highest CDI and existing RoS above threshold.

---

## Step 6: The Annual Brand Plan One-Pager

This is the document the brand manager presents to the VP Marketing. It is one page. The analytical detail lives in the appendix; this page must carry the decision.

---

### Surf Excel South Asia — Annual Brand Plan FY26

**Volume targets**

| Market | Volume target (tons) | YoY growth |
|--------|----------------------|------------|
| Pakistan | 80,100 | +6.0% |
| Bangladesh | 48,800 | +4.0% |
| **South Asia total** | **128,900** | **+5.3%** |

**Value targets**

| Market | Net revenue target | Gross margin target |
|--------|-------------------|---------------------|
| Pakistan | PKR 6.8 Bn | 36% (−2pp from Option B repackage dilution; recovers to 38% in Y2) |
| Bangladesh | BDT 5.1 Bn | 34% (palm oil headwind assumed at current forward curve) |

**Strategic priority for FY26 (one sentence):** Close the semi-urban distribution gap in Pakistan while defending the 500 g price tier through the 400 g repackage before Ariel converts trial buyers into loyalists.

**Investment split — £11.2 M total**

| Lever | £M | % of total | Market |
|-------|----|------------|--------|
| TV | 3.2 | 29% | Pakistan 2.6, Bangladesh 0.6 |
| Digital | 1.9 | 17% | Pakistan 1.5, Bangladesh 0.4 |
| Trade promotions | 1.6 | 14% | Pakistan 1.2, Bangladesh 0.4 |
| Distribution | 2.3 | 21% | Pakistan 2.0, Bangladesh 0.3 |
| Pack (400 g repackage) | 0.9 | 8% | Pakistan only |
| Validation pilots | 0.7 | 6% | Pakistan DiD pilots (TV + distribution) |
| Consumer activation (RoS repair) | 0.6 | 5% | Gujranwala, Hyderabad |
| **Total** | **11.2** | **100%** | |

**Three risks**

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Ariel responds to 400 g repackage with further price cut on their 400 g SKU | Medium | High — could erode the PKR 65–68 tier entirely | Hold Option C (counter-SKU) in reserve; tripwire at Ariel WD > 55% |
| Palm oil cost spike (Bangladesh) | High (futures market signals) | Gross margin erosion to 30% | IV elasticity gives room for a 5% price increase in Bangladesh with acceptable volume cost |
| Distribution execution failure (Faisalabad / Multan / Peshawar pilot) | Medium | Budget deployed into low-WD stores without RoS support | DiD pre-period monitoring; 8-week go / no-go gate before full deployment |

**What we know causally vs what we are betting on**

| Decision | Causally identified? | Basis | Risk if wrong |
|----------|---------------------|-------|---------------|
| Price elasticity = −2.1 | Yes | Palm oil IV, F=31.4, Day 15 IV standard | Low; IV is the strongest identification in the plan |
| Distribution ROI = 2.7× | Mostly | Geo controls, DiD planned | Medium; confounding from co-occurring media possible |
| TV marginal ROI below mean | No | Model extrapolation from Hill curve | High; TV blackout pilot required before committing |
| Option B volume response | Assumption | IV elasticity generalised to pack repackage | High; brand perception must be tracked post-launch |
| Promo depth reduction neutral on volume | Partially | Post-promo lag model; HDI spans 1.0 | Medium; run one matched city test in Q1 |

**Validation commitments — H1**

| Pilot | Design | Decision gate |
|-------|--------|---------------|
| TV blackout — one region, 8 weeks | DiD; matched control region | By Week 20: confirm TV reduction or restore |
| Distribution expansion — Faisalabad, Multan, Peshawar | DiD; 3 matched control cities | By Week 30: confirm £0.8 M distribution shift for H2 |
| Promo depth test — 16% vs 22% discount | Matched city pair | By Week 16: confirm promo depth reduction |
| 400 g pack perception — Kantar tracking | Weekly brand health monitor | By Week 8 post-launch: abort repackage if value-for-money score drops >5pp |

---

## Where the Brand Analysis Ends and the Category Analysis Begins

The brand manager's annual plan is internally coherent. It is not automatically portfolio-coherent. Before presenting to the VP Marketing, the brand manager must stress-test three interfaces with the category manager:

**1. Price-led category exit.** Surf Excel's −2.1 price elasticity measures brand-level switching. It does not measure how many consumers exit the laundry category entirely when Surf Excel raises price. If Surf Excel is the category entry price point in Bangladesh (consumers who cannot afford Surf Excel use hand soap or nothing), a price increase justified by the IV elasticity could destroy category volume. The category manager's MMM, which models category-level demand, must be consulted before any price increase above 5% in Bangladesh.

**2. Distribution at the expense of OMO.** In Bangladesh, OMO and Surf Excel compete for the same shelf space in the same outlets. The brand manager's recommended distribution increase of £0.3 M in Bangladesh may be gained by displacing OMO facings. The category manager must confirm that the Surf Excel distribution plan does not push OMO below its minimum viable shelf presence — a Unilever brand-vs-brand cannibalisation that harms the total portfolio even while benefiting the Surf Excel P&L.

**3. Annual plan vs JBP negotiation.** The Surf Excel annual brand plan feeds into the Joint Business Plan with key accounts. If the brand plan calls for a major price change or WD expansion in a retail account, the category manager must assess whether this is consistent with the category-level commitment made in the JBP. A price move that conflicts with the JBP category strategy creates execution risk at the trade level.

The brand plan should be shared with the category manager before the VP Marketing presentation. It is not a final document until that review is complete.

---

## Days Referenced

Every analytical step in this case study draws on specific course days. The table below maps the decision to the day and states exactly how the day's concept was applied.

| Day | Title | Application in this case study |
|-----|-------|-------------------------------|
| Day 1 | MMM fundamentals | Decomposition logic: separating base from incremental volume; defining what the model is measuring |
| Day 3 | Decomposition interpretation | Reading a declining base as brand equity erosion; interpreting the incremental-to-base ratio |
| Day 7 | Price and break-even | Break-even formula applied to Option B repackage: volume uplift required to maintain absolute gross profit |
| Day 8 | Price endogeneity | Why OLS price elasticity is biased; why the IV estimate is used instead for all pricing decisions |
| Day 9 | Distribution fundamentals | RoS interpretation: flat distribution contribution + expanding WD = falling RoS per outlet |
| Day 10 | Assortment and pack | Incrementality test for the 400 g counter-SKU (Option C); cross-SKU cannibalisation estimation |
| Day 11 | Promotions | Net ROI calculation including post-promotion dip; promo depth vs frequency trade-off |
| Day 12 | Media and adstock | TV adstock lambda; Hill response curve saturation; why marginal ROI differs from average ROI |
| Day 13 | DAGs and confounding | Identifying which paths in the DAG are open when interpreting media and distribution coefficients |
| Day 15 | Instrumental variables | Palm oil IV for price endogeneity; first-stage F-statistic as validity check; IV estimate anchors all pricing decisions |
| Day 16 | DiD design | Design of the distribution expansion geo-split pilot; parallel trends assumption; matched control selection |
| Day 19 | Synthetic control | Bangladesh DiD modification: synthetic control approach when number of geographic units is small |
| Day 23 | Cross-price and CDI/BDI | Cross-price elasticity used to quantify Ariel threat (Option A); CDI/BDI white space analysis for distribution prioritisation |
| Day 25 | Marginal ROI and optimisation | Distinguishing average ROI from marginal ROI; budget reallocation logic; why TV near saturation justifies reduction |
| Day 27 | Promotional ROI and depth | Net ROI including margin cost of discount; HDI spanning 1.0 as signal to reduce promo depth |
| Day 28 | Distribution screening | RoS threshold screen applied to Pakistan divisions; velocity improvement precondition before WD investment |
| Day 30 | Capstone integration | Full annual plan structure: decomposition → ROI table → budget optimisation → validation commitments |

---

## Navigation

[Back to Case Studies index](../README.md)
