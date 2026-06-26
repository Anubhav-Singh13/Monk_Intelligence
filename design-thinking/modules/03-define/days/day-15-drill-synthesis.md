# Day 15 — Drill Day: Synthesis

> **Today:** No new concepts. Seven progressive exercises drilling the synthesis chain: raw data → empathy map → insight → POV → HMW.
> **Format:** Gym session, not a lecture. Expect discomfort. That discomfort is the signal.
> **Reading time:** ~45 min · **Prereqs:** Days 9–14
> **Before you start:** Close everything. No notes, no previous pages open. Today measures what you can do without a safety net.

---

This is the drill day for the rate-limiting bottleneck of the course: **synthesis**. The seven exercises below progress from simple recall through full-chain execution on novel data. Each exercise is harder than the last. Do them in order — do not skip ahead.

The rule: attempt every exercise before opening the answer. If you open the answer first, you have turned a retrieval exercise into a reading exercise. The retrieval attempt — even an incomplete one — is where the learning happens.

---

## Exercise 1 — Chain recall (retrieval, no looking)

Write the full synthesis chain from memory. Five blanks, each pointing to the next:

```
[Raw observations from interviews and field sessions]
         ↓
[Blank 1: what you do with observations to find patterns]
         ↓
[Blank 2: what you produce when a pattern reframes the problem]
         ↓
[Blank 3: the team artifact that names one user + one need + one insight]
         ↓
[Blank 4: what you derive from that artifact to open ideation]
         ↓
[Blank 5: the phase you now enter]
```

Do not open the answer until you have filled all five from memory.

<details>
<summary>Filled chain</summary>

1. **Pattern recognition** (asking "why" until the root cause reframes the problem)
2. **Insight** (surprising, reframing, actionable)
3. **POV statement** ([User] needs [need] because [insight])
4. **HMW question** (open enough for 20 ideas, narrow enough to stay grounded in the need)
5. **Ideate** (diverge phase — generating many possible solutions to the HMW)
</details>

---

## Exercise 2 — Insight vs. data (discrimination)

Label each item below as **D** (data point) or **I** (insight). Attempt all six before checking.

1. "Users click the Help button an average of 3.2 times during onboarding."
2. "New users don't fail at the technical steps of onboarding — they fail at the moment they're asked to name their workspace, because naming implies commitment, and they haven't yet decided they trust the product."
3. "60% of users abandon during the payment step."
4. "Users who abandon at payment aren't blocked by the payment form — they're blocked by purchase doubt that the UX resolves too late, after the commitment moment rather than before it."
5. "Nurses check the paper chart before administering medication."
6. "Nurses maintain a parallel paper system not because the digital tool is hard to use, but because the tool's 3-minute update lag means the digital record can't be trusted as a source of truth at the point of care."

<details>
<summary>Answers</summary>

