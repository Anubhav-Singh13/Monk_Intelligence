# Glossary — MMM Mastery

Alphabetical. Each entry: plain-English definition · formal definition · introduced Day N.

---

**Adstock**
*Plain English:* The idea that advertising doesn't stop working the moment it stops airing — it leaves a "stock" of awareness that decays over time.
*Formal:* A geometric decay transformation applied to spend: $A_t = x_t + \lambda \cdot A_{t-1}$, where $\lambda \in [0,1]$ is the retention rate (also called decay or carryover parameter).
*Introduced: Day 4*

---

**Attribution**
*Plain English:* Crediting each marketing driver with a share of observed sales.
*Formal:* The allocation of a dependent variable $y_t$ across a set of driver variables $x_{1t}, \ldots, x_{kt}$ based on estimated coefficients in a regression model.
*Introduced: Day 1*

---

**Backdoor path**
*Plain English:* An indirect route in a causal graph from a cause to an effect that runs "backwards" through a common cause — a pathway that creates spurious correlation.
*Formal:* In a DAG, a path from $X$ to $Y$ that starts with an arrow into $X$ (i.e., $X \leftarrow \ldots \rightarrow Y$). Blocking all backdoor paths from $X$ to $Y$ is sufficient to identify the causal effect of $X$ on $Y$ (the backdoor criterion, Pearl 1993).
*Introduced: Day 14*

---

**Base sales**
*Plain English:* What the brand would sell if all paid marketing activity stopped tomorrow — driven by brand equity, distribution, seasonality, and long-run trends.
*Formal:* The intercept-equivalent component of the MMM decomposition: $\hat{y}_t^{\text{base}} = \alpha + \beta_{\text{season}} \cdot s_t + \beta_{\text{trend}} \cdot t$, where seasonality and trend are typically not marketing-controlled.
*Introduced: Day 3*

---

**Bayesian posterior**
*Plain English:* Your updated belief about a parameter after seeing the data — a full probability distribution, not a single number.
*Formal:* $P(\theta | \mathcal{D}) \propto P(\mathcal{D} | \theta) \cdot P(\theta)$, where $P(\theta)$ is the prior and $P(\mathcal{D}|\theta)$ is the likelihood. In MMM, $\theta$ includes media coefficients, decay rates, and saturation parameters.
*Introduced: Day 20*

---

**Cannibalization (promotional)**
*Plain English:* Volume sold during a promotion that would have been bought anyway — just earlier (pulled forward) or from the same shelf (substitution across pack sizes).
*Formal:* The negative autocorrelation in volume induced by promotion: $\text{net lift}_t = \text{promo lift}_t - \text{post-promo dip}_{t+1,\ldots,t+k}$. Naïve models that ignore this overstate promotional ROI.
*Introduced: Day 8*

---

**Carryover**
*Plain English:* The portion of an advertising effect that persists into future periods.
*Formal:* Synonymous with adstock in many implementations. The term "carryover" emphasises the time-persistence aspect; "adstock" is the name of the transformed variable.
*Introduced: Day 4*

---

