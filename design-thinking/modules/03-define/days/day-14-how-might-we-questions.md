# Day 14 — How Might We Questions

> **Today's one idea:** A "How Might We" question is the bridge between a locked problem definition and open ideation — it must be wide enough to allow creative solutions but narrow enough to exclude irrelevant ones.
> **Reading time:** ~38 min · **Prereqs:** Days 12–13
> **Primary source for today:** IDEO.org, *The Field Guide to Human-Centered Design*, IDEO, 2015, pp. 72–75. Free download at designkit.org/resources/1.
> **Before you start:** Recall Day 13's load-bearing idea — one sentence, no looking. *What are the three components of a strong POV statement — name each one.*

---

## The hook *(spaced callback to Day 8 — observation in the field)*

In the 1970s, a Procter & Gamble brand manager named Min Basadur was running an ideation session that kept stalling. Every time the team brainstormed, they argued about the problem definition rather than generating ideas. "That's not the real issue." "We're solving the wrong thing."

Basadur's solution: reframe every problem statement as a question that started with "How might we..." The three words did something elegant:

- **"How"** signals that a solution is achievable — it is solvable in principle
- **"Might"** signals that the solution is not known yet — it invites exploration rather than confirming a foregone conclusion
- **"We"** signals that it is a collaborative challenge — not one person's problem or one team's job

The phrase doesn't solve the problem. But it removes the two biggest blockers to creative thinking: the belief that the problem is unsolvable, and the belief that someone else already knows the answer.

Thirty years later, the d.school and IDEO formalized this technique into the core bridge between Define and Ideate. The HMW question is now the standard output of the Define phase across virtually every DT framework.

---

## Building the intuition

A HMW question takes the POV statement and reframes it as an open design challenge. It takes the *problem* space and opens it to the *solution* space — but only partway. Too much opening → the question is too broad (anything goes). Too little → the question is too narrow (the solution is already implied).

The calibration of a HMW question is the key skill:

| HMW statement | Problem |
|---------------|---------|
| "How might we improve healthcare?" | Too broad — could generate millions of unrelated ideas, none of which are grounded in the specific need |
| "How might we redesign the nurse's medication tracking screen?" | Too narrow — already implies a solution (a screen redesign), constraining the ideation before it starts |
| "How might we help charge nurses trust the accuracy of the medication record during shift handoff?" | Right calibration — specific enough to focus ideation on a real need, broad enough to allow software, process, social, and physical solutions |

The right calibration means a skilled designer could generate 20 meaningfully different ideas from the HMW in 10 minutes — and every single one would be relevant to the user's stated need.

**Deriving a HMW from a POV:**

The mechanical transformation:

```
POV: [User] needs [need] because [insight]
           ↓
HMW: "How might we [help user] [achieve the need], 
      given [the insight as a design constraint]?"
```

In practice, you rarely include the "given" clause explicitly — you just keep the insight in mind as a constraint when evaluating ideas. The question itself focuses on the need:

| POV | → | HMW |
|-----|---|-----|
| "A charge nurse needs to trust that the medication record is current because the 3-min lag creates a hidden safety window" | → | "How might we help charge nurses feel confident that the medication record reflects the patient's current state?" |
| "A mid-level PM needs to maintain a reliable picture of what was actually decided because the informal Slack channel has displaced the official wiki" | → | "How might we give PMs a trustworthy, low-friction record of decisions made across informal channels?" |

**You should generate multiple HMW questions from one POV.** The single POV can be reframed multiple ways — each one opens a different part of the ideation space. After writing 5–7 HMW variants, select the 2–3 that feel most generative and take those into Ideate.

---

## The formal picture

**Five reframing moves for generating multiple HMW questions from one insight:**

| Move | Description | Example (from the nurse POV) |
|------|-------------|------------------------------|
| **Amplify the positive** | What if this went perfectly well? | "How might we make charge nurses feel completely confident about patient data at handoff?" |
| **Remove the negative** | What if the pain point didn't exist? | "How might we eliminate the safety window created by the record lag?" |
| **Challenge assumptions** | What if a core assumption were wrong? | "How might we design handoff as if the electronic record didn't need to exist?" |
| **Explore adjacents** | What adjacent need does this reveal? | "How might we help the whole ward team — not just charge nurses — maintain a shared real-time picture of patient status?" |
| **Change the actor** | Who else is affected? | "How might we help night-shift nurses leave handoff notes that charge nurses trust immediately?" |

Each reframing move generates a different flavor of HMW — and therefore a different flavor of ideation. "Remove the negative" tends to generate incremental improvements. "Challenge assumptions" tends to generate more disruptive concepts. Using all five gives you a portfolio of ideation directions.

**Validating a HMW before entering Ideate:**

Run the "20-ideas test" mentally: could a creative team generate 20 meaningfully different ideas from this question in 20 minutes? If yes, it's calibrated right. If you can only think of 3 ideas, it's too narrow. If the ideas have nothing in common, it's too broad.

---

## Where it breaks / what it is not

**A HMW is not a solution.** "How might we add a notification when the record is updated?" is already a solution framed as a HMW — it has constrained the ideation before it starts. A real HMW stays at the level of the need: "How might we help nurses know when the record is reliable?"

**A HMW is not a feature request.** Feature requests come from stakeholders. HMW questions come from user research. The difference: "Add dark mode" is a feature request. "How might we help developers use our tool comfortably in low-light environments?" is a HMW derived from an observed need.

