# Day 23 — User Testing Fundamentals

> **Today's one idea:** A user test is an experiment with a hypothesis — not a presentation of your prototype to collect compliments.
> **Reading time:** ~38 min · **Prereqs:** Days 3, 20–21
> **Primary source for today:** Knapp, Jake, John Zeratsky, and Braden Kowitz. *Sprint.* Simon & Schuster, 2016. Chapter 12, "Friday: Test," pp. 225–252.
> **Before you start:** Recall Day 21's load-bearing idea — one sentence, no looking. *What is the riskiest assumption, and how do you identify it before building a prototype?*

---

## The hook *(spaced callback to Day 19 — what is a prototype)*

There is a failure mode that is more expensive than building the wrong thing. It is testing the wrong thing and concluding you were right.

In the early days of Google Glass, the company ran user feedback sessions. Users tried the device, spoke into it, laughed at what they could do. Feedback was broadly positive. The product team concluded: people like this.

What they failed to test: how users felt wearing the device in public, over time, when other people could see them. The sessions were conducted in a controlled room, with early adopters self-selected for enthusiasm, for 20 minutes each. The actual failure mode — social stigma, the nickname "Glasshole," the feeling that wearing it made you look like you were filming people — was never tested in the conditions where it would actually occur.

The tests were well-run. They tested the wrong thing.

A test is only as good as its hypothesis and its conditions. Both require deliberate design.

---

## Building the intuition

A user test is an experiment. It has:
- A **hypothesis** — what you believe will happen
- **Conditions** — who you test, in what context, with what task
- **Observations** — what actually happens
- **A conclusion** — whether the hypothesis was confirmed, denied, or complicated

Most team "user tests" are missing the first item. Without a hypothesis, you are running a show-and-tell. Users will react to what's in front of them, say polite things, suggest features they might like. None of that is what you need.

With a hypothesis, the test has a question to answer. The user's behavior is data, not feedback.

**Writing a testable hypothesis:**

Format: *"We believe [user type] will [specific action/reaction] when presented with [prototype feature/flow] because [insight from research]."*

Examples:

| Weak (no hypothesis) | Strong (testable hypothesis) |
|---------------------|------------------------------|
| "Let's see what users think of the new dashboard" | "We believe power users will scan the dashboard for their top project first and feel frustrated if it requires scrolling, because our research shows they monitor 3 primary projects and expect them above the fold" |
| "Let's get feedback on the onboarding flow" | "We believe new users will abandon the 'connect your calendar' step because it feels optional and the value of connecting it isn't visible until after the connection is made" |

The hypothesis specifies a user, an action, a feature, and the research-grounded reason. When the test ends, you can say definitively: confirmed, denied, or needs more data.

**Five-user rule:**

Nielsen's research on usability testing: five users reveal approximately 85% of usability issues. The same principle applies to DT concept tests. You do not need 50 users to learn whether your concept resonates — you need 5 users who match your target archetype, interviewed one at a time, with a consistent protocol.

Why one at a time: in group sessions (focus groups), social dynamics suppress honest reactions. Users modify their responses based on what others say. Individual sessions remove this effect.

---

## The formal picture

**A DT user test session — structure (60 min):**

| Phase | Duration | What happens |
|-------|----------|-------------|
| **Welcome and brief** | 5 min | Explain the session: "We're testing the idea, not you. There are no right answers. Please think aloud." |
| **Background questions** | 5 min | 2–3 questions about the user's context and current behavior — this is mini-empathy research embedded in the test session |
| **Task execution** | 30–35 min | User attempts 2–3 tasks with the prototype; you observe without helping |
| **Debrief** | 10 min | Open questions: "What surprised you? What would you change? Would you use this in real life?" |
| **Note consolidation** | 5 min | (After user leaves) immediately write your top 3 observations while fresh |

**The facilitator's rules during task execution:**

```
DO:                                     DON'T:
────────────────────────────────────    ────────────────────────────────────
Ask "What are you thinking right now?"  Explain how the prototype works
Remain silent when user is stuck        Tell the user they did something wrong
Note hesitation and exact words         Defend your design choices
Ask "What did you expect to happen?"    Ask "Did you like it?"
Let the user fail — that's the data     Jump in to rescue them
```

The most important rule: **let users fail**. When a user gets stuck, the natural human instinct is to help. Resist it. The stuck moment is where the learning lives. The question "why did you stop here?" is more valuable than any feature you could add.

**The note-taking role:**

In a test session with two team members present: one facilitates (talks to the user), one takes notes (watches and writes). Notes should be behavioral observations — what the user did, what they said verbatim (in quotes), where they hesitated. Not interpretations.

Good note: *"User clicked the 'Archive' button twice, then said 'I'm not sure what that did.'"*
Bad note: *"User was confused by the Archive function."*

The good note is a fact. The bad note is an interpretation. Interpretations belong in synthesis, not in raw notes.

---

## Where it breaks / what it is not

