# AI Engineering

> **The promise:** after 30 days you can build a production-grade AI system from scratch across the **seven engineering disciplines** — foundation model, prompt, context, harness, loop, graph, and ontology — and defend every design choice line by line.

## Who this course is for

You already call LLM APIs and have built with agent frameworks, but the engineering *around* the model — choosing it, instructing it, feeding it, running it, looping it, coordinating it, and giving it shared meaning — is a black box. You're strong on models and math, lighter on the production systems that wrap them. This course is **L2 (Builder)**: you implement each piece yourself and debug at the internals level, then harden it for production. It optimizes for one outcome — *build and defend your own AI system* — not for surveying frameworks.

Not for you if you want a LangChain/LangGraph tutorial (this course builds *under* those abstractions), or if you only want to use models without understanding how they run. There is code on almost every page; skipping it defeats the point.

## The seven disciplines

The course is organized around seven layers, each with a one-word essence (full map in [`disciplines.md`](disciplines.md)):

| # | Discipline | Essence | Question |
|---|---|---|---|
| 1 | Foundation Model | **Capability** | Which model, and what can it reliably (not) do? |
| 2 | Prompt Engineering | **Instructions** | How do I specify the task so success is evaluable? |
| 3 | Context Engineering | **Information** | What does it see for its next decision? |
| 4 | Harness Engineering | **Runtime** | What environment runs it — tools, boundaries, state, logs? |
| 5 | Loop Engineering | **Feedback** | How does it act, check, retry, stop, escalate? |
| 6 | Graph Engineering | **Coordination** | How is the work connected — branches, handoffs, parallelism? |
| 7 | Ontology Engineering | **Shared Meaning** | What counts as a customer, an approval, a completed order? |

## The arc at a glance

On **Day 1** you can call an LLM API and use frameworks, but "the AI" and "the model" are the same thing in your head. By **Day 30** that inversion is complete: the model is a replaceable, jaggedly-capable component, and the real engineering surface is the seven layers around it — which you've built and can defend choice by choice.

The through-line is one question: **what turns a stateless, jagged, probabilistic model into a system that reliably does real work?** The seven disciplines are seven answers. The course teaches them **bottom-up by prerequisite** (you meet the model, then instruct it, build a loop, engineer its context, control it, harden it, and give it shared meaning) while the seven layers stay the reference map — every day is tagged with the layer it serves. The capstone forces every choice at once, on a real task, and makes you justify each.

## How to use this course

- **Rhythm:** one page per day, 30–45 min of focused reading, in order. Each page assumes the one before.
- **Exercises are non-optional.** Every page opens with a *retrieval* exercise: close the page and reconstruct the prior idea from memory *before* reading.
- **The code compounds.** Day 7's `dispatch` feeds Day 10's loop; Day 12's assembler feeds Days 14–17; by Day 30 it's your capstone system. Keep one growing repo.
- **`bibliography.md`** is for when a page hooks you. **Rest & Synthesize days (18, 29)** add no material — pure retrieval, where retention is won.
- **If life intervenes:** never skip the retrieval opener, even if you read nothing else.

## The learning path

