# Day 1 — What Design Thinking Actually Is

> **Today's one idea:** Design Thinking is a problem-finding discipline before it is a problem-solving one.
> **Reading time:** ~35 min · **Prereqs:** None — this is Day 1
> **Primary source for today:** Tim Brown, "Design Thinking," *Harvard Business Review*, June 2008, pp. 84–92
> **Free video:** Tim Brown, *"Designers — Think Big!"* TED2009, 16 min. Search YouTube: `Tim Brown designers think big TED` (official TED channel). Watch the first 6 minutes before reading.

---

## The hook

In 2004, the government of Peru asked a design firm to help reduce malnutrition in children. The obvious solution — the one any well-meaning engineer would propose — was: design better food. More calories, better distribution, cheaper production.

IDEO went to rural Peru and watched. Not surveyed. Watched.

What they found: the food wasn't the problem. Mothers already knew which foods were nutritious. The problem was that preparing those foods took two hours, and most mothers worked in the fields all day. The real problem was *time and social norms around cooking*, not nutrition knowledge.

If you build the best nutritious food product in the world and drop it into that context, almost nothing changes. You have answered a question nobody asked.

This is the trap Design Thinking was built to avoid.

---

## Building the intuition

Think about the last product or feature that failed — not because it was badly built, but because it turned out nobody actually wanted it the way you imagined. The engineering was fine. The sprint was run well. But the underlying assumption about what the user needed was wrong.

That failure happened in a stage *before* the product was built. It happened when someone — a PM, a stakeholder, a team — decided what problem to solve. That decision was probably made fast, with incomplete information, shaped by what was easy to measure or what the loudest stakeholder wanted.

Design Thinking is the discipline that slows that moment down and makes it rigorous.

Here is the clearest way to see the distinction:

```
Normal product process:           Design Thinking process:

  Problem (given)                   Who has this problem? (research)
       ↓                                     ↓
  Solution (build it)               What is the *real* problem? (define)
       ↓                                     ↓
  Ship                              Many possible solutions (ideate)
                                             ↓
                                    Quick test, learn, iterate
                                             ↓
                                    Ship (or reframe and restart)
```

The left column assumes the problem statement is correct. The right column treats the problem statement as the first thing to interrogate.

Tim Brown's 2008 HBR paper defines a design thinker as someone with three characteristics:

1. **Empathy** — the ability to imagine the world from multiple perspectives, including those of people who are not like you
2. **Integrative thinking** — the ability to see contradictions not as problems to eliminate, but as tensions that, held together, can reveal a better solution
3. **Experimentalism** — a bias toward trying things rather than planning endlessly

Notice what is *not* on that list: visual skill, software ability, or any specific domain knowledge. DT is a cognitive posture, not a professional credential.

---

## The formal picture

Design Thinking has a working definition you will see repeated throughout this course:

> **Design Thinking** is a human-centered, iterative approach to problem-solving that emphasizes empathetic inquiry, divergent idea generation, and rapid prototyping to address ambiguous or complex problems.

Break this down:

| Term | What it means in practice |
|------|--------------------------|
| **Human-centered** | The user's experience is the primary constraint, not technology or business process |
| **Iterative** | You loop — empathize → define → ideate → prototype → test → repeat — you do not march linearly |
| **Empathetic inquiry** | You observe and interview to surface needs people cannot articulate themselves |
| **Divergent idea generation** | You generate many ideas before converging on one |
| **Rapid prototyping** | You build cheaply to learn, not to launch |
| **Ambiguous or complex problems** | The "wicked problems" we will study on Day 5 — DT is less necessary for problems with known solutions |

One more distinction that will matter across the entire course:

- A **tame problem** has a clear formulation, a known solution space, and measurable progress. "How do we reduce server response time below 200ms?" is tame.
- A **wicked problem** resists clear formulation, has no obvious right answer, and solving one part creates new problems. "How do we reduce childhood obesity?" is wicked.

DT is built for wicked problems. It is overkill for tame ones. Knowing the difference is the first judgment a practitioner must make.

---

## Where it breaks / what it is not

**DT is not a guarantee of good solutions.** It is a process that increases the probability of solving the *right* problem. You can run every DT phase correctly and still build a product that fails — because markets are unpredictable, because the problem you found is real but not economically viable, or because execution falls short. DT improves the problem-finding phase, not the outcome.

**DT is not a replacement for domain expertise.** The Peru nutrition team still needed nutrition scientists. DT told them where to point those scientists, not what the scientists should know.

