# Day 9 — The Empathy Map

> **Today's one idea:** An empathy map synthesizes what a user says, does, thinks, and feels into one shared team artifact — turning scattered observations into a human picture.
> **Reading time:** ~38 min · **Prereqs:** Days 6–8
> **Primary source for today:** Stanford d.school, *Design Thinking Bootcamp Bootleg*, 2018, pp. 18–22 ("Empathy Map" template and instructions). Free PDF at dschool.stanford.edu/resources.
> **Before you start:** Recall Day 8's load-bearing idea — one sentence, no looking. *What does observation reveal that interviews cannot — name one of the three things.*

---

## The hook

You've done three interviews and two observation sessions. You have 14 pages of notes, 40 sticky notes on a wall, six quotes that feel important, and three video clips you haven't reviewed yet. Your teammate has their own set of notes. Another teammate couldn't make the sessions and only has the summaries you've shared.

You need to design something. Together. Starting now.

The problem: the raw empathy data exists in seven different formats, spread across three people's heads, and nobody has a shared picture of the user yet. You can't build a POV statement on top of scattered notes — you need a common artifact that the whole team can look at and reason from.

This is what the empathy map solves.

---

## Building the intuition

An empathy map is a one-page visual that organizes what you observed about a specific user into four quadrants — **Says, Does, Thinks, Feels** — plus two synthesis zones: **Pains** and **Gains**.

Think of it as a translation layer: it takes your raw observation data (quotes, behavioral notes, AEIOU observations) and converts it into a shared human picture that every team member can hold in their head.

Here's why the four-quadrant structure matters:

**Says** (outer, visible) — what the user expressed verbally. Direct quotes belong here. "I export to CSV because it's easier." These are above-the-waterline data — the stated preferences and reported behaviors from Day 7's interviews.

**Does** (outer, visible) — what the user physically did during observation. Actions, behaviors, workarounds. "User opened a second spreadsheet tab every time they started a new report." This is Day 8's observational data.

**Thinks** (inner, inferred) — what the user might be thinking but didn't say. You infer this from facial expressions, hesitations, body language. "Why doesn't this tool remember my settings?" These are hypotheses, not certainties — mark them as such.

**Feels** (inner, inferred) — the emotional undercurrent. "Anxious about getting the numbers wrong." Again, inferred from behavioral signals, not directly reported — users rarely volunteer emotional labels.

The power of the map comes from the quadrant *boundaries*:

```
┌──────────────────┬──────────────────┐
│     THINKS       │     FEELS        │
│  (inner world,   │  (inner world,   │
│   inferred)      │   inferred)      │
├──────────────────┼──────────────────┤
│      SAYS        │      DOES        │
│  (outer world,   │  (outer world,   │
│   observable)    │   observable)    │
└──────────────────┴──────────────────┘
              [USER in center]
         ─────────────────────────
              PAINS (bottom)
              GAINS (bottom)
```

The most valuable data lives in the **gaps**:
- **Says one thing, Does another** → Day 8's gold signal: a workaround, an adaptation, a behavior the user doesn't consciously report
- **Says one thing, Feels the opposite** → a rationalization covering an emotional reality (the ATM user who says "it's fine" but shields their body defensively)

---

## The formal picture

**How to build an empathy map (the process):**

1. **Pick one user archetype** — or one specific user from your research, if you interviewed 3–5 individuals. One empathy map = one human. If you have multiple distinct user types, build one map per type.

2. **Dump raw data** — working with your team, take all your notes and quotes and place each sticky note in the relevant quadrant. Quotes go in Says; behavioral observations go in Does; inferences go in Thinks and Feels.

3. **Look for gaps** — actively search for contradictions between quadrants. Mark them.

4. **Synthesize Pains and Gains** — at the bottom of the map, write 3–5 pains (frustrations, obstacles, fears) and 3–5 gains (goals, wants, measures of success) *derived from* the four quadrants. These are not new observations — they are synthesis from what you've already placed.

5. **Draft an insight** — the empathy map doesn't automatically generate an insight, but it creates the conditions for one. After filling in all quadrants, ask: "What is the most surprising thing we've learned about this person?" The answer to that question is usually the direction of your insight (Day 12).

**What a filled empathy map reveals that raw notes don't:**

| Raw notes | Empathy map |
|-----------|-------------|
| Linear, time-ordered | Spatial, relationship-visible |
| Individual (my notes, your notes) | Shared (team looks at same artifact) |
| Hard to spot patterns | Gaps and contradictions are visible |
| Includes everything | Forces prioritization |

**Common mistakes:**

- **Putting solutions in the map.** "User needs a better export button" belongs in Ideate, not in an empathy map. The map is observation and inference only.
- **Skipping Thinks and Feels.** These are the hardest quadrants because they require inference — but they contain the most important data. If both inner quadrants are empty, your empathy research was too surface-level.
- **Building one map for "all users."** A composite "average user" empathy map is almost always wrong. Build one per distinct user type; the differences between maps are as important as what's in each one.

---

## Where it breaks / what it is not