**Multiple HMW questions is not the same as a weak POV.** A team that generates 7 different HMW questions from a single strong POV is doing exactly the right thing — each question opens a different design exploration. A team that has 7 different POVs (one per person) and generates one HMW from each is doing the wrong thing — each person is solving a different problem.

**The HMW question is revisable.** If Ideate generates no good ideas, the HMW may be miscalibrated. Return to Define, adjust the framing, and retry. This is the loop working correctly.

---

## Try it yourself

> **Close this page before attempting Exercise 1.**

**Exercise 1 — Retrieval.** Without looking: what do the three words "How," "Might," and "We" each contribute to the HMW question? One sentence per word.

<details>
<summary>Compare to this</summary>

**"How"** signals that a solution is achievable in principle — it frames the challenge as solvable. **"Might"** signals that the solution is not predetermined — it keeps the space open rather than confirming a known answer. **"We"** signals collaborative ownership — the problem belongs to the team, not to one person or one function.
</details>

---

**Exercise 2 — Direct application.** Take this POV statement and generate three HMW questions from it using three different reframing moves from the table above. Label which move you used for each.

**POV:** "A new employee in their first week at a remote-first company needs to quickly understand who to ask for what because there is no visible social graph — informal relationships and domain expertise are invisible until you happen to bump into someone in a meeting."

<details>
<summary>Three strong HMW questions</summary>

**(Amplify the positive):** "How might we help new remote employees feel immediately connected to the right people for their work — on day one?" — Opens ideation toward onboarding experiences that create human visibility, not org-chart tools.

**(Challenge assumptions):** "How might we design onboarding as if the new employee already knew everyone — and worked backward from that?" — Opens disruptive directions: pre-built relationship maps, proactive introductions from colleagues, AI-mediated "who to meet" recommendations.

**(Explore adjacents):** "How might we make domain expertise and informal relationships visible to the whole team, not just new employees?" — Shifts the scope to a broader knowledge-mapping problem — could generate a completely different product area.

Note: each of these opens a different ideation direction. Bringing all three to Day 16's brainstorming session would yield three distinct solution clusters.
</details>

---

**Exercise 3 — Stretch.** You have a HMW question: "How might we make our product easier to use?" Run the 20-ideas test: can a team generate 20 meaningfully different ideas from this in 20 minutes? If not (and it can't), diagnose exactly what is wrong with the calibration — too broad, too narrow, or missing the insight — and rewrite it as a HMW that would pass the 20-ideas test.

<details>
<summary>Diagnosis and rewrite</summary>

**What's wrong:** Too broad — "easier to use" is so vague it generates ideas across the entire product surface simultaneously. Twenty ideas would cover onboarding, navigation, documentation, error messages, loading times, and mobile responsiveness — none of them connected by a specific user need. The insight is entirely missing (why is it hard to use, for whom, at what moment?). This reads like a feature request framing, not a research-derived HMW.

**Rewritten (assuming you have empathy data showing the specific pain):** "How might we help first-time users understand what to do after they complete signup — before they give up and leave?" — Specific user (first-time users), specific moment (post-signup), specific need (understand the path forward). Now a team can generate 20 ideas (tooltips, interactive tours, a "what to do first" prompt, a pre-configured template, a short onboarding call, a progress tracker...) all grounded in the same need.
</details>

---

**Transfer — apply it:**

> Take the POV statement you drafted in Day 13's transfer exercise. Write two HMW questions from it using two different reframing moves. Then run the 20-ideas test on each — which one feels more generative?

---

## Connect it back

The Define phase is now fully covered. You have three tools in sequence: Insight (Day 12) → POV (Day 13) → HMW (Day 14). Together they form the bridge from observation to action — the machine that converts rich empathy data into a focused, researchable design challenge.

Tomorrow is the Drill Day — seven progressive exercises that force you to run this entire chain under exam conditions, cold, with raw data you haven't seen before. It will be uncomfortable. That discomfort is the sign the drill is working.

**Sharp question you should be able to answer now:** What is the test for whether a HMW question is calibrated correctly — not too broad, not too narrow?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
IDEO.org, *The Field Guide to Human-Centered Design* (2015), pp. 72–75 ("How Might We"). IDEO's own treatment of the HMW technique, with examples and the five reframing moves formalized slightly differently than today's page. Free PDF at designkit.org/resources/1. Cross-referencing IDEO's version with today's page reinforces the calibration intuition.

**Free video — watch today:**
NNgroup, *"How Might We Questions"* — Search YouTube: `NNgroup how might we questions`. ~5 min. Clear, example-driven short video on HMW calibration — directly reinforces today's "too broad / too narrow / right" spectrum.

**Free video — companion:**
AJ&Smart, *"How to Write a Good HMW Question"* — Search YouTube: `AJ Smart how might we question`. ~6 min. More product-team-focused framing; AJ&Smart's version of calibration is slightly different and the contrast is instructive.

**If you want the deep version:**
Brown, Tim, *Change by Design* (HarperBusiness, 2009), Chapter 4, "Building to Think" (the Ideate section, pp. 88–112). Brown's discussion of how IDEO frames design challenges before brainstorming is the richest narrative context for understanding what a well-formed HMW question does to a creative session. Reading time: ~35 additional minutes.

---

## Navigation

← **Previous:** [Day 13 — The Point of View Statement](./day-13-point-of-view-statement.md)
→ **Next:** [Day 15 — Drill Day: Synthesis](./day-15-drill-synthesis.md)
