# AI Engineering

> **The promise:** after 26 days you can build a production-grade AI system from scratch across the four engineering disciplines — **harness, loop, context, and graph** — and defend every design choice line by line.

## Who this course is for

You already call LLM APIs and have built with agent frameworks, but the engineering *around* the model — the harness that runs it, the loop that drives it, the context you feed it, the graph structures that scale it — is a black box. You're strong on models and math, lighter on the production systems that wrap them. This course is **L2 (Builder)**: you implement each piece yourself and debug at the internals level, then harden it for production. It optimizes for one outcome — *build and defend your own AI system* — not for surveying frameworks.

Not for you if you want a LangChain/LangGraph tutorial (this course builds *under* those abstractions), or if you only want to use models without understanding how they run. There is code on almost every page; skipping it defeats the point.

## The arc at a glance

On **Day 1** you can call an LLM API and use frameworks, but "the AI" and "the model" are the same thing in your head. By **Day 26** that inversion is complete: the model is a replaceable component, and the real engineering surface is the system around it — which you've built across four disciplines and can defend choice by choice.

The through-line is one question: **what happens around each model call, and across many calls, to turn a stateless probabilistic component into a reliable system?** The four disciplines are four answers. **Harness engineering** wraps a single call (prompting, structured output, tools, and — later — reliability and operations). **Loop engineering** drives many calls toward a goal and stops correctly. **Context engineering** decides what the model sees each call, out of everything it could. **Graph engineering** structures memory and control as graphs when flat lists break down. The capstone forces you to make every one of those choices at once, on a real task, and justify it.

## How to use this course

- **Rhythm:** one page per day, 30–45 min of focused reading, in order. Each page assumes the one before.
- **Exercises are non-optional** at this depth. Every page opens with a *retrieval* exercise: close the page and reconstruct the prior idea from memory *before* reading. It's the highest-value five minutes of your day.
- **The code compounds.** Day 5's `dispatch` feeds Day 8's loop; Day 10's assembler feeds Days 12–15; by Day 26 it's your capstone system. Keep one growing repo.
- **`bibliography.md`** is for when a page hooks you and you want the primary source — you don't need it to keep moving. **Rest & Synthesize days (16, 25)** add no material; they're pure retrieval, and where retention is won.
- **If life intervenes:** never skip the retrieval opener, even if you read nothing else.

## The four disciplines

| Discipline | The question it answers | Modules |
|---|---|---|
| **Harness engineering** | How do I wrap a model call so it's reliable, tool-capable, observable? | M1 (scaffold) · M5 (operations) |
| **Loop engineering** | How do I call the model repeatedly toward a goal, and stop correctly? | M2 (build) · M4 (control) |
| **Context engineering** | What do I put in front of the model each call, out of all I could? | M3 |
| **Graph engineering** | How do I structure memory and control as graphs when flat breaks? | M6 |

## The learning path

