# Day 18 — Capstone: Build & Harden

> **Today's one idea:** A production agent is the *integration* of every prior day — and you're ready when you can build one on a real task and defend each design choice against the alternative you rejected.
> **Reading time:** ~45 min to read + a multi-hour build · **Prereqs:** all of Days 1–17
> **Primary source for today:** Anthropic, "Building Effective Agents," 2024 (architecture & patterns).
> **Before you start:** Recall Day 17's load-bearing idea — one sentence, no looking: *What are the only two legitimate reasons to split one agent into several, and what is the cost of every split?*

## The hook (2–4 min)

Seventeen days ago the harness was a black box and "the agent" meant "the model." Today you close the loop: you'll build a real agent, on a real task, that runs a bounded loop against real tools, manages its own context under budget, survives injected failures, emits a replayable trace, and passes an eval *you* wrote — and you'll be able to answer, for every part, *"why this and not the obvious alternative?"*

That last part is the real exam. Anyone can wire an API to a `while` loop. The L2 Builder can look at each line and say: *this budget is hierarchical because sub-agents multiply cost (Day 17); this constraint is pinned not recency-kept because recency drops old load-bearing facts (Day 7); this write is idempotent because retries double-apply mutations (Day 14).* The course was never about the code. It was about earning the *why* behind every line.

## The challenge

Build and harden **one agent** on **one** of these tracks. Both are real, both have objective verifiers (Day 15), both will expose every subsystem you built.

**Track A — Coding agent (SWE-bench-lite style).** Given a real GitHub issue and a repo, the agent inspects the code, edits files, runs the tests, and resolves the issue. Verifier: the repo's own test suite (green = done). This is the canonical harness task; SWE-bench is literally this.

**Track B — Support agent (τ-bench style).** Given a user request and a domain (retail/airline) with API tools and policy rules, the agent completes the transaction correctly *and* within policy. Verifier: final database state matches the goal state; policy violations auto-fail. This stresses tool boundaries, constraints, and multi-turn user interaction.

Pick the one closer to your work. The build below is written track-agnostic; adapt the tools and verifier.

## The specification — what "done" means

Your agent must demonstrably do all eight. Treat each as a checkbox with *evidence*, not a vibe:

```mermaid
mindmap
  root((Production<br/>Harness))
    Loop
      bounded turns
      observe-decide-act
    Tools
      schema + dispatch
      validated at boundary
    Context
      state vs context split
      assembled per turn
      compaction of the tail
    Memory
      exact-pin constraints
      semantic recall
    Control
      hierarchical budgets
      stuck-detection
      verified termination
    Resilience
      timeout + retry + breaker
      idempotent writes
    Evaluation
      >=10 real tasks
      trajectory graded, pass^k
    Observability
      span trace
      cost/latency breakdown
```

1. **Bounded loop** (Days 2, 5, 12): runs to a verified done or stops cleanly on a budget/stuck exit — never hangs, never loops forever.
2. **Safe tool boundary** (Days 3, 6): every tool has a schema; every call is validated; every outcome (success/error) is an observation; the agent is confined (workspace/policy).
3. **Engineered context** (Days 4, 7, 8, 11): `assemble_context` selects, orders (U-curve), and fits under a budget; the stable head is cache-friendly and the volatile tail is compacted.
4. **Two-tier memory** (Day 10): standing constraints pinned in exact memory; durable facts in recallable memory; nothing load-bearing lost to recency.
5. **Honest control** (Day 12): hierarchical budgets (turns/tokens/cost/time), stuck-detection, and *verified* termination (a rejected "done" resumes work).
6. **Production resilience** (Day 14): timeouts, classified retries with backoff, a circuit breaker, and idempotent mutations — survives an injected chaos-monkey.
7. **A real eval** (Day 15): ≥10 tasks from real cases, code-graded, run k≥3, reporting success + pass^k + turns/cost/latency.
8. **A replayable trace** (Day 16): span-level tracing with a cost/latency breakdown; you can explain where every dollar and second went and replay a failed run.

## The build (your "Try it yourself" — this is the whole day)

This is the direct-application exercise, in real conditions. Work in your course repo; you've written most of these pieces across Days 3–17 — the capstone is *integration and defense*, not greenfield.