**Causal identification**
*Plain English:* A causal effect is "identified" when you can compute it from the data without ambiguity — all confounding paths are blocked.
*Formal:* The effect of $X$ on $Y$ is identified if there exists an adjustment set $\mathbf{Z}$ (possibly empty) such that $P(Y | do(X)) = \sum_{\mathbf{z}} P(Y | X, \mathbf{Z}=\mathbf{z}) P(\mathbf{Z}=\mathbf{z})$ (Pearl's adjustment formula).
*Introduced: Day 15*

---

**Collider**
*Plain English:* A variable that is caused by two other variables in the DAG — conditioning on it opens a spurious association between its causes.
*Formal:* A node $C$ in a DAG where two arrows point *into* it: $X \to C \leftarrow Z$. Conditioning on a collider opens the path $X \to C \leftarrow Z$, inducing correlation between $X$ and $Z$ even if they are marginally independent.
*Introduced: Day 14*

---

**Confounder**
*Plain English:* A variable that affects both the marketing input and the sales outcome — creating a spurious correlation between them.
*Formal:* A variable $C$ such that $C \to X$ and $C \to Y$ in the DAG, where $X$ is the driver and $Y$ is sales. Uncontrolled confounders bias OLS estimates of the $X \to Y$ effect.
*Introduced: Day 13*

---

**Credible interval**
*Plain English:* The range of values that contains the true parameter with a given probability, given the data and the prior — the Bayesian analogue of a confidence interval.
*Formal:* A 95% credible interval $[a, b]$ satisfies $P(a \leq \theta \leq b | \mathcal{D}) = 0.95$. Unlike a frequentist confidence interval, a credible interval makes a direct probability statement about the parameter.
*Introduced: Day 20*

---

**Cross-price elasticity**
*Plain English:* How much your brand's volume changes when a competitor changes their price.
*Formal:* $\eta_{XY} = \frac{\partial \ln Q_X}{\partial \ln P_Y}$, where $Q_X$ is your brand's volume and $P_Y$ is the competitor's price. Positive values indicate substitutability; negative values indicate complementarity.
*Introduced: Day 23*

---

**DAG (Directed Acyclic Graph)**
*Plain English:* A diagram of arrows showing what causes what — with no loops allowed.
*Formal:* A graph $G = (V, E)$ where $V$ is a set of variables (nodes) and $E$ is a set of directed edges (arrows) representing causal relationships, with no directed cycles. Used to encode causal assumptions and determine identification strategies.
*Introduced: Day 14*

---

**Decay parameter (λ)**
*Plain English:* The fraction of last week's advertising effect that carries into this week. A value of 0.7 means 70% carries over; 0.3 dissipates.
*Formal:* The retention rate in the adstock recursion $A_t = x_t + \lambda A_{t-1}$. The mean lag (average time an ad effect persists) is $\frac{\lambda}{1-\lambda}$ periods.
*Introduced: Day 4*

---

**Decomposition**
*Plain English:* Breaking total observed sales into a sum of parts, each attributed to a driver.
*Formal:* $\hat{y}_t = \hat{y}_t^{\text{base}} + \sum_{k} \hat{y}_{kt}^{\text{incremental}}$, where each incremental component is $\hat{\beta}_k \cdot f(x_{kt})$ and $f$ is the appropriate transformation (adstock, Hill, etc.).
*Introduced: Day 3*

---

**DiD (Difference-in-Differences)**
*Plain English:* Comparing the change in outcomes in a treated group to the change in a control group over the same period — the "difference of differences" cancels out common trends.
*Formal:* $\hat{\tau}^{DiD} = (\bar{Y}_{1,\text{post}} - \bar{Y}_{1,\text{pre}}) - (\bar{Y}_{0,\text{post}} - \bar{Y}_{0,\text{pre}})$, where subscript 1 = treated, 0 = control. Identifies causal effects under the parallel-trends assumption.
*Introduced: Day 16*

---

**Endogeneity**
*Plain English:* When a driver variable (like price) is correlated with the error term — because it was set in response to the same conditions that drive the outcome.
*Formal:* $\text{Cov}(x_t, \epsilon_t) \neq 0$ in the regression $y_t = \alpha + \beta x_t + \epsilon_t$. Causes OLS estimates of $\beta$ to be biased and inconsistent.
*Introduced: Day 13*

---

**Hill function**
*Plain English:* The S-shaped curve that models diminishing returns to advertising spend. Also called the saturation curve.
*Formal:* $f(x) = \frac{x^{\alpha}}{x^{\alpha} + K^{\alpha}}$, where $K$ is the half-saturation constant (spend at which response is 50% of maximum) and $\alpha > 0$ controls the steepness. For $\alpha > 1$ the curve is sigmoidal; for $\alpha \leq 1$ it is concave.
*Introduced: Day 5*

---

**Incremental sales**
*Plain English:* The extra sales generated by a marketing driver above and beyond what the brand would have sold without it.
*Formal:* $\hat{y}_{kt}^{\text{incremental}} = \hat{\beta}_k \cdot f(x_{kt})$, where $f$ applies all relevant transformations. The sum of incremental components across all drivers is total marketing-driven sales.
*Introduced: Day 3*

---

**Instrumental variable (IV)**
*Plain English:* A variable that affects your endogenous driver (e.g., price) but has no direct effect on the outcome (e.g., sales) except through that driver — used to recover causal estimates when the driver is endogenous.
*Formal:* A variable $Z$ is a valid instrument for $X$ in the model $Y = \beta X + \epsilon$ if: (1) Relevance: $\text{Cov}(Z, X) \neq 0$; (2) Exclusion restriction: $\text{Cov}(Z, \epsilon) = 0$. The IV estimator is $\hat{\beta}^{IV} = \frac{\text{Cov}(Z, Y)}{\text{Cov}(Z, X)}$.
*Introduced: Day 17*

---

**MAPE (Mean Absolute Percentage Error)**
*Plain English:* The average percentage by which your model's predictions miss the actual values — a scale-free measure of fit.
*Formal:* $\text{MAPE} = \frac{1}{T} \sum_{t=1}^{T} \left| \frac{y_t - \hat{y}_t}{y_t} \right| \times 100$. In MMM, a holdout-period MAPE below 10% is generally considered acceptable.
*Introduced: Day 22*

---

**Marginal ROI**
*Plain English:* The extra revenue generated by spending one more pound on a channel at its current spend level.
*Formal:* $\text{mROI}_k = \hat{\beta}_k \cdot f'(x_k) \cdot \frac{\text{Revenue}}{\text{Volume}}$, where $f'$ is the derivative of the saturation transformation. At the optimal spend allocation, marginal ROIs are equal across all channels.
*Introduced: Day 25*

---

**Numeric distribution**
*Plain English:* The percentage of relevant stores (by count) that stock a given product.
*Formal:* $\text{ND} = \frac{\text{number of stores stocking SKU}}{\text{total number of stores in the universe}} \times 100$. Measured by Nielsen or Kantar store audits.
*Introduced: Day 9*

---

**Parallel trends assumption**
*Plain English:* In a DiD design, the assumption that the treated and control groups would have followed the same trend in the absence of the treatment.
*Formal:* $E[Y_{1t}(0) - Y_{1,t-1}(0)] = E[Y_{0t}(0) - Y_{0,t-1}(0)]$, where $Y(0)$ denotes the potential outcome without treatment. This assumption is untestable in the post-treatment period but can be assessed pre-treatment.
*Introduced: Day 16*

---

**Price elasticity**
*Plain English:* The percentage change in volume for a 1% increase in price. A value of −2 means a 1% price rise reduces volume by 2%.
*Formal:* $\eta = \frac{\partial \ln Q}{\partial \ln P}$. In a log-log regression $\ln Q = \alpha + \eta \ln P + \ldots$, the coefficient on $\ln P$ is the elasticity directly.
*Introduced: Day 7*

---

**Prior distribution**
*Plain English:* Your belief about a parameter before seeing the data — encoded as a probability distribution.
*Formal:* $P(\theta)$, the marginal distribution over parameters in the Bayesian model. In MMM, informative priors typically come from business knowledge (e.g., TV half-life is 2–6 weeks, not 52) and prevent physically implausible posteriors.
*Introduced: Day 20*

---

**Response curve**
*Plain English:* The graph of sales uplift vs. spend for a given channel, after applying adstock and saturation transformations.
*Formal:* $g(x) = \hat{\beta} \cdot h(f(x))$, where $f$ is adstock and $h$ is the Hill function. The curve's slope at any point is the marginal ROI; the point where slope = 1 (in revenue terms) is the efficiency frontier.
*Introduced: Day 25*

---

**ROI (Return on Investment) in MMM**
*Plain English:* Revenue generated per pound of marketing spend.
*Formal:* $\text{ROI}_k = \frac{\sum_t \hat{y}_{kt}^{\text{incremental}} \times \text{price}}{\sum_t x_{kt}}$. Note that this is a *total* ROI averaged over the observed spend range; it differs from marginal ROI.
*Introduced: Day 25*

---

**Weighted distribution**
*Plain English:* The percentage of total category sales (or volume) accounted for by stores that stock a given product. Higher than numeric distribution when the product is in bigger stores.
*Formal:* $\text{WD} = \frac{\sum_{j \in \text{stocking stores}} v_j}{\sum_{j \in \text{all stores}} v_j} \times 100$, where $v_j$ is store $j$'s category volume. Source: Nielsen or Kantar store audit.
*Introduced: Day 9*

---

**White space**
*Plain English:* Markets, geographies, or consumer segments where category demand exists but your brand's penetration or distribution is low relative to its potential.
*Formal:* Operationalised as the gap between a brand's observed penetration index (brand share of category in a given cell) and the penetration predicted by the cell's demographic and distribution profile. Cells with large negative residuals are white space.
*Introduced: Day 23*