| Day | Layer | Title | One idea |
|---|---|---|---|
| 1 | — | [What Is AI Engineering?](modules/00-foundations/days/day-01-what-is-ai-engineering.md) | Engineering *around* a rented model is the discipline. |
| 2 | — | [The Stateless Model & the Harness-as-OS](modules/00-foundations/days/day-02-stateless-model-and-harness.md) | LLM = stateless CPU; harness = its OS. |
| 3 | ① Capability | [Choosing a Foundation Model](modules/00-foundations/days/day-03-choosing-a-foundation-model.md) | Pick the cheapest model that clears *your* eval. |
| 4 | ① Capability | [Model Capabilities & Failure Modes](modules/00-foundations/days/day-04-model-capabilities-and-failure-modes.md) | Capability is jagged; design around the pits. |
| 5 | ② Instructions | [Prompting as Programming](modules/01-harness-engineering-the-scaffold/days/day-05-prompting-as-programming.md) | A prompt is a spec, not a wish. |
| 6 | ② Instructions | [Structured Output & the Parse Boundary](modules/01-harness-engineering-the-scaffold/days/day-06-structured-output.md) | Constrain to schema; validate before trusting. |
| 7 | ④ Runtime | [Tools: The Model's Hands](modules/01-harness-engineering-the-scaffold/days/day-07-tools-the-models-hands.md) | A tool is a request the harness runs. |
| 8 | ④ Runtime | [The Tool Interface](modules/01-harness-engineering-the-scaffold/days/day-08-the-tool-interface.md) | The tool boundary is a trust boundary. |
| 9 | ⑤ Feedback | [Why You Need a Loop](modules/02-loop-engineering-building-the-loop/days/day-09-why-you-need-a-loop.md) | Capability comes from iteration + feedback. |
| 10 | ⑤ Feedback | [Your First Agentic Loop](modules/02-loop-engineering-building-the-loop/days/day-10-your-first-agentic-loop.md) | A working agent is ~60 lines you own. |
| 11 | ③ Information | [Context Is Everything It Sees](modules/03-context-engineering/days/day-11-context-is-everything.md) | The model's whole mind is the payload you assemble. |
| 12 | ③ Information | [Context Assembly](modules/03-context-engineering/days/day-12-context-assembly.md) | Rebuild the payload each turn: select → order → fit. |
| 13 | ③ Information | [Drill I: Context Assembly](modules/03-context-engineering/days/day-13-drill-context-assembly.md) | Reps on eviction & placement under pressure. |
| 14 | ③ Information | [Compaction & Lost-in-the-Middle](modules/03-context-engineering/days/day-14-compaction-lost-in-the-middle.md) | Compress + place, don't delete. |
| 15 | ③ Information | [Context Engineering Patterns](modules/03-context-engineering/days/day-15-context-engineering-patterns.md) | A reusable catalog — agents, RAG, chat. |
| 16 | ③ Information | [Memory & State Across Turns](modules/03-context-engineering/days/day-16-memory-and-state.md) | The loop is stateless unless you persist it. |
| 17 | ③ Information | [Retrieval-Augmented Generation](modules/03-context-engineering/days/day-17-retrieval-augmented-generation.md) | Ground the model in external knowledge. |
| 18 | — | [Rest & Synthesize I](modules/03-context-engineering/days/day-18-rest-synthesize-i.md) | Consolidate Days 1–17 from memory. |
| 19 | ⑤ Feedback | [Loop Control & Stopping](modules/04-loop-engineering-control-and-orchestration/days/day-19-loop-control-and-stopping.md) | A loop that can't decide it's done is a bug. |
| 20 | ⑤ Feedback | [Drill II: The Turn, End-to-End](modules/04-loop-engineering-control-and-orchestration/days/day-20-drill-the-turn-end-to-end.md) | Fuse assembly + memory + compaction + control. |
| 21 | ⑥ Coordination | [Multi-Agent Orchestration](modules/04-loop-engineering-control-and-orchestration/days/day-21-multi-agent-orchestration.md) | Split one loop into many only when context demands. |
| 22 | ④ Runtime | [Failure & Recovery](modules/05-harness-engineering-reliability-and-operations/days/day-22-failure-and-recovery.md) | Failure is an expected input, not an exception. |
| 23 | ⑤ Feedback | [The Evaluation Harness](modules/05-harness-engineering-reliability-and-operations/days/day-23-the-evaluation-harness.md) | You can't harden what you can't measure. |
| 24 | ④ Runtime | [Observability & Cost](modules/05-harness-engineering-reliability-and-operations/days/day-24-observability-and-cost.md) | Instrument first, then optimize. |
| 25 | ③+⑦ | [Context Graphs](modules/06-graph-engineering/days/day-25-context-graphs.md) | Store memory as a temporal graph you traverse. |
| 26 | ⑥ Coordination | [Control-Flow Graphs](modules/06-graph-engineering/days/day-26-control-flow-graphs.md) | Model the loop itself as a state graph. |
| 27 | ⑦ Shared Meaning | [Ontology & Shared Meaning](modules/07-ontology-engineering/days/day-27-ontology-and-shared-meaning.md) | Define what your entities and relations *are*. |
| 28 | ⑦ Shared Meaning | [Business Rules & Constraints](modules/07-ontology-engineering/days/day-28-business-rules-and-constraints.md) | Enforce meaning at the write boundary. |
| 29 | — | [Rest & Synthesize II](modules/08-synthesis/days/day-29-rest-synthesize-ii.md) | Consolidate the back half from memory. |
| 30 | — | [Capstone: Build & Harden an AI System](modules/08-synthesis/days/day-30-capstone-build-and-harden.md) | Assemble all seven layers; defend every choice. |

Full path with rationale and spaced-callback logic: [`learning-path.md`](learning-path.md).

## The Course Shelf

Top five must-haves (full annotated list in [`bibliography.md`](bibliography.md)):

1. **Chip Huyen, *AI Engineering*, O'Reilly 2025** — the spine of the discipline.
2. **Yao et al., "ReAct," ICLR 2023** (arXiv:2210.03629) — the loop's backbone.
3. **Anthropic, "Effective Context Engineering for AI Agents," 2025** — the context bottleneck.
4. **Packer et al., "MemGPT," 2023** (arXiv:2310.08560) — harness-as-OS and tiered memory.
5. **Noy & McGuinness, "Ontology Development 101," Stanford 2001** — the practical on-ramp to shared meaning.

## The capstone

On Day 30 you build and harden a real AI system: a coding agent (SWE-bench-lite style) or a customer-support / investigation agent (τ-bench style). "Done" means it runs a bounded, controlled loop against real tools; manages its own context under budget (with memory/RAG or a context graph where warranted); grounds its knowledge in an ontology with enforced constraints; recovers from an injected failure; emits a replayable trace; and passes a small eval set you wrote — and you can justify every choice against the alternatives the course rejected.

## Meta

- **Depth level:** L2 — Builder.
- **Structure:** 30 days across 9 modules, organized by the seven engineering disciplines.
- **Estimated total hours:** ~19–23 hours (30 pages × 35–45 min + capstone build).
- **Last updated:** 2026-09-10.
- **Glossary:** [`glossary.md`](glossary.md) · **Disciplines map:** [`disciplines.md`](disciplines.md).
