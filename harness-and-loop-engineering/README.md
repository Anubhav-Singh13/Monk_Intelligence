# Harness & Loop Engineering

> **The promise:** After 18 days you can build a production-grade agent harness from scratch — the loop, tool boundary, context manager, compaction, control, recovery, evaluation, and observability — in a few hundred lines you can defend line by line.

## Who this course is for

You already call LLM APIs and have built with agent frameworks, but the **harness** — the code that turns a stateless next-token predictor into something that *does things* — is a black box. You're strong on models and math, lighter on the production orchestration code that wraps them. This course is **L2 (Builder)**: you will implement a working harness yourself and debug at the internals level, then harden it for production. It optimizes for one outcome — *build and defend your own agent runtime* — not for surveying frameworks.

Not for you if you want a LangChain/LangGraph tutorial (this course builds *under* those abstractions, not on top of them), or if you only want to use agents without understanding how they run. There is code on almost every page; skipping it defeats the point.

## The arc at a glance

On **Day 1** you can call an LLM API and use frameworks, but "the agent" and "the model" are the same thing in your head. By **Day 18** that inversion is complete: you see the model as a replaceable CPU and the harness as the real engineering surface, and you've written your own — loop, tool boundary, context manager, compaction, control, recovery, eval, observability — in a few hundred lines you can defend line by line.

The single story tying it together is **"what flows through the loop each turn?"** Every page is a different answer. First *why* there's a loop at all (Days 1–5), then *what you put into each turn* and how you keep it from rotting (Days 6–13 — the context/state bottleneck), then *how you keep the loop alive and honest under production load* (Days 14–17). The capstone forces you to make all those choices at once, on a real task, and justify each one.

## How to use this course

- **Rhythm:** one page per day, 30–45 min of focused reading. Do them in order — each page assumes the one before.
- **Exercises are non-optional** at this depth. Every page opens with a *retrieval* exercise: close the page and reconstruct yesterday's idea from memory *before* reading. This is the highest-value five minutes of your day; skipping it quietly wastes the course.
- **The "Try it yourself" code exercises are the spine of the build.** By Day 18 they compose into your capstone harness. Keep every day's code in one growing repo.
- **`bibliography.md`** is for when a page hooks you and you want the primary source. You don't need it to keep moving — the pages are self-contained. Consult it on the "if you have 15 extra minutes" prompt.
- **Rest & Synthesize days (Day 9)** add no new material. They are pure retrieval and consolidation. Do not skip them thinking they're filler — they're where retention is won.
- **If life gets in the way:** never skip the retrieval opener, even if you read nothing else that day. Missed a day? Do its retrieval opener plus the code exercise; you can defer the deep reading.

## The learning path

| Day | Title | One idea | Core source |
|---|---|---|---|
| 1 | [What Is a Harness?](days/day-01-what-is-a-harness.md) | An LLM is a stateless CPU; the harness is the OS around it. | Karpathy, *Software Is Changing (Again)* |
| 2 | [Why You Need a Loop](days/day-02-why-you-need-a-loop.md) | Agent intelligence comes from iterating, not one big call. | ReAct (arXiv:2210.03629) |
| 3 | [Tools: The Model's Hands](days/day-03-tools-the-models-hands.md) | A tool is a function the model can *ask* the harness to run. | Toolformer (arXiv:2302.04761) |
| 4 | [Context Is Everything It Sees](days/day-04-context-is-everything.md) | The model's whole mind each turn is the payload you assemble. | Lost in the Middle (arXiv:2307.03172) |
| 5 | [Your First Agentic Loop](days/day-05-your-first-agentic-loop.md) | A working ReAct agent is ~60 lines. | 12-Factor Agents (Horthy) |
| 6 | [The Tool Interface](days/day-06-the-tool-interface.md) | Treat model output as untrusted: schema → dispatch → parse. | Anthropic, *Writing Effective Tools* |
| 7 | [Context Assembly](days/day-07-context-assembly.md) | Each turn you *rebuild* the message list and decide what survives. | Anthropic, *Context Engineering* |
| 8 | [Drill I — Context Assembly](days/day-08-drill-context-assembly.md) | Reps: prune, order, budget a payload under token pressure. | — |
| 9 | [Rest & Synthesize I](days/day-09-rest-synthesize-i.md) | Consolidate the foundations arc from memory. | — |
| 10 | [Memory & State Across Turns](days/day-10-memory-and-state.md) | The loop is stateless unless you persist it deliberately. | MemGPT (arXiv:2310.08560) |
| 11 | [Compaction & Lost-in-the-Middle](days/day-11-compaction-lost-in-the-middle.md) | When history won't fit, *where* you cut changes what the model uses. | Lost in the Middle (arXiv:2307.03172) |
| 12 | [Loop Control & Stopping](days/day-12-loop-control-and-stopping.md) | A loop that can't decide it's done is a bug. | Anthropic, *Effective Harnesses* |
| 13 | [Drill II — The Turn, End-to-End](days/day-13-drill-the-turn-end-to-end.md) | Combine assembly + memory + compaction + control into one turn. | — |
| 14 | [Failure & Recovery](days/day-14-failure-and-recovery.md) | Production loops need retries, timeouts, circuit breakers, idempotency. | Nygard, *Release It!* |
| 15 | [The Evaluation Harness](days/day-15-the-evaluation-harness.md) | You can't harden what you can't measure; eval trajectories, not vibes. | Husain, *Your AI Product Needs Evals* |
| 16 | [Observability & Cost](days/day-16-observability-and-cost.md) | Instrument first, then optimize tokens, caching, and routing. | Huyen, *AI Engineering* |
| 17 | [Multi-Agent Orchestration](days/day-17-multi-agent-orchestration.md) | Split one loop into several only when context demands it. | AutoGen (arXiv:2308.08155) |
| 18 | [Capstone — Build & Harden](days/day-18-capstone-build-and-harden.md) | Assemble everything and defend every design choice. | Anthropic, *Building Effective Agents* |

## The Course Shelf

Top five must-haves (full annotated list in [`bibliography.md`](bibliography.md)):

1. **Yao et al., "ReAct," ICLR 2023** (arXiv:2210.03629) — the loop's backbone.
2. **Packer et al., "MemGPT," 2023** (arXiv:2310.08560) — harness-as-OS and tiered memory; the bottleneck's technical spine.
3. **Dex Horthy, "12-Factor Agents," AI Engineer 2025** ([talk](https://www.youtube.com/watch?v=8kMaTybvDUw)) — the most on-topic production-patterns talk in the field.
4. **Anthropic, "Effective Context Engineering for AI Agents," 2025** ([post](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)) — the context/state bottleneck, current best practice.
5. **Chip Huyen, *AI Engineering*, O'Reilly 2025** — spine for the production arc (eval, cost, observability).

## The capstone

On Day 18 you build and harden a real agent: a coding agent that resolves a **SWE-bench-lite**–style GitHub issue (or a customer-support agent on a τ-bench–style task if you prefer). "Done" means it runs a bounded loop against real tools, manages its own context under a token budget, recovers from at least one injected failure, emits a trace you can replay, and passes a small eval set you wrote yourself — and you can justify every design choice against the alternatives the course rejected.

## Glossary

Key terms with first-use day: [`glossary.md`](glossary.md).

## Meta

- **Depth level:** L2 — Builder (build it yourself, debug the internals, harden for production).
- **Estimated total hours:** ~11–14 hours (18 pages × 35–45 min + capstone build).
- **Last updated:** 2026-09-04.
- **Feedback:** these are living notes — correct anything that reads wrong, and flag any source that won't resolve.