**Phase 1 — Skeleton (compose what you have).** Wire your Day 13 `turn()` — the four separated state slices (transcript / durable memory / control-accounting / this-turn context) — around your chosen track's tools and verifier. Get one task passing end to end. *Checkpoint: it solves one real task and stops cleanly on an unsolvable one.*

**Phase 2 — Harden (make it survive).** Add resilience (Day 14): wrap tool and model calls in `resilient_call` with a circuit breaker; make every mutating tool idempotent. Inject failures (a flaky tool, an overloaded model response, a permanently-down dependency) and confirm the loop never crashes and recovers or escalates honestly. *Checkpoint: passes a chaos-monkey run.*

**Phase 3 — Measure (stop guessing).** Build the eval (Day 15): ≥10 real tasks with objective graders, run k≥3. Add the tracer (Day 16). Produce a baseline: success, pass^k, avg turns/cost/latency, and a cost breakdown. *Checkpoint: one number that summarizes your agent's quality, and a trace that explains its cost.*

**Phase 4 — Optimize (surgery, not superstition).** Read your trace, find the biggest cost/latency line item, apply the *one* matching fix (almost certainly: cache the stable head, compact the tail — Day 16), and re-run the eval to confirm cost dropped *without* success dropping. Make one more measured improvement of your choice. *Checkpoint: a measured before/after on cost AND success.*

**Phase 5 — Decide on multi-agent (Day 17).** Identify whether your task has a context-isolated or parallelizable subtask. If yes, split it out as a sub-agent-tool with a hierarchical budget and A/B it against the single-loop version on your eval. If no, *write one paragraph justifying why a single loop is correct here.* Either answer is right; the *reasoning* is graded.

## The defense — the real exam (retrieval, cold)

Close every page. Answer these from memory, in writing, as if defending the design to a skeptical senior engineer. Each question pairs a choice with the alternative you rejected — name *both* and say *why*. These are the spaced callbacks to the whole course; if any is shaky, that's your last diagnostic before you're done.

1. **(Day 1)** Your agent "remembers" 30 turns of work, yet you insist the model is stateless. Reconcile these precisely. *Alternative rejected: "the model has memory."*
2. **(Day 4 + 7)** You send `assemble_context(state)`, not `messages=history`. Give the two distinct failures the naïve version causes and which each subsystem fixes. *Alternative rejected: "just use the big context window."*
3. **(Day 6)** The provider's native tool calling parsed your tool arguments. Why do you *still* validate them, and where? *Alternative rejected: "the API validated it."*
4. **(Day 10 + 11)** A constraint stated on turn 2 still governs turn 40. Explain the two different mechanisms that could preserve it and which you chose for constraints vs. narrative. *Alternative rejected: "keep the last N turns."*
5. **(Day 12 + 15)** Your agent said "done." Give the two reasons you don't believe it and what the harness does about each. *Alternative rejected: "trust the model's completion."*
6. **(Day 14)** Your retry logic and one of your tools interact dangerously unless you did one specific thing. Name the bug and the fix, and whose design decision it was. *Alternative rejected: "just retry on any failure."*
7. **(Day 16)** Cost is $X/run. Walk the method that finds the right optimization, and why "use a cheaper model" is the wrong first move. *Alternative rejected: "smaller model = cheaper."*
8. **(Day 17)** Justify your single-loop-vs-multi-agent decision with the two-and-only-two legitimate reasons to split. *Alternative rejected: "more agents = smarter."*

<details><summary>Model answers (check yourself only after writing all eight)</summary>

