# Quantum Computing — L1 Conceptual Course

> **The promise:** By the end of this course, you can explain quantum computing clearly to a curious non-technical friend *and* follow quantum computing news and product announcements without being lost.

## Who this course is for

This course is for curious, intelligent adults who have heard about quantum computing and want to *actually understand it* — not just nod along. It assumes no physics background, no advanced math beyond school-level algebra, and only a vague familiarity with how classical computers work. It targets **L1 depth**: you will be able to explain, reason about, and evaluate claims — but not implement algorithms or derive physics from first principles.

This course is *not* for someone who wants to write quantum programs, read research papers mathematically, or go deep on quantum physics. If that's you, this course is still a great starting point — but follow it with L2 material listed in [`bibliography.md`](bibliography.md).

## The arc at a glance

On Day 1, you'll read about a wall that classical computers can never climb — not because engineers aren't clever enough, but because of the physics of information itself. By Day 6 you'll have three strange but concrete ideas in your hands — superposition, entanglement, interference — each explained through physical analogy, not equations. You won't be able to *build* anything yet, but you'll be able to explain why a quantum computer isn't just "a faster classical computer" to anyone who asks.

The middle arc (Days 7–18) turns those intuitions into mechanisms: how gates and circuits work, why certain problems yield to quantum speedup, and why building quantum hardware is one of the hardest engineering challenges humans have ever attempted. The final arc (Days 19–23) is about the world: near-term applications that are real, medium-term ones that are plausible, and claims that are premature — and how to tell the difference. The capstone asks you to *use* this, not recite it.

## How to use this course

- **Rhythm:** One page per day, 30–45 minutes of focused reading. Don't rush; don't skip.
- **Exercises:** Recommended even at L1. They take 5–10 minutes and are the fastest way to find out whether an idea actually landed.
- **Bibliography:** When a page points you to a specific chapter or timestamp, follow it if you have 15 extra minutes. The course stands alone, but the sources deepen it.
- **Rest & Synthesize days:** Days 6 and 14 introduce no new material. Use them to re-explain the preceding ideas in your own words — out loud or in writing. If you can't, re-read the relevant pages before continuing.
- **If life gets in the way:** Missing a day is fine. Missing a week means re-reading the previous 2–3 pages before you continue — concepts build on each other precisely.

## The learning path

