# Day 7 — The Research Interview

> **Today's one idea:** A DT interview surfaces needs people cannot articulate by asking about behavior, not opinion.
> **Reading time:** ~40 min · **Prereqs:** Days 5–6
> **Primary source for today:** Stanford d.school, *Design Thinking Bootcamp Bootleg*, 2018, pp. 9–18 ("Empathize" section)
> **Before you start:** Recall Day 6's load-bearing idea — one sentence, no looking. *Why can't users fully report their own needs — name one of the three reasons.*

---

## The hook

Two interviewers. Same user. Same topic: how nurses manage medication handoffs between shifts.

**Interviewer A:** "On a scale of 1–5, how confident do you feel about the current handoff process?"
User: "About a 3. It could be better."

**Interviewer B:** "Tell me about the last time a handoff caused a problem. Walk me through exactly what happened."
User: "Last Tuesday, I came on at 7am and found that the night nurse hadn't noted a PRN pain med administered at 5am. The patient was asking for more by 7:30, and I had no idea if they'd already hit their limit. I had to track down the night nurse by phone — took 22 minutes. That happens maybe twice a week."

Both interviewers talked to the same nurse about the same topic. One got a rating. One got a story with a specific incident, a time cost, a frequency, and an emotional texture (the nurse's anxiety about overdose risk).

The second interview is not just "better data." It is a completely different category of evidence — the kind you can actually design against.

The difference: **Interviewer A asked for an opinion. Interviewer B asked for a story.**

---

## Building the intuition

A DT research interview is not a product call. It is not a usability test. It is not a customer satisfaction survey delivered verbally. It is a structured conversation designed to surface behavior, context, and latent need.

The core technique is simple and counterintuitive for people trained in product discovery:

**Ask about the past, not the future. Ask about specific incidents, not general habits. Ask about behavior, not opinion.**

Why? Because:
- **Future questions** ("Would you use a feature that...?") elicit optimistic intentions, not real behavior
- **General questions** ("How do you usually handle X?") elicit idealized self-descriptions, not specific experiences
- **Opinion questions** ("What do you think of X?") elicit conscious preferences, not the latent needs that observation would reveal

**Story questions** ("Tell me about a specific time when...") bypass all three traps. Stories are anchored in real events, contain specific behavioral detail, and often surface emotional texture that the user didn't plan to share.

Five question types, ranked from least to most useful in a DT interview:

| Type | Example | Why it's problematic |
|------|---------|---------------------|
| Closed | "Do you use the export feature?" | Yes/no; no story, no context |
| Opinion | "What do you think of the dashboard?" | Conscious preference, not behavior |
| Hypothetical | "Would you use a dark mode?" | Optimistic intention, not real behavior |
| **Behavioral** | "Walk me through how you typically start your week at work" | Better — reveals real workflow |
| **Story/incident** | "Tell me about the last time this process caused you a problem" | Best — anchored in a real event with real detail |

---

## The formal picture

A DT research interview has four phases:

**1. Frame and build rapport (5 min)**
Explain you are learning, not testing them. You have no product to sell; you want to understand their experience. Ask them to think out loud and correct you if you misunderstand. Ask a warm-up question about their role or daily routine — not about the topic yet.

**2. Explore their world (15–20 min)**
Ask broad, open questions about their context before narrowing. "Tell me about your workday" before "Tell me about how you manage medication handoffs." This reveals context you didn't know to ask about.

**3. Dig into specific incidents (10–15 min)**
This is the core. For every general statement they make, ask for a specific story: "Can you tell me about a specific time that happened?" Follow up on anything emotionally charged: "You seemed frustrated when you mentioned X — what was going on there?" Use the "five whys" selectively — not in sequence, but when a stated reason feels like it is hiding a deeper one.

**4. Wrap up (5 min)**
Ask: "Is there anything about this topic that I haven't asked but should have?" This often surfaces the most important insight — the thing they assumed you already knew.

**Key techniques:**

```
The "Five Whys" in practice:
User: "I export to CSV because it's easier."
You: "What makes CSV easier for you?"
User: "Because I can open it directly in Excel."
You: "What do you do with it in Excel?"
User: "I reformat the dates and then paste it into our weekly report template."
You: "Why does the date format need changing?"
User: "Because our finance team requires ISO format and your export uses US format."
→ The real problem: date format incompatibility with finance. Nothing to do with CSV.
```

**What NOT to do:**
- Don't lead: "Do you agree that the current process is too slow?" leads directly to confirmation bias
- Don't suggest solutions: "Would it help if we added a button here?" shifts from research to validation
- Don't interpret aloud: "So you're saying you feel anxious" — let them name their emotions
- Don't multitask: take notes by hand or have a separate note-taker; eye contact matters

---

## Where it breaks / what it is not

**Five users is enough to start.** Nielsen's research suggests that 5 users reveal approximately 85% of usability issues. The d.school suggests 5 users per user archetype for DT research. You do not need 50 interviews before defining — you need 5 rich ones. The goal is depth over breadth.

**Recording is not the same as listening.** Many teams record interviews and then never transcribe or review them. The value is in real-time listening and note-taking — not in having an archive. If you record, commit to reviewing it within 24 hours.

**This is not a product demo.** The single most common failure mode: the interviewer starts explaining features. The moment you describe your product is the moment the user stops telling you about their problem and starts giving you feedback on your solution. Stay in problem space as long as possible.

**"User interviews" on a product call are not DT interviews.** Most product calls are 30% small talk, 30% demo, 30% "what features do you want," 10% listening. A DT interview is the inverse: 80% listening, 20% structured probing.

---

## Try it yourself

> **Close this page before attempting Exercise 1.**

**Exercise 1 — Retrieval.** Without looking: name the five question types from worst to best in a DT interview, and state in one sentence why story/incident questions are at the top.

<details>
<summary>Compare to this</summary>

Worst to best: closed, opinion, hypothetical, behavioral, story/incident. Story questions are at the top because they are anchored in real past events — they bypass optimistic future-projection (hypotheticals), bypass idealized self-description (general behavioral questions), and reveal emotional and contextual detail that no other question type surfaces. If you had the order slightly different, what matters is that story/incident is clearly at the top and closed is clearly at the bottom.
</details>

---

**Exercise 2 — Direct application.** You are interviewing a nurse about medication handoffs (the Day 7 hook scenario). Write five actual interview questions you would ask — one of each type. Then cross out the three worst ones and explain why the remaining two are the ones worth asking.

<details>
<summary>A strong answer (yours may differ)</summary>

**Closed:** "Do you use the handoff checklist every shift?" *(cross out — yes/no)*
**Opinion:** "What do you think of the current handoff system?" *(cross out — opinion, not behavior)*
**Hypothetical:** "Would you use a mobile app for handoffs?" *(cross out — future intention)*
**Behavioral:** "Walk me through how you typically conduct a shift handoff." *(keep — reveals real process)*
**Story/incident:** "Tell me about the last time a handoff went wrong. What happened?" *(keep — anchored, specific, likely to surface the real problem)*

The two keepers open the interview (behavioral, for context) and then drive into a specific incident (story, for depth). The three crossed-out questions would give you numbers, opinions, and wishes — none of which you can design against reliably.
</details>

---

**Exercise 3 — Stretch.** You are interviewing someone and they say: "I generally handle it fine, it's just occasionally a bit slow." This is a vague general statement. Write your next three follow-up questions, in order, designed to move from that vague statement to a specific incident with enough detail to be useful.

<details>
<summary>A strong sequence</summary>

1. "Can you tell me about a specific time recently when it felt slow?" *(move from general to incident)*
2. "Walk me through exactly what you were doing at that moment — what had just happened before?" *(get context and sequence)*
3. "What was the impact of that slowness for you — what did you end up doing?" *(surface the consequence and any workaround)*

The sequence: ground → contextualize → consequences/workaround. The workaround is often where the real need lives.
</details>

---

**Transfer — apply it:**

> Think of a feature decision coming up in your work. Write one story-type question you could ask a real user this week to get below-the-waterline data on it. Make it specific enough that you could actually send it as a calendar invite topic.

---

## Connect it back

Day 6 established *why* observation beats self-report. Day 7 gives you the primary conversational tool for closing the gap — a structured interview that asks about behavior and incident, not opinion and intention. Day 8 takes you one step further: when even a good interview can't capture what you need, you go and watch.

**Sharp question you should be able to answer now:** Why do you ask "Tell me about the last time..." rather than "Tell me about how you usually..."?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Stanford d.school, *Design Thinking Bootcamp Bootleg* (2018), pp. 9–13 ("Interview for Empathy"). The d.school's concise guide to interview techniques — includes their specific list of "why to interview" and common mistakes. Free PDF at dschool.stanford.edu/resources.

**Free video — watch today:**
NNgroup, *"User Interviews: The Complete Guide"* — NNgroup YouTube channel. Search YouTube: `NNgroup user interviews complete guide`. ~8 min. Covers question types, probing techniques, and common mistakes with real examples. Directly reinforces today's content.

**Free video — companion:**
IDEO U, *"How to Conduct User Interviews"* — Search YouTube: `IDEO user interview how to` — IDEO's short introduction to empathy interviewing. ~5–7 min. Different framing; good to watch both for the contrast.

**If you want the deep version:**
Liedtka, Jeanne and Tim Ogilvie, *Designing for Growth* (Columbia Business School Publishing, 2011), Tool 2 "Value Chain Analysis" and Tool 3 "The Interview Guide." Liedtka's interview guide template is the most concrete L1 tool available — it gives you a printable question structure for a 45-minute DT interview. Reading time: ~20 additional minutes.

---

## Navigation

← **Previous:** [Day 6 — Empathy vs. Sympathy](./day-06-empathy-vs-sympathy.md)
→ **Next:** [Day 8 — Observation in the Field](./day-08-observation-in-the-field.md)