1. **D** — a metric; describes behavior without reframing the problem
2. **I** — surprising (the problem is naming/commitment, not technical complexity), reframes (shifts attention from UX steps to psychology of commitment), actionable (HMW: "how might we help new users commit to the product before they're asked to name it?")
3. **D** — a metric; describes where but not why
4. **I** — surprising (the form isn't the blocker, purchase doubt is), reframes (the problem is timing of reassurance, not form design), actionable
5. **D** — describes behavior, no reframe
6. **I** — surprising (ease of use isn't the issue, trust in data currency is), reframes (shifts from UX to data architecture and lag), actionable
</details>

---

## Exercise 3 — Weak POV diagnosis (recognition)

Each POV below has one specific flaw. Name the flaw and write one sentence explaining what is missing or wrong.

1. "Users need a better onboarding experience because onboarding is confusing."
2. "Enterprise customers need a Salesforce integration because they already use Salesforce."
3. "People who manage projects need to stay organized because projects are complex."

<details>
<summary>Diagnoses</summary>

1. **Flaw: the "because" is not an insight — it restates the symptom.** "Onboarding is confusing" is just "onboarding is confusing." The insight should explain *why* it is confusing — what root cause makes it so, and what that implies about the design direction. Fix: "because the tool asks users to make irreversible structural decisions (workspace naming, team invitations) before they have enough context to make them confidently."

2. **Flaw: the need is a solution.** "A Salesforce integration" is a specific technical solution, not a need. It has closed the design space before ideation begins. Fix the need to: "need to maintain a single source of truth for customer data without manual syncing between tools" — now the solution space is open (integration could be one answer; so could API access, export formats, or a native CRM feature).

3. **Flaw: the user is too generic and the insight is missing.** "People who manage projects" is an enormous, heterogeneous group. "Projects are complex" is a truism that could be written by anyone without research. Both the user and the insight need research specificity.
</details>

---

## Exercise 4 — HMW calibration (judgment)

Rate each HMW as **Too Broad (B)**, **Too Narrow (N)**, or **Well Calibrated (✓)**. Explain your rating in one sentence.

1. "How might we make healthcare better?"
2. "How might we add a 'pending transactions' label to the banking app's balance display?"
3. "How might we help small business owners trust their account balance enough to make daily financial decisions with confidence?"
4. "How might we make the login page load faster?"
5. "How might we help software engineers stay in flow during a workday that includes mandatory meetings?"

<details>
<summary>Ratings</summary>

1. **B** — "healthcare" and "better" are both undefined; generates ideas across the entire healthcare system with no shared direction.
2. **N** — This is a solution, not a question. The design space is already closed; ideation would only generate variations on this one solution.
3. **✓** — Specific user (small business owners), specific need (trust their balance for daily decisions), insight embedded (trust, not accuracy, is the real problem). Could generate 20+ meaningfully different ideas.
4. **N** — "Faster login page" is an engineering optimization task, not a HMW question worth an ideation session. No user insight is embedded.
5. **✓** — Specific user (software engineers), specific need (maintain flow), and the "mandatory meetings" phrase embeds the insight (external interruptions break flow). Generates ideas across scheduling, tooling, social norms, and physical environment.
</details>

---

## Exercise 5 — Full chain on novel data (direct application)

Here is a set of raw empathy data from research on how small NGO teams manage donor relationships. You have not seen this data before. Work through the full synthesis chain — without notes.

**Raw observations:**
- "The development director keeps a personal Excel spreadsheet with 'real' donor relationship notes — separate from the official CRM"
- "Said: 'The CRM is for grant reports. My spreadsheet is for actually knowing people.'"
- "When a major donor called, the director went to the spreadsheet, not the CRM, for context"
- "CRM entries are rarely updated in real time — usually entered in batches at month-end"
- "Said: 'I'm terrified of losing my spreadsheet. If I leave or get sick, nobody knows what I know.'"
- "Two junior staff members said they 'don't really use the CRM' — it feels 'too formal'"

**Your task — work through this sequence:**

1. What pattern do you see across these observations?
2. Write one insight statement (Liedtka format: "X does Y because Z")
3. Write a POV statement (d.school format: "[User] needs [need] because [insight]")
4. Write two HMW questions using two different reframing moves

Do all four from memory before checking.

<details>
<summary>Strong answers</summary>

**1. Pattern:** The CRM serves a formal, reporting-facing function, while the real relationship knowledge lives in an informal, personal system (the spreadsheet). The two systems serve two different audiences (grant reports vs. actual relationship management) and the practitioner doesn't trust the CRM to hold relational context.

**2. Insight:** "Development directors maintain personal relationship records outside the CRM because the CRM was designed for grant compliance reporting, not for the informal, contextual knowledge that actually drives donor relationships — and this creates a critical institutional knowledge gap when staff turn over."

**3. POV:** "A development director at a small NGO needs to maintain and share informal, relationship-level donor knowledge — not just contact records — because the CRM captures what donors gave, but not who they are, what they care about, or what was said at the last dinner, and this knowledge lives only in one person's head."

**4. HMW questions:**
- *(Remove the negative)* "How might we eliminate the risk that relationship knowledge disappears when the development director leaves?"
- *(Explore adjacents)* "How might we help the whole development team — not just the director — build and access the informal knowledge that drives major donor relationships?"

Note: the first HMW focuses on knowledge continuity; the second opens the scope to team-wide relationship intelligence. Both are calibrated correctly; each would generate different ideation directions.
</details>

---

## Exercise 6 — Says/Does gap identification (bottleneck drill)

For each pair below, identify the Says/Does gap and write the insight it points toward.

**Pair A:**
- Says: "I always check Slack in the morning to stay up to date."
- Does: Arrives at the office and spends 20 minutes in email before opening Slack; checks Slack only when someone messages them directly.

**Pair B:**
- Says: "The approval process is clear — I know what needs sign-off."
- Does: Sends informal Slack messages to the approver before officially submitting, to "pre-check" the request.

<details>
<summary>Insights from the gaps</summary>

**Pair A:** The gap: user says Slack is their primary communication tool but actually uses email for morning information-gathering. Insight: "The user treats Slack as a reactive, direct-message tool and email as a broadcast/broadcast-catch-up tool — meaning 'staying up to date' for this user happens in email, not Slack. Slack's notifications and unread counts are not the triggers they assumed." → HMW direction: "How might we meet this user in email where they actually start their day?"

**Pair B:** The gap: user says the approval process is clear but performs an informal pre-check ritual before officially submitting. Insight: "The formal approval process has a high perceived risk of rejection that the user mitigates with an informal channel — meaning the process feels unreliable or opaque enough that no one trusts a cold submission. The informal pre-check is a trust mechanism, not a preference." → HMW direction: "How might we give submitters enough signal about likely approval before they formally submit, so they don't need a back-channel?"
</details>

---

## Exercise 7 — Full chain under constraint (stretch + spaced callback from Day 5)

**The scenario:** You are a PM at a healthtech company. Your empathy research (5 interviews + 3 observation sessions with hospital nurses) has produced the following single insight:

> "Ward nurses don't document patient deterioration signs in real time during their shift — they batch-update the electronic record during charting time — because the documentation system requires too many taps and confirmations to use at the bedside, and nurses prioritize patient contact over administrative accuracy during busy periods."

**Your task:**
1. Write a POV statement for the charge nurse archetype (not the bedside nurse — she is the one who reads the documentation and acts on it)
2. Write one HMW question that addresses the charge nurse's perspective, derived from the same insight
3. Explain in two sentences how this illustrates the wicked nature of the problem (connect to Day 5)

<details>
<summary>Full answer</summary>

**1. POV for the charge nurse:** "A charge nurse coordinating patient care across a ward needs to make triage and escalation decisions based on a real-time picture of patient status, because the documentation she reads was entered in batches — meaning the clinical picture she sees is always lagging the clinical reality, and she has no way to know how large that gap is."

**2. HMW question:** "How might we help charge nurses understand the confidence level of the clinical record — specifically, how current it is — so they can weight their decisions appropriately?"

**3. Wicked nature (Day 5 connection):** The problem cannot be solved by fixing the documentation system alone, because doing so would change the bedside nurse's workflow, which would affect patient contact time, which is a clinical safety variable — solving one part of the problem generates a new problem elsewhere. The question "who should we design for — the bedside nurse or the charge nurse?" is itself a wicked sub-problem, because it depends on clinical workflow tradeoffs that have no single right answer.
</details>

---

## Reflection

Before moving on, answer these two questions from memory:

1. Which of the seven exercises felt hardest? That sub-skill is your personal bottleneck — the one to revisit before Day 28.
2. Which exercise revealed a gap between what you thought you knew and what you could actually produce without notes?

These two answers are more valuable than any score. The drill has no grade — it is a diagnostic.

---

## Navigation

← **Previous:** [Day 14 — How Might We Questions](./day-14-how-might-we-questions.md)
→ **Next:** [Day 16 — Brainstorming Done Right](../../04-ideate/days/day-16-brainstorming-done-right.md)