1. The *model* retains nothing between calls (pure `tokens_in→tokens_out`); the *harness* reconstructs a curated view of the past into context each turn. "The agent remembers" = "the harness re-shows the model its relevant history." Memory is a harness responsibility, not a model property.
2. `messages=history` **overflows** the finite window `L` (fixed by compaction/eviction — Days 8, 11) *and* buries the current goal in the low-attention **middle** (fixed by placement/re-pinning — Days 4, 7). Two different failures, two different fixes; both invisible in a demo, fatal by turn 30.
3. Native tool calling validates *shape*, not *values*; `block.input` is untrusted model output (e.g. `path="../../etc/passwd"`). Validation lives in the harness at the tool boundary, as deterministic, testable code — the model can't be a security control.
4. **Exact-pin** (Day 10): store the constraint in key-value memory, always injected in a strong position regardless of age — best for hard constraints that must not drift. **Running summary** (Day 11): fold it into the digest — fine for narrative, unreliable for hard constraints (prose can drop it). Choose exact-pin for constraints, summary for the story.
5. It could be a **hallucinated completion** (fixed by objective *verification* — run the tests/check state, Day 12) and it could be **a fluke that won't reproduce** (fixed by *pass^k* over k runs, Day 15). A rejected claim becomes a "verification failed" observation and the loop resumes.
6. Retrying a **non-idempotent write** after a lost-ack timeout **double-applies** the mutation (duplicate record). Fix: idempotency key derived from the intended action, checked before applying — and it's a **Day 6 tool-design** decision, not a retry-layer patch, because only the tool knows whether "do X twice" is intentional.
7. **Trace first** (span-level cost breakdown), find the biggest line item — for agents usually *repeated input tokens* re-sent every turn — then cache the stable prefix. "Cheaper model" is wrong first because per-token price isn't the bottleneck (repeated input is), and a weaker model can *raise* cost by needing more turns. Validate any cut against the eval's success/pass^k.
8. Split *only* for **context isolation** (keep a subtask's mess out of the main window) or **parallelism** (independent subtasks concurrently for latency). Every split adds a lossy serialized handoff; "more agents = smarter" ignores that agents play telephone across boundaries and multiply cost. If subtasks need each other's *working context*, it's one loop.
</details>

## What "done" looks like (rubric)

You've completed the course when your agent:

- [ ] Solves ≥1 real task on your track, end to end, with a verified outcome.
- [ ] Stops cleanly (budget/stuck/verified-done) on every run — you cannot make it hang or loop forever.
- [ ] Survives a chaos-monkey run (injected transient + permanent + down-dependency failures) without crashing, and doesn't double-apply any write.
- [ ] Reports an eval score over ≥10 tasks at k≥3: success, pass^k, avg turns/cost/latency.
- [ ] Produces a trace you can read to explain its cost, and you made ≥1 *measured* optimization (cost down, success held).
- [ ] Comes with your written answers to all eight defense questions, each naming the rejected alternative.

If every box is checked and every defense answer holds, you have done the thing the course promised on Day 1: **built a production-grade harness from scratch and can defend every design choice against the alternatives.** The model is the CPU; you built the OS.

> **Transfer — apply it:** This is the most direct transfer possible — the capstone *is* your domain if you chose your track to match your work. Take the agent you built and name the one subsystem you'd invest in first for *your actual production use case*, and the one metric on your eval you'd hold the line on. Write it down; it's your next week's roadmap.

## Connect it back

Day 1 you learned the model is a stateless CPU and the harness is the OS; every day since added one organ — a loop (2), hands (3), eyes (4), a body (5), a safe boundary (6), memory management (7–11), a nervous system of control (12–13), an immune system (14), instruments (15–16), and the judgment to know when one body is enough (17). Today you assembled the whole organism and proved it lives under production load. There's no "next day" — there's the frontier: longer-horizon agents, learned control policies, better verifiers, agents that improve their own harness. You now have the one thing that makes that frontier legible: you know what's actually running when an agent runs. The final question, which you couldn't answer 18 days ago and can now answer cold: *given a stateless next-token predictor, what exactly must you build around it to make it reliably do real work — and why each piece?*

## Suggested readings for today

**Required if you have 15 extra minutes:** Anthropic, "Building Effective Agents," 2024 — [link](https://www.anthropic.com/engineering/building-effective-agents). Read it *now*, at the end — it will read completely differently than it would have on Day 1. Every pattern it names, you've now built and can critique.

**If you want the deep version (the frontier):**
- Jimenez et al., "SWE-bench," arXiv:2310.06770 — if you took Track A, run against the real benchmark and compare to the leaderboard.
- Yao et al., "τ-bench," arXiv:2406.12045 — if you took Track B, the real harness and pass^k tasks.
- Dex Horthy, "12-Factor Agents" — [talk](https://www.youtube.com/watch?v=8kMaTybvDUw)/[repo](https://github.com/humanlayer/12-factor-agents) — re-read all twelve factors; you've now independently derived every one. That's the sign you're done.
- Sumers et al., "CoALA," arXiv:2309.02427 — the research frame for where agent architectures go next.

---

## Navigation

← **Previous:** [Day 17 — Multi-Agent Orchestration](day-17-multi-agent-orchestration.md)  
→ **Course complete:** [Back to the README](../README.md) · [Learning path](../learning-path.md) · [Bibliography](../bibliography.md)
