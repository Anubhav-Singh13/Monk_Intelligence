# Day 21 — What to Prototype

> **Today's one idea:** Prototype the riskiest assumption in your concept — not the whole solution, and not the safest part.
> **Reading time:** ~38 min · **Prereqs:** Days 19–20
> **Primary source for today:** Knapp, Jake, John Zeratsky, and Braden Kowitz. *Sprint.* Simon & Schuster, 2016. Chapter 11, "Friday: Test," pp. 225–249 (specifically the "critical path" and "assumption mapping" sections).
> **Before you start:** Recall Day 20's load-bearing idea — one sentence, no looking. *What are the three low-fidelity prototype formats — name each and state what it is best suited to test.*

---

## The hook *(spaced callback to Day 16 — brainstorming done right)*

A team at a fintech startup had a concept: a savings app that automatically rounds up every purchase to the nearest dollar and invests the difference. The concept had three components:
1. Bank account integration (to detect purchases)
2. Rounding-up logic (to calculate the investment amount)
3. A notification system (to tell the user what just happened)

The team spent their first two weeks building a beautiful notification system. Polished push notifications. Custom animations. A delightful confirmation screen.

Then they tested with users.

The users never got to the notification screen. They abandoned at step one: connecting their bank account. The act of handing over banking credentials to an unknown startup felt too risky. Users left before any of the three components mattered.

Two weeks of polished notification design, made worthless by an untested assumption about bank account trust.

The assumption the team should have tested first: **will users trust this app enough to connect their bank account?** That was the riskiest assumption — the one that, if false, invalidated everything else. But it was also the scariest one to test, so the team built the comfortable parts first.

This is the failure mode today is designed to prevent.

---

## Building the intuition

Every concept has a stack of assumptions underneath it. Some assumptions are low-risk (if they're wrong, you can fix the detail without changing the concept). Some assumptions are high-risk (if they're wrong, the concept fails entirely).

The high-risk assumptions are the ones you must test first — even though they are the most uncomfortable to test, because a negative result would mean discarding the work you've already done.

Here is a simple framework for identifying which assumption to prototype:

**Step 1: List all the assumptions your concept depends on.**

For the round-up savings app:
- Users will trust the app enough to connect their bank account ← **trust assumption**
- Users will feel positively about automatic small investments without explicit approval ← **autonomy assumption**
- Users will notice and value the notifications ← **engagement assumption**
- The rounding-up logic will feel meaningful (not too small to matter) ← **magnitude assumption**

**Step 2: Score each assumption on two dimensions:**

| Assumption | Importance (if wrong, concept fails?) | Evidence (how confident are you?) |
|-----------|--------------------------------------|----------------------------------|
| Trust (bank connection) | High — no connection = no product | Low — never tested with users |
| Autonomy (auto-invest) | Medium — could add approval step | Medium — some analogues exist |
| Engagement (notifications) | Low — product works without them | High — standard mobile behavior |
| Magnitude (amounts feel meaningful) | Medium — affects retention | Low — no research done |

**Step 3: Prototype the assumption in the top-left quadrant: high importance + low evidence.**

That is the trust assumption. The minimum test: show users a mockup of the bank connection screen and ask "would you connect your account here?" — and watch the reaction, not just the answer. Better test: a Wizard of Oz prototype where users actually go through a fake onboarding and you observe at the bank-connection step.

The matrix forces the uncomfortable question: *what could most easily kill this concept, and have we tested it?*

---

## The formal picture

**Assumption mapping — the full process:**

```mermaid
quadrantChart
    title Assumption Map — What to Prototype First
    x-axis Low Evidence --> High Evidence
    y-axis Low Importance --> High Importance
    quadrant-1 Test first (high risk, low evidence)
    quadrant-2 Monitor (high risk, but evidence exists)
    quadrant-3 Ignore for now (low risk, low evidence)
    quadrant-4 Safe to build (low risk, evidence exists)
    Bank trust: [0.2, 0.95]
    Autonomy: [0.5, 0.6]
    Notifications: [0.85, 0.3]
    Magnitude: [0.25, 0.55]
```

The top-left quadrant (high importance, low evidence) is where you build your first prototype. Everything in the bottom-right quadrant (low importance, high evidence) can be built with confidence — no prototype needed.

**What "riskiest assumption" means in practice:**

The riskiest assumption is not necessarily the most technically complex. It is the one that, if false, makes the rest of the prototype irrelevant. Ask this question:

> *"If a user fails at this moment in the experience, does everything else we've built become worthless?"*

If yes: that moment is your riskiest assumption. Build the prototype to test that moment first.

**Three types of assumptions and how to test each:**

| Assumption type | What it claims | How to test it cheaply |
|----------------|---------------|----------------------|
| **Desirability** | Users want this at all | Concierge test (do it manually for 5 users), landing page with signup, paper prototype reaction |
| **Usability** | Users can figure out how to use it | Paper prototype with think-aloud, 5-user usability session |
| **Feasibility** | This can actually be built | Technical spike (engineering exploration), not a DT prototype — this is outside DT's scope |

DT prototypes primarily test desirability and usability. Feasibility is an engineering question — valid, but not what a prototype session answers.

**The "critical path" principle from *Sprint*:**

Knapp introduces the concept of the "critical path" — the sequence of steps a user must take for the concept to deliver its core value. The critical path is usually 3–5 steps. Each step is a potential failure point. The first failure on the critical path is the one that prevents all subsequent steps from being reached.

Prototype the first failure point on the critical path. In the round-up app: the critical path is *connect account → first round-up happens → user sees notification → user trusts the process*. The first failure point is "connect account." That is what to prototype.

---

## Where it breaks / what it is not