| Day | Module | Title | One idea |
|---|---|---|---|
| 1 | Foundations | [What Is AI Engineering?](modules/00-foundations/days/day-01-what-is-ai-engineering.md) | Engineering *around* a rented model is the discipline. |
| 2 | Foundations | [The Stateless Model & the Harness-as-OS](modules/00-foundations/days/day-02-stateless-model-and-harness.md) | LLM = stateless CPU; harness = its OS. |
| 3 | Harness · Scaffold | [Prompting as Programming](modules/01-harness-engineering-the-scaffold/days/day-03-prompting-as-programming.md) | A prompt is a spec you engineer, not a wish. |
| 4 | Harness · Scaffold | [Structured Output & the Parse Boundary](modules/01-harness-engineering-the-scaffold/days/day-04-structured-output.md) | Constrain to a schema; validate before trusting. |
| 5 | Harness · Scaffold | [Tools: The Model's Hands](modules/01-harness-engineering-the-scaffold/days/day-05-tools-the-models-hands.md) | A tool is a request the harness runs. |
| 6 | Harness · Scaffold | [The Tool Interface](modules/01-harness-engineering-the-scaffold/days/day-06-the-tool-interface.md) | The tool boundary is a trust boundary. |
| 7 | Loop · Build | [Why You Need a Loop](modules/02-loop-engineering-building-the-loop/days/day-07-why-you-need-a-loop.md) | Capability comes from iteration + feedback. |
| 8 | Loop · Build | [Your First Agentic Loop](modules/02-loop-engineering-building-the-loop/days/day-08-your-first-agentic-loop.md) | A working agent is ~60 lines you own. |
| 9 | Context | [Context Is Everything It Sees](modules/03-context-engineering/days/day-09-context-is-everything.md) | The model's whole mind is the payload you assemble. |
| 10 | Context | [Context Assembly](modules/03-context-engineering/days/day-10-context-assembly.md) | Rebuild the payload each turn: select → order → fit. |
| 11 | Context | [Drill I: Context Assembly](modules/03-context-engineering/days/day-11-drill-context-assembly.md) | Reps on eviction & placement under pressure. |
| 12 | Context | [Compaction & Lost-in-the-Middle](modules/03-context-engineering/days/day-12-compaction-lost-in-the-middle.md) | Compress, don't delete; place survivors well. |
| 13 | Context | [Context Engineering Patterns](modules/03-context-engineering/days/day-13-context-engineering-patterns.md) | A reusable catalog — agents, RAG, chat. |
| 14 | Context | [Memory & State Across Turns](modules/03-context-engineering/days/day-14-memory-and-state.md) | The loop is stateless unless you persist it. |
| 15 | Context | [Retrieval-Augmented Generation](modules/03-context-engineering/days/day-15-retrieval-augmented-generation.md) | Ground the model in external knowledge. |
| 16 | Context | [Rest & Synthesize I](modules/03-context-engineering/days/day-16-rest-synthesize-i.md) | Consolidate Modules 1–5 from memory. |
| 17 | Loop · Control | [Loop Control & Stopping](modules/04-loop-engineering-control-and-orchestration/days/day-17-loop-control-and-stopping.md) | A loop that can't decide it's done is a bug. |
| 18 | Loop · Control | [Drill II: The Turn, End-to-End](modules/04-loop-engineering-control-and-orchestration/days/day-18-drill-the-turn-end-to-end.md) | Fuse assembly + memory + compaction + control. |
| 19 | Loop · Control | [Multi-Agent Orchestration](modules/04-loop-engineering-control-and-orchestration/days/day-19-multi-agent-orchestration.md) | Split one loop into many only when context demands. |
| 20 | Harness · Ops | [Failure & Recovery](modules/05-harness-engineering-reliability-and-operations/days/day-20-failure-and-recovery.md) | Failure is an expected input, not an exception. |
| 21 | Harness · Ops | [The Evaluation Harness](modules/05-harness-engineering-reliability-and-operations/days/day-21-the-evaluation-harness.md) | You can't harden what you can't measure. |
| 22 | Harness · Ops | [Observability & Cost](modules/05-harness-engineering-reliability-and-operations/days/day-22-observability-and-cost.md) | Instrument first, then optimize. |
| 23 | Graph | [Context Graphs](modules/06-graph-engineering/days/day-23-context-graphs.md) | Store memory as a temporal graph you traverse. |
| 24 | Graph | [Control-Flow Graphs](modules/06-graph-engineering/days/day-24-control-flow-graphs.md) | Model the loop itself as a state graph. |
| 25 | Synthesis | [Rest & Synthesize II](modules/07-synthesis/days/day-25-rest-synthesize-ii.md) | Consolidate Modules 4–6 from memory. |
| 26 | Synthesis | [Capstone: Build & Harden an AI System](modules/07-synthesis/days/day-26-capstone-build-and-harden.md) | Assemble everything; defend every choice. |

Full path with rationale and spaced-callback logic: [`learning-path.md`](learning-path.md).

## The Course Shelf

Top five must-haves (full annotated list in [`bibliography.md`](bibliography.md)):

1. **Chip Huyen, *AI Engineering*, O'Reilly 2025** — the spine of the discipline: evaluation, context, retrieval, cost.
2. **Yao et al., "ReAct," ICLR 2023** (arXiv:2210.03629) — the loop's backbone.
3. **Packer et al., "MemGPT," 2023** (arXiv:2310.08560) — harness-as-OS and tiered memory.
4. **Anthropic, "Effective Context Engineering for AI Agents," 2025** — the context bottleneck, current best practice.
5. **Dex Horthy, "12-Factor Agents," AI Engineer 2025** ([talk](https://www.youtube.com/watch?v=8kMaTybvDUw)) — production harness/loop patterns.

## The capstone

On Day 26 you build and harden a real AI system: a coding agent that resolves a **SWE-bench-lite**–style GitHub issue (or a customer-support agent on a **τ-bench**–style task). "Done" means it runs a bounded, controlled loop against real tools; manages its own context under a token budget (with memory/RAG or a context graph where warranted); recovers from at least one injected failure; emits a replayable trace; and passes a small eval set you wrote — and you can justify every design choice against the alternatives the course rejected.

## Glossary

Key terms with first-use day: [`glossary.md`](glossary.md).

## Meta

- **Depth level:** L2 — Builder (build it yourself, debug the internals, harden for production).
- **Structure:** 26 days across 8 modules, organized by the four engineering disciplines.
- **Estimated total hours:** ~16–20 hours (26 pages × 35–45 min + capstone build).
- **Last updated:** 2026-09-04.
- **Feedback:** living notes — correct anything that reads wrong, and flag any source that won't resolve.