**DT is not Agile rebranded.** This confusion trips up nearly every practitioner with an Agile background, so we'll address it carefully on Day 4. For now: Agile is primarily a delivery framework. DT is primarily a discovery framework. They operate in different time horizons and answer different questions.

**DT is not only for designers.** Brown's 2008 paper was addressed explicitly to business leaders and product managers. The methods are accessible to anyone willing to observe before assuming.

---

## Try it yourself

> **Close this page before attempting Exercise 1. Seriously — close it.**

**Exercise 1 — Retrieval.** Without looking at the page: write down (in your own words, in one sentence) what makes Design Thinking different from normal problem-solving. Then open the page and compare.

<details>
<summary>What to compare against</summary>

The key distinction: normal problem-solving treats the problem as given and focuses on building the solution. Design Thinking treats the problem statement as the first thing to interrogate — it is a problem-*finding* discipline before it is a problem-solving one. If your sentence captures that the problem itself is questioned, you have it.
</details>

---

**Exercise 2 — Direct application.** Think of a product or feature decision you were involved in (at work, a side project, or even a purchase decision). Was the problem statement questioned before work began, or was it assumed? Write two sentences: what the assumed problem was, and what a Design Thinking team would have done first before accepting it.

<details>
<summary>What a good answer looks like</summary>

There is no single correct answer here. A strong response names a specific problem statement (e.g., "we assumed users wanted faster onboarding") and describes a concrete first step in DT (e.g., "a DT team would have first observed how new users actually navigate the product for the first time, before deciding that speed was the issue"). The key is specificity — don't stay abstract.
</details>

---

**Exercise 3 — Stretch.** Tim Brown's three characteristics of a design thinker are empathy, integrative thinking, and experimentalism. You already work in Agile. Which of these three do you think Agile *most* neglects — and why? Write three sentences defending your answer.

<details>
<summary>A sample argument (yours may differ)</summary>

The most defensible answer is **empathy** — not because Agile practitioners are uncaring, but because Agile structures (sprints, backlogs, velocity tracking) are primarily focused on the delivery team's throughput rather than on sustained contact with the user. User stories exist, but they are often written by product managers based on second-hand information. Experimentalism is already present in Agile (iterate, ship small); integrative thinking shows up in retrospectives. The biggest gap, structurally, is the depth of empathetic observation built into the process. If you argued differently, articulate the structural reason — not just that you personally feel you do the thing.
</details>

---

**Transfer — apply it:**

> Name a problem your team is currently working on or recently shipped. Write one sentence: is it a tame problem (clear formulation, known solution space) or a wicked one (the question itself is contested)? If it is wicked, what assumption about the user is baked into the current problem statement that no one has verified?

---

## Connect it back

Today established the foundational claim: the problem statement itself is the first design challenge. Every method you will learn across the next 27 days — interviews, empathy maps, POV statements, prototypes, tests — exists to serve this one discipline of questioning the question.

Tomorrow we will ask: if we're designing for humans, what does it actually mean to understand a human? The answer will surprise you — and it will make every research tool we pick up afterward feel obvious rather than arbitrary.

**Sharp question you should be able to answer now:** What is the difference between a problem you *solve* and a problem you *find* — and why does that difference justify an entirely separate methodology?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Tim Brown, "Design Thinking," *Harvard Business Review*, June 2008, pp. 84–92. Read the first three sections ("Meet Thomas Edison," "What Is Design Thinking?", "How Design Thinking Happens"). These three sections are the intellectual core; the rest of the paper extends into social innovation, which is optional at L1.

**Free video (watch now or after Day 3):**
Tim Brown, *"Designers — Think Big!"* TED2009. Search YouTube: `Tim Brown designers think big TED` — official TED channel, 16 min. The first 6 minutes give the Peru malnutrition story and three other examples that reinforce today's one idea. The rest of the talk extends into social-scale applications — interesting, not essential for L1.

**If you want the deep version:**
Tim Brown, *Change by Design* (HarperBusiness, 2009), Chapter 1 "Getting Under Your Skin." This is the book-length version of today's argument, told through richer stories. Read it if the HBR paper left you wanting more texture. Chapter 1 is ~30 pages; plan 45–60 extra minutes.

---

## Navigation

← **Back to course overview:** [README](../../../README.md)
→ **Next:** [Day 2 — The Human at the Center](./day-02-human-at-the-center.md)