**"Users said they liked it" is not a confirmed hypothesis.** Self-reported liking in a test session is the least reliable data you will collect. It is subject to social desirability bias (users don't want to disappoint you), interviewer effect (the person who built it is in the room), and optimism about future behavior. The most reliable signal: what did users *do*, not what did they say they liked.

**Testing with the wrong users invalidates all results.** If your POV names a specific user archetype (charge nurses managing 16-bed wards during peak census) and you test with general nurses who have no charge nurse experience, your test is measuring the wrong thing. Recruitment is as important as the test protocol. One well-matched user is worth ten mismatched ones.

**Don't test with people who already know the concept.** Your own team, your product's power users who have been briefed, and enthusiastic early adopters who believe in your company are all biased test participants. You need users who encounter the prototype cold — the same way real users will.

**Five users is enough for one hypothesis.** If you have two distinct user archetypes or two competing concepts, test each with five separate users. Do not cross-test — a user who has seen Concept A will interpret Concept B differently. Separate sessions; separate insights.

---

## Try it yourself

> **Close this page before attempting Exercise 1.**

**Exercise 1 — Retrieval.** Without looking: what are the four components of a well-structured user test (hypothesis, conditions, observations, conclusion)? Then state the facilitator's single most important rule during task execution.

<details>
<summary>Compare to this</summary>

**Four components:** (1) Hypothesis — what you believe will happen, in specific terms; (2) Conditions — which users, in what context, with what task; (3) Observations — what actually happened, recorded as behavioral facts; (4) Conclusion — whether the hypothesis was confirmed, denied, or complicated. **Most important facilitator rule:** let users fail — the stuck moment is the learning. Do not rescue the user or explain the prototype during task execution.
</details>

---

**Exercise 2 — Direct application.** Write a complete test plan for this prototype: *a paper prototype of a "daily decision log" feature — a simple interface that lets PMs record decisions made in Slack, tag them with a project, and search them later.*

Your plan must include:
- Hypothesis (in the specified format)
- User recruitment criteria (2 sentences)
- One task to give the user
- Two observations you would specifically watch for
- One debrief question

<details>
<summary>A strong test plan</summary>

**Hypothesis:** "We believe PMs who manage 3+ active projects will try to use the search feature within the first 5 minutes of interacting with the prototype, because our research shows that the most acute pain is retrieval (finding a past decision), not entry."

**Recruitment:** Mid-level product managers at companies with 50+ employees, managing at least 3 simultaneous active projects. Must use Slack as their primary communication tool and have expressed frustration with finding past decisions. Exclude PMs who already use a formal decision log.

**Task:** "You've just been asked by your engineering lead about a decision made three weeks ago regarding the authentication flow. Show me what you'd do with this tool."

**Two observations to watch for:**
1. Does the user go to search immediately, or to the entry/tagging interface? (Tests the hypothesis about retrieval being the primary need)
2. Does the user attempt to tag the decision before or after writing it — or not at all? (Tests whether tagging feels natural or burdensome)

**Debrief question:** "After using this for a week, where do you think the decisions you care about most would end up — in this tool, or somewhere else? Why?"
</details>

---

**Exercise 3 — Stretch.** A user completes all tasks successfully and says: "I love this — I would definitely use it every day." A team member says: "That's confirmation — let's build it." As the DT practitioner, what three questions do you ask before agreeing?

<details>
<summary>The three questions</summary>

1. **"Did we test the right hypothesis?"** Successful task completion and positive self-report confirm usability and stated desirability — but do they confirm the riskiest assumption from Day 21's map? If the riskiest assumption was about trust (e.g., "will users trust a third party to log their private decisions?") and the test didn't specifically probe that, the confirmation is incomplete.

2. **"What did the user actually do vs. what did they say they'd do?"** "I would definitely use it every day" is a future-intention statement — the most unreliable data point in a test session. What tasks did they complete without help? Where did they hesitate? Those behavioral signals are more predictive of actual adoption than any self-report.

3. **"Who is this user, and does their profile match our POV?"** A single positive session from one well-matched user is a weak signal. The hypothesis needs confirmation from 4 more users with the same profile before it's robust enough to build from. One enthusiastic user could be an outlier, an early adopter, or someone who happens to match the use case perfectly but represents 5% of the actual market.
</details>

---

**Transfer — apply it:**

> Write a one-sentence hypothesis for the next user test your team should run — based on something you are currently uncertain about in your product. Use the format: "We believe [user] will [action] when presented with [feature/flow] because [insight]."

---

## Connect it back

Day 19 established that a prototype is a learning instrument. Day 23 gives it the structure that makes the learning possible: a hypothesis, controlled conditions, behavioral observation, and a conclusion. Tomorrow closes the loop — how do you take five sessions of raw observations and synthesize them into a decision the team can act on?

**Sharp question you should be able to answer now:** Why is "the user got stuck" more valuable data than "the user said they were confused" — and what question do you ask at the moment of stuckness?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Knapp et al., *Sprint* (Simon & Schuster, 2016), Chapter 12 "Friday: Test," pp. 225–252. The Sprint 5-user test protocol is the most actionable user testing framework available at L1. Knapp's "interview script" and "note-taking grid" (pp. 237–244) are directly usable in your first test session.

**Free video — watch today:**
NNgroup, *"Usability Testing 101"* — NNgroup YouTube channel. Search YouTube: `NNgroup usability testing 101`. ~8 min. The definitive short introduction to user testing from the organization that published the five-user research. Essential viewing before running your first session.

**Free video — companion:**
NNgroup, *"Think-Aloud: The #1 Usability Tool"* — NNgroup YouTube channel. Search YouTube: `NNgroup think aloud usability`. ~5 min. Specifically covers the think-aloud technique — how to prompt it, how to maintain it without leading the user, and what to do when users go silent.

**If you want the deep version:**
Krug, Steve. *Rocket Surgery Made Easy: The Do-It-Yourself Guide to Finding and Fixing Usability Problems.* New Riders, 2010. The most accessible and practical book on running your own user tests — Krug's "hallway testing" approach is directly applicable to the first-session context. Reading time for Ch. 1–4: ~60 additional minutes. Strongly recommended before Day 27 (mini-sprint planning).

---

## Navigation

← **Previous:** [Day 22 — Rest & Synthesize II](./day-22-rest-and-synthesize-2.md)
→ **Next:** [Day 24 — Capturing and Synthesizing Test Feedback](./day-24-capturing-test-feedback.md)
