# Day 20 — Low-Fidelity Prototyping

> **Today's one idea:** Paper prototypes, storyboards, and role-plays test assumptions 10× faster than code — and often surface the same issues.
> **Reading time:** ~38 min · **Prereqs:** Days 18–19
> **Primary source for today:** Knapp, Jake, John Zeratsky, and Braden Kowitz. *Sprint.* Simon & Schuster, 2016. Chapters 9–10, pp. 193–223.
> **Before you start:** Recall Day 19's load-bearing idea — one sentence, no looking. *What is a prototype for — and what makes something a prototype rather than a product?*

---

## The hook *(spaced callback to Day 14 — HMW questions)*

In 1982, two researchers at IBM named John Gould and Clayton Lewis were studying how to make software easier to use. They asked a simple question: how early in the design process can you identify usability problems?

Their answer: you can find 80% of usability issues with a prototype made of paper, a pencil, and a human playing the role of the computer.

The technique — which later became known as **paper prototyping** — works like this: a designer draws the screens of an interface on paper. A user sits across the table. A second person (the "human computer") manually swaps out paper screens as the user "taps" or "clicks" on them. A third person takes notes.

The user, astonishingly, suspends disbelief almost immediately. They interact with paper as if it were software. They say things like "this button isn't working" when the human computer doesn't swap screens fast enough. The illusion is fragile but sufficient — sufficient to reveal whether the flow makes sense, whether labels are understandable, and whether users can find what they need.

This technique is 40+ years old, costs zero engineering time, and still outperforms most alternatives for concept-level learning.

---

## Building the intuition

Low-fidelity prototyping works because **users don't need polish to engage with an idea** — they need enough signal to react to the underlying concept. Paper sketches provide just enough signal.

The three most useful low-fidelity formats for product practitioners:

**Format 1: Paper prototype (screen-based products)**

Draw each key screen on a separate piece of paper or index card. Include:
- The major UI elements (buttons, input fields, navigation)
- Enough text to communicate what each element does
- No color, no pixel-perfect spacing — rough is fine

To test: lay screens face-down. Give the user a task: "You've just received a notification that a colleague updated a shared project. Show me what you'd do." As they point to or touch elements, you (the human computer) swap screens. Take notes on where they hesitate, where they go wrong, and what they say aloud.

**Format 2: Storyboard (service or experience concepts)**

A storyboard is a 6–8 panel comic strip. Each panel shows one step in the user's experience with the concept. No artistic skill required — stick figures work. The panels show:
- Panel 1: The user's current situation (the pain point)
- Panels 2–6: The user encountering and using the concept
- Panel 7–8: The user's situation after (the gain)

To test: show the storyboard to a user and ask them to narrate what they see. Where does the story stop making sense? Where do they say "but I wouldn't do that"? The storyboard tests the narrative plausibility of your concept before any UI is designed.

**Format 3: Role-play (service or human-interaction concepts)**

For concepts involving a service interaction, role-play is the most powerful and least-used prototype format. One team member plays the user; another plays the service provider or system. You act out the interaction in real time.

Role-play tests:
- Whether the conversation flow makes sense
- Whether the information the service provides is what the user actually needs
- Whether the emotional tone of the interaction matches the user's needs

Five minutes of role-play will reveal more about a service concept than a week of whiteboarding. The discomfort of acting it out is exactly the signal that something important is being tested.

---

## The formal picture

**Side-by-side comparison of effort vs. learning:**

| Method | Time to build | Learning per hour invested | Best for |
|--------|--------------|--------------------------|---------|
| Paper prototype | 30 min | Very high — tests concept direction | Screen-based apps, flows |
| Storyboard | 45 min | High — tests experience narrative | Multi-step journeys, new service concepts |
| Role-play | 15 min setup | Very high — tests human interaction | Services, support flows, onboarding |
| Wireframe (digital) | 2–4 hours | Medium — tests navigation logic | Confirmed concepts, flow testing |
| Figma prototype | 1–2 days | Low-medium — tests UI detail | Late-stage concept validation |
| Coded prototype | 1–2 weeks | Low (per learning unit) — tests technical feasibility | Confirmed concept + flow |

**The paper prototype process step by step:**

```
Before the session (30 min):
1. Sketch each key screen on paper — one per index card
2. Write a task for the user: "Your goal is to [task]"
3. Write a hypothesis: "We believe users will [action] when they see [element]"

During the session (20–30 min):
1. Brief the user: "This is a rough sketch. Treat it like a real product. 
   Think out loud as you go."
2. Give the task — don't explain the interface
3. Swap screens manually as the user interacts
4. Note: hesitations, wrong paths, verbal reactions, anything surprising

After the session (15 min):
1. Write down the three most important observations while they're fresh
2. Mark which hypothesis was confirmed or denied
3. Note what the next prototype needs to change
```

**What "think aloud" means and why it matters:**

Ask users to narrate their thinking as they interact: "I see this button — I think it means X — I'll press it — oh, that's not what I expected." This running commentary reveals the mental model users bring to your interface — specifically where their model and your designer's model diverge (the Norman gulfs from Day 2). Without think-aloud, you only see behavior. With think-aloud, you see the reasoning behind the behavior.

---

## Where it breaks / what it is not

**Paper prototypes don't test visual polish — and that's the point.** If your question is "do users find this interface beautiful and trustworthy?", a paper prototype will mislead you. That question requires higher fidelity. But if your question is "can users navigate from the home screen to completing a task?", paper answers it reliably and cheaply.