**An empathy map is a hypothesis document, not a validated truth.** The Thinks and Feels quadrants especially are inferences — educated guesses based on observation. They can be wrong. The map is a tool for building a shared starting hypothesis about the user, not a final portrait. Validate it by going back to users.

**An empathy map is not a persona.** A persona is a fictional composite character with a name, age, and backstory — it's a communication tool. An empathy map is a synthesis tool — it organizes raw research data into a structure for reasoning. The two are often used together: empathy maps feed the creation of personas. Don't conflate them.

**An empathy map without real research data is fiction.** The quadrants should contain observations and quotes, not what you imagine a user might say or do. If your Says quadrant is full of things you invented without talking to anyone, the map is a projection of your own assumptions — the opposite of empathy.

---

## Try it yourself

> **Close this page before attempting Exercise 1.**

**Exercise 1 — Retrieval.** Without looking: name the four quadrants of an empathy map and state which two are "outer world" (observable) and which two are "inner world" (inferred). Then name the two synthesis zones at the bottom.

<details>
<summary>Compare to this</summary>

Four quadrants: **Says** (outer), **Does** (outer), **Thinks** (inner), **Feels** (inner). The two synthesis zones: **Pains** and **Gains**. If you remembered the quadrants but forgot the outer/inner distinction, re-read "Building the intuition" — that distinction is the whole reason the map surfaces gaps between what users say and what they do.
</details>

---

**Exercise 2 — Direct application.** Take the nurse from Days 7 and 8 (or any user you have direct knowledge of from your own work). Fill in one sticky note for each of the four quadrants — four actual observations or inferences, one per quadrant. Be specific. Then: do any two quadrants contradict each other? Name the contradiction if yes.

<details>
<summary>A strong example (for the nurse scenario)</summary>

**Says:** "The handoff process is fine — I just double-check the chart." *(direct quote)*
**Does:** Spends 4 minutes cross-referencing the paper chart with the electronic record before every medication round. *(observed behavior)*
**Thinks:** "I don't trust the electronic record to be current." *(inferred from the cross-referencing behavior)*
**Feels:** Low-grade anxiety about making a medication error, especially at shift change. *(inferred from the defensive behavior and the word "double-check")*

**Contradiction:** Says "the process is fine" but Does a time-consuming manual cross-check that only makes sense if you don't trust the primary system. This is the gap — and it points toward the real problem: trust in the electronic record's currency, not the handoff format itself.
</details>

---

**Exercise 3 — Stretch (spaced callback from Day 5).** Day 5 described wicked problems as having no definitive formulation. How does the contradiction between Says and Does in an empathy map relate to the wicked nature of a problem? Write two sentences.

<details>
<summary>The connection</summary>

The Says/Does gap reveals that users have adapted to the problem in ways that make the problem invisible to them — which means the problem cannot be surfaced by asking users to define it. This is exactly what makes the underlying problem wicked: the users living inside it cannot articulate its formulation, because their adaptations have made it feel like "the way things are." The empathy map makes the wicked problem visible by externalizing the gap between stated and observed reality.
</details>

---

**Transfer — apply it:**

> Pick a user of your product. Based on what you already know (from support tickets, product analytics, or past conversations): place one observation in each of the four empathy map quadrants. Note which quadrant you found hardest to fill — that's the gap in your current empathy research.

---

## Connect it back

Today's empathy map is the first synthesis tool of the course — the first time you convert raw data into a shared, structured artifact. This synthesis skill will be formalized on Day 12 (From Data to Insight) and drilled on Day 15. For now, the empathy map is your bridge from the Empathize phase to the Define phase.

Tomorrow you add the second synthesis artifact: the journey map, which extends the empathy map across time. Where the empathy map asks "who is this person?", the journey map asks "what happens to this person across their whole experience?"

**Sharp question you should be able to answer now:** What is the most valuable thing you can find in an empathy map that raw interview notes alone won't show you?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Stanford d.school, *Design Thinking Bootcamp Bootleg* (2018), pp. 18–22. The d.school empathy map template and instructions. Print or screenshot the template — you will use it on Days 15 and 28.

**Free video — watch today:**
NNgroup, *"Empathy Mapping: A Guide to Getting Inside a User's Head"* — NNgroup YouTube channel. Search YouTube: `NNgroup empathy mapping guide`. ~6 min. Clear, step-by-step walkthrough with a real example. Directly complements today's page.

**Free template:**
Download the d.school Bootcamp Bootleg PDF for a printable empathy map template. Alternative: NNgroup's free empathy map template — search `NNgroup empathy map template` — available as a free PDF download from their website.

**If you want the deep version:**
Gibbons, Sarah. "Empathy Mapping: The First Step in Design Thinking." Nielsen Norman Group, 2018. Search NNgroup.com for "empathy mapping." This is a long-form article (not a video) that covers all four quadrants with real-world product examples, common pitfalls, and the relationship to persona development. Reading time: ~20 additional minutes.

---

## Navigation

← **Previous:** [Day 8 — Observation in the Field](./day-08-observation-in-the-field.md)
→ **Next:** [Day 10 — Journey Mapping](./day-10-journey-mapping.md)