| Day | Title | One idea | Core source |
|-----|-------|----------|-------------|
| 1 | [Why Quantum at All?](modules/01-quantum-world/days/day-01-why-quantum.md) | Classical computers have a ceiling that physics itself imposes | Gribbin, Ch. 1 |
| 2 | [The Strangeness of the Quantum World](modules/01-quantum-world/days/day-02-quantum-strangeness.md) | At tiny scales, nature violates everyday intuition — fundamentally | Gribbin, Ch. 3 |
| 3 | [Superposition — Both and Neither](modules/01-quantum-world/days/day-03-superposition.md) | A qubit isn't a fuzzy bit; it's a physical system in a genuine blend of states | Rudolph, Ch. 1 |
| 4 | [Entanglement — Correlated by Nature](modules/01-quantum-world/days/day-04-entanglement.md) | Two entangled qubits share a fate that can't be explained by local hidden information | Rudolph, Ch. 2 |
| 5 | [Interference — How Quantum Computers "Aim"](modules/01-quantum-world/days/day-05-interference.md) | Quantum algorithms amplify right answers and cancel wrong ones via wave mechanics | Rieffel & Polak, Ch. 1 |
| 6 | [Rest & Synthesize I — The Three Pillars](modules/01-quantum-world/days/day-06-rest-synthesize-1.md) | Consolidate: can you explain all three pillars without notes? | Days 1–5 |
| 7 | [Qubits in the Real World](modules/02-quantum-computers/days/day-07-qubits-real-world.md) | A qubit is a physical object — a photon, electron spin, or superconducting loop | Hidary, Ch. 2 |
| 8 | [Quantum Gates — Operations on Qubits](modules/02-quantum-computers/days/day-08-quantum-gates.md) | Quantum gates are reversible transformations that rotate a qubit's state | Rieffel & Polak, Ch. 4 |
| 9 | [Quantum Circuits — Wiring Gates Together](modules/02-quantum-computers/days/day-09-quantum-circuits.md) | A quantum circuit is a recipe: a sequence of gates applied before measurement | Rieffel & Polak, Ch. 4 |
| 10 | [Measurement — The Act of Looking](modules/02-quantum-computers/days/day-10-measurement.md) | Measurement collapses superposition into a definite outcome, probabilistically | Rudolph, Ch. 3 |
| 11 | [Deutsch's Problem — The First Quantum Speedup](modules/02-quantum-computers/days/day-11-deutsch-problem.md) | One quantum query answers a question that takes two classical queries | Rieffel & Polak, Ch. 5 |
| 12 | [Grover's Algorithm — Quantum Search](modules/02-quantum-computers/days/day-12-grovers-algorithm.md) | Searching N unsorted items takes √N quantum steps vs. N classical | Rieffel & Polak, Ch. 6 |
| 13 | [Shor's Algorithm — Why Cryptographers Worry](modules/02-quantum-computers/days/day-13-shors-algorithm.md) | Quantum computers can factor large numbers exponentially faster than any known classical method | Rieffel & Polak, Ch. 7 |
| 14 | [Rest & Synthesize II — Algorithms & Speedups](modules/02-quantum-computers/days/day-14-rest-synthesize-2.md) | Consolidate: what kinds of problems get quantum speedups, and why? | Days 7–13 |
| 15 | [Decoherence — The Enemy of Quantum](modules/03-building-quantum/days/day-15-decoherence.md) | Interaction with the environment destroys superposition; this is the central engineering challenge | Hidary, Ch. 3 |
| 16 | [Quantum Error Correction — Fighting Back](modules/03-building-quantum/days/day-16-error-correction.md) | Quantum errors can be detected and corrected without collapsing the state — at enormous overhead | Hidary, Ch. 4 |
| 17 | [The Hardware Landscape](modules/03-building-quantum/days/day-17-hardware-landscape.md) | Each hardware platform trades off coherence, speed, and scalability differently | Hidary, Ch. 5 |
| 18 | [Quantum Advantage vs. Quantum Supremacy](modules/03-building-quantum/days/day-18-advantage-vs-supremacy.md) | "Supremacy" is a narrow milestone; practical advantage on useful problems is a separate, harder goal | Preskill 2018 |
| 19 | [Quantum Cryptography — Unhackable by Physics](modules/04-applications-future/days/day-19-quantum-cryptography.md) | QKD uses quantum mechanics to detect eavesdropping; its security is a law of physics | Gribbin, Ch. 8 |
| 20 | [Quantum Simulation — The Original Killer App](modules/04-applications-future/days/day-20-quantum-simulation.md) | Quantum computers can simulate molecules that classical computers cannot | Feynman 1982 |
| 21 | [Quantum Machine Learning — Hype vs. Reality](modules/04-applications-future/days/day-21-quantum-ml.md) | QML speedup claims are narrower and more conditional than headlines suggest | Preskill 2018 |
| 22 | [The Road Ahead — Timelines, NISQ, and Fault Tolerance](modules/04-applications-future/days/day-22-road-ahead.md) | We are in the NISQ era: real but not yet transformative | Preskill 2018 |
| 23 | [Capstone — Explain, Evaluate, Anticipate](modules/04-applications-future/days/day-23-capstone.md) | Three written challenges that force you to use everything | All |

## The Course Shelf

1. **Gribbin** — *Computing with Quantum Cats* (2013) — Best accessible narrative; spine for Module 1.
2. **Hidary** — *Quantum Computing: An Applied Approach*, 2nd ed. (2021) — Hardware and practical framing; spine for Module 3.
3. **Rieffel & Polak** — *Quantum Computing: A Gentle Introduction* (2011) — Algorithm clarity; spine for Module 2.
4. **Preskill** — "Quantum Computing in the NISQ Era and Beyond" (2018) — Best single source on the field's current state.
5. **Aaronson** — *Quantum Computing Since Democritus* lecture notes — Best philosophical and conceptual framing.

Full annotated shelf → [`bibliography.md`](bibliography.md)

## The capstone

On Day 23, you will write three short pieces without notes: (1) a five-sentence dinner-table explanation of quantum computing for a curious non-technical friend; (2) a one-page annotation of a real quantum computing news article, flagging what's accurate, what's hype, and what questions you'd ask; and (3) a one-page "state of quantum" briefing — where we are in 2026, what the key challenges are, and what milestones to watch. If you can complete all three, you have crossed the L1 finish line.

## Glossary

[→ glossary.md](glossary.md)

---

## Meta

| | |
|--|--|
| **Depth level** | L1 — Practitioner (explain and evaluate, not implement) |
| **Estimated total time** | ~15–18 hours reading + ~5 hours exercises |
| **Modules** | 4 modules, 23 days |
| **Last updated** | June 2026 |
