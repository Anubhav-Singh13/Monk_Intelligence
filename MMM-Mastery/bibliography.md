# Bibliography — MMM Mastery Course Shelf

Full annotated sources. Every entry includes: full citation, what it covers, whose intuition it favours, where it is hardest, and which course days rely on it most.

---

## Foundational Books

### 1. Angrist, J.D. & Pischke, J.S. (2009). *Mostly Harmless Econometrics: An Empiricist's Companion*. Princeton University Press.

**What it covers.** The practical toolkit of causal identification: potential outcomes, regression anatomy, instrumental variables, difference-in-differences, and regression discontinuity. Written for empirical researchers who want to answer causal questions with observational data — exactly the position an MMM practitioner is in.

**Whose intuition it favours.** Economists and policy researchers. The examples are labour economics (wages, education, returns to schooling), but the mechanics translate cleanly to FMCG once you see that "price" in marketing plays the same endogenous role as "schooling" in their examples.

**Where it is hardest.** Chapter 4 (IV) requires patience. The authors derive everything from first principles, which is rigorous but dense on a first read. Work through the Angrist-Krueger (1991) compulsory schooling example slowly — the parallel to using input cost shocks as an instrument for price will click.

**Course days that rely on it most.** Days 16–17 (DiD, IV) use this as the primary reference. Days 13–15 use Chapter 2 (regression anatomy) for the confounding discussion.

**Recommended entry point.** Chapter 1 (Introduction) + Chapter 3 Section 3.1 (the conditional independence assumption) — read after Day 15.

---

### 2. Gelman, A., Hill, J., & Vehtari, A. (2021). *Regression and Other Stories*. Cambridge University Press.

**What it covers.** Bayesian and classical regression from a practical modelling perspective. Covers model building, prior selection, posterior predictive checks, and causal inference — in that order, with worked R examples throughout (Python translations exist in the community).

**Whose intuition it favours.** Applied statisticians. The authors are unusually honest about what regression does and does not tell you — a quality shared with this course.

**Where it is hardest.** Chapters 19–22 (causal inference) assume you are comfortable with Chapters 1–18. Do not jump ahead.

**Course days that rely on it most.** Days 20–22 (Bayesian MMM, Python build, validation). Chapter 9 (prediction and Bayesian inference) is the direct reference for Day 20 priors. Chapter 11 (assumptions, diagnostics) maps to Day 22 validation.

**Recommended entry point.** Chapter 1 (Why learn regression?) + Chapter 9 (Prediction and Bayesian inference) — read after Day 20.

---