**Role-play feels awkward — that's not a reason to skip it.** The discomfort of acting out a service interaction is almost always a signal that the interaction itself has an awkward moment in it. If you feel uncomfortable playing the role of the AI assistant explaining a rejection, that discomfort is telling you the user will feel it too.

**Low-fi prototyping is not for external stakeholders.** Paper prototypes are for learning, not for presentations. Showing a paper prototype to a VP as a "demo" of your concept will likely result in premature feature requests or loss of confidence. Build higher fidelity for presentations; keep paper prototypes for user learning sessions.

**You still need a test plan.** A paper prototype without a user task, a hypothesis, and a note-taker is just a drawing session. The format is cheap — the planning is not optional.

---

## Try it yourself

> **Close this page before attempting Exercise 1.**

**Exercise 1 — Retrieval.** Without looking: name the three low-fidelity prototype formats from today, and state in one phrase what each one is best suited to test.

<details>
<summary>Compare to this</summary>

**Paper prototype** — best for testing whether users can navigate a screen-based interface and whether the flow logic makes sense. **Storyboard** — best for testing whether the narrative of a user's experience with a concept is plausible end-to-end. **Role-play** — best for testing human-to-human or human-to-system service interactions and whether the emotional tone and information exchange is right.
</details>

---

**Exercise 2 — Direct application.** You have this concept: *"When a new employee joins a remote company, the system automatically schedules five 15-minute 'intro calls' with colleagues from different teams in their first week, based on the new hire's role and interests."*

Choose the right low-fidelity prototype format, write the user task you would give, and write the hypothesis you would test. Then name the one thing you would not learn from this prototype.

<details>
<summary>A strong answer</summary>

**Format:** Role-play — this is a service interaction (the scheduling system "offers" matches; the new employee reacts). Paper prototype won't capture the interpersonal dynamics.

**User task:** "It's your first Monday at the company. You've just received a message saying five intro calls have been scheduled for you this week. I'm going to play the system — here's the message. Walk me through what you'd do."

**Hypothesis:** We believe new employees will feel relieved (not overwhelmed) by having introductions automatically scheduled, because it removes the social burden of initiating connection with strangers.

**What you would NOT learn:** Whether the matching algorithm (role + interests) produces *good* matches. Role-play can't test the algorithm quality — it can only test whether users *want* pre-scheduled intros at all. To test match quality, you need real data and real people who know each other. That's a second-loop prototype question.
</details>

---

**Exercise 3 — Stretch (spaced callback from Day 16 — brainstorming).** In Day 16, you learned that the first ideas generated in a brainstorm are usually the most obvious. How does paper prototyping enforce the same "go through the obvious ideas quickly" discipline in the Prototype phase — and why does building 3 paper prototypes in a day beat spending 3 days building 1 Figma prototype?

<details>
<summary>The parallel argument</summary>

In brainstorming, volume before evaluation ensures you pass through the obvious ideas to reach the creative ones — you can't skip to idea 40 without having idea 5 first. In prototyping, the same logic applies: the first prototype of a concept almost always tests the most obvious interpretation of the idea. When it fails (or partially succeeds), the learning points you toward the non-obvious version — but only if you can iterate quickly. Three paper prototypes in a day means three cycles of "build → test → learn → refine," each one more precisely targeted than the last. One Figma prototype in three days means one cycle, with high sunk-cost pressure not to discard it if the results are mixed. Low fidelity enforces iteration discipline in the same way that sticky notes enforce brainstorming volume.
</details>

---

**Transfer — apply it:**

> Pick one concept from your team's current backlog that has been in planning for more than two weeks without a user test. Write a 5-step paper prototype plan: what you'd sketch, what task you'd give the user, what hypothesis you'd test, who you'd recruit, and when you could run it this week.

---

## Connect it back

Day 19 established that a prototype is a question made tangible. Day 20 gives you the three cheapest formats for making that question tangible — paper, storyboard, and role-play. Tomorrow's Day 21 is the hardest judgment call in the Prototype phase: given a concept with many testable aspects, which assumption is the one most worth testing first?

**Sharp question you should be able to answer now:** A user is interacting with your paper prototype and says "I don't understand what this button does." Is that a prototype fidelity problem or a concept problem — and how do you tell the difference?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Knapp et al., *Sprint* (Simon & Schuster, 2016), Chapters 9–10 "Thursday: Prototype," pp. 193–223. Knapp's "just enough" prototyping principle and his specific advice on building a Façade prototype in one day. The facade/Wizard-of-Oz method covered in Sprint is the most immediately actionable technique for product teams.

**Free video — watch today:**
NNgroup, *"Paper Prototyping: A How-To Guide"* — Search YouTube: `NNgroup paper prototyping how to`. ~8 min. A clear, step-by-step demonstration of a real paper prototype session — you see the human-computer technique in action. Essential viewing before running your first session.

**Free video — companion:**
IDEO U, *"How to Prototype in Design Thinking"* — Search YouTube: `IDEO prototype design thinking how to`. ~6 min. IDEO's treatment of prototyping fidelity levels with product examples.

**If you want the deep version:**
Snyder, Carolyn. *Paper Prototyping: The Fast and Easy Way to Design and Refine User Interfaces.* Morgan Kaufmann, 2003. Chapter 3 "Creating Paper Prototypes" and Chapter 5 "Running a Usability Test." The definitive text on paper prototyping — if you plan to run regular paper prototype sessions, this is the reference to own. Reading time for two chapters: ~60 additional minutes. Flag for after Day 28.

---

## Navigation

← **Previous:** [Day 19 — What Is a Prototype?](./day-19-what-is-a-prototype.md)
→ **Next:** [Day 21 — What to Prototype](./day-21-what-to-prototype.md)