**"Let's test everything" means testing nothing rigorously.** A prototype session where you are simultaneously testing desirability, usability, and emotional response will give you superficial data on all three. Pick the one assumption that matters most and design the session around testing only that.

**Riskiest ≠ hardest to build.** Teams often prototype the technically complex parts first because engineers are more comfortable working on hard engineering problems than on "will users want this?" questions. The desirability assumption is often the riskiest — and the cheapest to test — but it gets deferred in favor of the technical spike.

**Your assumptions change as you learn.** After the first prototype session, the trust assumption may be resolved. The new riskiest assumption is now the autonomy assumption (will users accept automatic investing without approval?). Rebuild the assumption map after each test. What is riskiest changes as evidence accumulates.

---

## Try it yourself

> **Close this page before attempting Exercise 1.**

**Exercise 1 — Retrieval.** Without looking: describe the two-step assumption mapping framework. What are the two dimensions you score assumptions on, and what does the top-left quadrant on the matrix tell you?

<details>
<summary>Compare to this</summary>

**Two dimensions:** (1) Importance — if this assumption is false, does the concept fail entirely? (2) Evidence — how confident are you that this assumption is true, based on existing research? **Top-left quadrant:** high importance + low evidence = the assumption you must test first. This is where the riskiest assumption lives — the one that, if false, makes everything else you've built irrelevant.
</details>

---

**Exercise 2 — Direct application.** Here is a concept: *"A mobile app that helps freelancers set aside money for taxes automatically — it analyzes each incoming payment and moves a calculated percentage to a separate 'tax bucket' account."*

List four assumptions the concept depends on. Score each on importance (H/M/L) and evidence (H/M/L). Identify which one to prototype first and describe in two sentences the cheapest test you would run.

<details>
<summary>A strong answer</summary>

| Assumption | Importance | Evidence |
|-----------|-----------|---------|
| Freelancers will trust a third-party app to move money automatically from their account | H | L — untested; fintech trust is fragile |
| The automatic percentage calculation will feel accurate enough for users to rely on it | H | M — tax rate complexity is known; accuracy feels uncertain |
| Freelancers will check the "tax bucket" balance frequently enough to feel in control | M | L — unknown engagement behavior |
| The app integration with existing bank accounts is technically feasible | M | M — similar products exist |

**Prototype first:** Trust assumption (H importance, L evidence). **Cheapest test:** Build a one-page landing page that describes the concept and has a single CTA — "Connect your bank account to get started." Measure how many freelancers click it vs. abandon. Better: show 5 freelancers a paper prototype of the bank-connection screen and ask them to think aloud as they decide whether to connect. Observe hesitation and language — the specific language they use about "safety" or "risk" is the data.
</details>

---

**Exercise 3 — Stretch.** Return to the round-up savings app from the hook. After the team runs a test and confirms that users *will* connect their bank account (the trust assumption is resolved), what is the *new* riskiest assumption — and what prototype would you build next?

<details>
<summary>The next assumption</summary>

With trust confirmed, the new riskiest assumption from the map is the **autonomy assumption**: will users accept automatic micro-investments without explicit per-transaction approval? This is high importance (if users want to approve every investment, the "automatic" core of the product breaks) and still medium-low evidence. **Next prototype:** A Wizard of Oz test — manually run the app for 5 freelancers for one week. Send them a manual "we've invested $X from your round-ups today" notification (a Slack message or email, not a real transfer). Observe: do they ask to override it? Do they feel anxious or relieved? Do they check what was invested? This tests the autonomy assumption with zero engineering commitment.
</details>

---

**Transfer — apply it:**

> Name one concept your team is currently planning to build. List its three most important assumptions. Score each on importance and evidence. Which is in the top-left quadrant — and have you tested it yet?

---

## Connect it back

Module 05 is complete. Three days have given you the prototype mindset (Day 19), the low-fidelity methods (Day 20), and the judgment of what to test (Day 21). Together these form the Prototype phase: build cheaply, test the riskiest thing, learn fast.

Tomorrow is Rest & Synthesize II — a full retrieval session covering the Define, Ideate, and Prototype arcs (Days 12–21) before entering the Test and Iterate phase.

**Sharp question you should be able to answer now:** A concept has five testable assumptions. You only have time to prototype one this week. How do you decide which one — and what question do you ask to identify it?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Knapp et al., *Sprint* (Simon & Schuster, 2016), Chapter 11, pp. 225–249. Knapp's "critical path" concept and his specific advice on identifying what to test on Friday of a Sprint week. The "assumption stacking" discussion (pp. 236–242) maps directly onto today's assumption mapping framework.

**Free video — watch today:**
AJ&Smart, *"How to Choose What to Prototype in a Design Sprint"* — Search YouTube: `AJ Smart what to prototype design sprint`. ~8 min. AJ&Smart's practical product-team framing of the "prototype the riskiest thing" principle. Very applicable to today's content.

**Free video — companion:**
Strategyzer, *"Testing Business Ideas"* — Search YouTube: `Strategyzer testing business ideas assumptions`. ~6 min. Strategyzer's treatment of assumption testing from a business model perspective — reinforces today's framework from a slightly different angle and is directly applicable to product development contexts.

**If you want the deep version:**
Bland, David J. and Alexander Osterwalder. *Testing Business Ideas.* Wiley, 2019. Chapter 2 ("Assumption Mapping") and Chapter 4 ("Designing Experiments"). The most rigorous treatment of assumption-to-experiment design available at the practitioner level. Reading time for two chapters: ~50 additional minutes. Flag for after Day 28 if assumption mapping resonated strongly.

---

## Navigation

← **Previous:** [Day 20 — Low-Fidelity Prototyping](./day-20-low-fidelity-prototyping.md)
→ **Next:** [Day 22 — Rest & Synthesize II](../../06-test-and-iterate/days/day-22-rest-and-synthesize-2.md)