### 3. Cunningham, S. (2021). *Causal Inference: The Mixtape*. Yale University Press.
Free online: [https://mixtape.scunning.com](https://mixtape.scunning.com)

**What it covers.** A graduate econometrics text written with unusual clarity and wit. Covers potential outcomes, DAGs, matching, DiD, synthetic control, regression discontinuity, and IV. Code in R and Stata (Python ports in the community).

**Whose intuition it favours.** Empirical social scientists and anyone who prefers narrative over notation. The running examples are from economics (crime, health, education), but the causal logic is universal.

**Where it is hardest.** The IV chapter (Chapter 7) is the most technically demanding. The DiD chapter (Chapter 9) is the most practically useful for MMM geo-tests.

**Course days that rely on it most.** Alternative intuition source for Days 13–17. Use when Angrist & Pischke feels too terse.

**Recommended entry point.** Chapter 1 (Introduction) — read at any point. Chapter 9 (Difference-in-differences) — read after Day 16.

---

### 4. Huntington-Klein, N. (2021). *The Effect: An Introduction to Research Design and Causality*. CRC Press.
Free online: [https://theeffectbook.net](https://theeffectbook.net)

**What it covers.** Research design through a causal lens. Exceptional on DAGs and identification — the plain-English treatment of "what does it mean for an effect to be identified" is the best available for non-economists.

**Whose intuition it favours.** Social scientists and practitioners new to causal reasoning. Less mathematical than Angrist & Pischke, more visual.

**Where it is hardest.** The later chapters on panel data assume familiarity with econometric notation. Skip to the relevant sections using the course day callouts below.

**Course days that rely on it most.** Days 14–15 (DAGs, identification). Chapter 6 (Causal Diagrams) is the primary reference for Day 14. Chapter 8 (Identification) maps exactly to Day 15.

**Recommended entry point.** Chapter 5 (Identification) — read after Day 13.

---

### 5. Pearl, J. & Mackenzie, D. (2018). *The Book of Why: The New Science of Cause and Effect*. Basic Books.

**What it covers.** The non-technical case for causal reasoning over correlational thinking. Introduces the Ladder of Causation (association → intervention → counterfactual), DAGs, do-calculus, and counterfactuals through narrative.

**Whose intuition it favours.** General readers and anyone who prefers stories to equations. This is not a technical reference — it is an intuition-builder.

**Where it is hardest.** Chapter 8 (Counterfactuals) is where the narrative shifts towards formalism. Stop there if you want intuition only.

**Course days that rely on it most.** Intuition primer for Days 13–15. Read Chapter 1 (The Ladder of Causation) before Day 1 or alongside it.

**Recommended entry point.** Chapter 1 (The Ladder of Causation) — read before or on Day 1.

---

### 6. Charan, A. *The Marketing Analytics Practitioner's Guide*. [Publisher / Year — VERIFY]

**What it covers.** Marketing analytics with an FMCG practitioner lens — demand forecasting, price elasticity, distribution analytics, promotional effectiveness, and marketing mix modelling. Grounded in real-world data from consumer goods contexts.

**Whose intuition it favours.** FMCG marketers and brand managers. Charan writes from the brand P&L perspective rather than the econometric one — which makes his framing ideal for understanding what the CMO actually needs from a model.

**Where it is hardest.** The MMM chapters assume some familiarity with regression. An ML practitioner will find them straightforward; use them for the business framing, not the technical derivations.

**Course days that rely on it most.** Days 6–11 (5Ps as drivers) draw on Charan's practitioner framing of each lever. Days 25–29 (business decision modules) use his decision frameworks as the bridge between model output and recommendation.

**Recommended entry point.** The 5Ps or marketing mix chapter — read alongside Day 6.

---

## Landmark Papers

### 7. Jin, Y., Wang, Y., Sun, Y., Chan, D., & Koehler, J. (2017). "Bayesian Methods for Media Mix Modeling with Carryover and Shape Effects." Google Inc. Technical Report.

**What it covers.** Defines the two core MMM transformations used in this course: the geometric decay adstock (carryover) and the Hill function (shape/saturation). Derives Bayesian estimation via Stan. The paper is the technical foundation for Google's internal MMM tooling and the ancestor of both Robyn and PyMC-Marketing.

**Course days.** Primary reference for Days 4–5 (adstock, saturation) and Days 20–21 (Bayesian priors, Python build).

---

### 8. Chan, D. & Perry, M. (2017). "Challenges and Opportunities in Media Mix Modeling." Google Inc. Technical Report. [VERIFY — search Google Scholar]

**What it covers.** An honest assessment of what MMM can and cannot do: multicollinearity, identification problems, measurement error, and the role of experiments in calibrating models. Read this paper when you want to understand why the methodology has critics.

**Course days.** Primary reference for Days 1, 13, 22.

---

### 9. Robyn Team, Meta (2021). "Robyn: An Open-Source Semi-Automated and Augmented Model Building Application for Improved Media Mix Modeling." Meta Open Source. [github.com/facebookexperimental/Robyn](https://github.com/facebookexperimental/Robyn)

**What it covers.** Meta's open-source MMM framework: Nevergrad-based hyperparameter optimisation, Pareto-front model selection, budget allocator, and response curves. The architecture shares conceptual structure with PyMC-Marketing.

**Course days.** Reference architecture for Day 21 pipeline design. The Robyn documentation is the best plain-English explanation of response curve construction.

---

### 10. PyMC Labs (2022). *PyMC-Marketing: Bayesian Media Mix Modeling & CLV in Python*. [github.com/pymc-labs/pymc-marketing](https://github.com/pymc-labs/pymc-marketing)

**What it covers.** Python library for Bayesian MMM using PyMC. Implements geometric and weibull adstock, Hill saturation, channel contribution decomposition, budget optimisation, and posterior predictive checks. All Python code in this course targets this library.

**Course days.** Primary implementation reference for Days 21–22.

---

### 11. Brodersen, K.H., Gallusser, F., Koehler, J., Remy, N., & Scott, S.L. (2015). "Inferring causal impact using Bayesian structural time-series models." *Annals of Applied Statistics*, 9(1), 247–274.

**What it covers.** The CausalImpact methodology: using a Bayesian structural time-series model on control series to estimate the counterfactual for a treated series. An alternative to DiD when a clean parallel-trends control geography is unavailable.

**Course days.** Supplement for Day 16. Use CausalImpact when the DiD parallel-trends assumption cannot be defended.

---

## Video Lectures / Courses

### 12. McElreath, R. (2023). *Statistical Rethinking* (2nd ed. lecture series). YouTube: @rmcelreath.

**What it covers.** A full Bayesian data analysis course taught with extraordinary pedagogical care. Lectures 1–4 cover the Bayesian workflow (priors, likelihood, posterior). Lectures 9–11 cover model comparison, posterior prediction, and MCMC.

**Course days.** Primary intuition source for Day 20. Lecture 2 (Garden of Forking Data) is the single best introduction to what a prior distribution actually means.

**Entry point.** Lecture 1 (Golem of Prague) — watch before or after Day 20.

---

### 13. Neal, B. (2020). *Introduction to Causal Inference* (video course). [bradyneal.com/causal-inference-course](https://www.bradyneal.com/causal-inference-course)

**What it covers.** A graduate-level causal inference course designed for ML practitioners. Lectures 1–4 cover potential outcomes, DAGs, identification, and backdoor criterion. Free, rigorous, the right audience for this course.

**Course days.** Spine for Days 14–15. Lecture 3 (The do-calculus and graphical criteria) maps directly to Day 14 DAG content.

**Entry point.** Lecture 1 — watch after Day 13.

---

### 14. Huntington-Klein, N. (2021). *Econometrics* lecture series. YouTube: @NickHuntington-Klein.

**What it covers.** Video companions to *The Effect*. The DiD and IV episodes add worked examples in R that translate to Python and anchor the abstract estimation logic in concrete datasets.

**Course days.** Supplement for Days 16–17.

**Entry point.** DiD episode — watch after Day 16.

---

## Optional Deeper Dives

- **Varian, H.R. (2016).** "Causal inference in economics and marketing." *PNAS*, 113(27), 7310–7315. — A 5-page bridge between economics causal thinking and ML. Read after Day 17.

- **Google Meridian (2024).** opensource.google/projects/meridian — Google's successor to the Jin et al. (2017) framework. Production-grade Bayesian MMM with hierarchical priors. Read after Day 22 as a reference architecture.

- **Rubin, D.B. (1974).** "Estimating causal effects of treatments in randomized and nonrandomized studies." *Journal of Educational Psychology*, 66(5), 688–701. — The foundational potential outcomes paper. Read after Day 15 if you want the formal Rubin Causal Model framework.

- **Imbens, G.W. & Angrist, J.D. (1994).** "Identification and estimation of local average treatment effects." *Econometrica*, 62(2), 467–475. — The LATE theorem: IV estimates the effect only for compliers, not the whole population. Read after Day 17 when you ask "what does my IV estimate actually mean?"
