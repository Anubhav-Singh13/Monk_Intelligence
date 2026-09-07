# Day 25 — Rest & Synthesize II

> **Today:** No new concepts. Only consolidation of the **control, operations & graph arc** (Days 17–24).
> **Format:** Re-state → Re-derive → Full run → Quiz
> **Reading time:** ~35 min (longer if gaps surface — that's the point)
> **Prereqs:** [Days 17–24](../../../README.md)

The back half of the course turned a working agent into a *production* one — controlled, resilient, measured — and then gave you graph tools for when flat structures break down. Today you verify it holds in memory before the capstone assembles everything.

Close all previous pages. Work from memory first. A blank is a diagnostic, not a failure.

---

## Step 1 — Re-state the 7 back-half ideas (5 min)

> **Why this table is not in day order:** day-order lets you recite by sequence instead of retrieving each idea on its own. The scrambled order forces genuine recall. Resist reordering.

Write one sentence for each, without looking:

| Concept (Day) | Your sentence |
|---|---|
| Control-flow graphs (Day 24) | |
| The evaluation harness (Day 21) | |
| Multi-agent orchestration (Day 19) | |
| Context graphs (Day 23) | |
| Loop control & stopping (Day 17) | |
| Observability & cost (Day 22) | |
| Failure & recovery (Day 20) | |

<details><summary>Compare to these</summary>

1. **Control-flow graphs (Day 24):** when a loop's branching gets complex, make control explicit as a graph of nodes (steps) and conditional edges (transitions), gaining inspectability, checkpoint/resume durability, and composability.
2. **Evaluation harness (Day 21):** you can't harden what you can't measure — run the agent over a frozen task set with automatic graders (trajectory + outcome), producing comparable metrics instead of vibes.
3. **Multi-agent orchestration (Day 19):** split one loop into several with delegated, isolated context only when context demands it — and usually you shouldn't, because coordination adds cost and failure modes.
4. **Context graphs (Day 23):** store memory as a temporal graph of entities/relations so recall can traverse connections (multi-hop) and respect time (supersession), fixing flat retrieval's blind spots.
5. **Loop control & stopping (Day 17):** a loop that can't decide it's done is a bug — enforce budgets, detect stuck/no-progress, and *verify* completion claims before accepting them, all in the harness.
6. **Observability & cost (Day 22):** instrument first (spans/traces of each turn), then optimize — token budgets, caching, model routing — because you can't cut what you can't see.
7. **Failure & recovery (Day 20):** treat failure as an expected input — classify transient vs. permanent, retry-with-backoff, circuit-break dead tools, make writes idempotent, and turn every error into an observation, not a crash.
</details>

---

## Step 2 — Re-derive the production turn (10 min)

Fill the blanks: one turn of a *production-grade* agent, from memory. Each blank names what it wants.

```
turn:
    if budget.exceeded():        return escalate(...)   # control checks run ____[A] the model call, why? ____[B]
    if is_stuck(recent):         return intervene(...)
    if over_budget(state):       compact_into(memory)   # which state slice is compacted? ____[C]
    msgs = assemble_context(state, memory)
    resp = resilient_call(model, msgs)                  # resilient_call wraps the call with ____[D] (4 things)
    budget.charge(resp.usage)                            # counts ____[E] usage, not estimates
    if resp.claims_done:
        ok = verify(...)                                 # why verify a 'done' claim? ____[F]
        return success() if ok else keep_going()
    for call in resp.tool_calls:
        obs = dispatch(call.name, call.args)             # dispatch treats args as ____[G]
    emit_span(turn, tokens, cost, latency)               # this is ____[H], done before optimizing
```

<details><summary>Filled-in</summary>

- **[A]** *before* the model call.
- **[B]** to avoid spending tokens/cost/a call when you're already out of budget or stuck — you guard the resource before consuming it.
- **[C]** the *transcript* slice (high-volume, low-density); control/accounting and durable memory are not compacted (Day 18).
- **[D]** timeout, failure classification (transient vs. permanent), backoff retry, and a circuit breaker (Day 20).
- **[E]** *real* usage from the API response (tokens/cost), not estimates.
- **[F]** the model's "done" is a *claim*, not a fact — it can be wrong/lie; verify objectively (run tests / re-check state) where possible (Day 17).
- **[G]** untrusted input — validate before executing (Day 6, carried through).
- **[H]** observability / a trace span — you instrument first, then optimize (Day 22).
</details>

**Questions to answer without looking** (mechanisms, not definitions):

1. Why do budget and stuck checks run *before* the model call but verification *after*?
2. When would you split one agent into several, and what's the main cost you pay for doing so?
3. What's the difference between retrying a *transient* failure and surfacing a *permanent* one, and why does the split matter?
4. Why is a control-flow graph *resumable* when a raw `while` loop isn't?
5. What does a context graph recall that a flat vector store can't, and why?

<details><summary>Answers</summary>

1. Control guards the resource *before* you spend it (don't pay for a call you shouldn't make); verification evaluates *this turn's* completion claim, which doesn't exist until after the model responds.
2. Split when a subtask needs its *own isolated, clean context* (or genuinely parallel work); cost = coordination overhead, extra latency/tokens, and new failure modes at the boundaries — often not worth it.
3. Transient (timeout/529) → retry with backoff (it'll likely succeed); permanent (bad args/404/auth) → surface as an observation so the *model* adapts (retrying would fail identically). The split avoids both wasted retries and swallowed information.
4. Because a graph's position is an explicit `(node, state)` you can checkpoint and reload; a `while` loop's position is an ephemeral program counter that's lost on crash.
5. Multi-hop answers (facts connected by relationships, not in any one chunk) and current-vs-stale facts (temporal edges mark superseded facts), because it stores typed relations and validity intervals, not isolated points.
</details>

---

## Step 3 — Run / apply it (10 min)

Verify the production machinery works end to end. Check off:

- [ ] Your agent stops on *stuck* (repeated no-progress) well before `max_turns`, and on a false "done" its verifier rejects and it continues.
- [ ] Budgets count *real* token/cost usage each turn and stop at the first ceiling hit (often cost/time, not turns).
- [ ] `resilient_call` recovers a transient failure via retry, trips a breaker on a dead tool (fast-fail), and surfaces a permanent error to the model — process never crashes.
- [ ] You can produce a *trace* of a run (per-turn tokens, cost, latency, tool calls) and replay/read it.
- [ ] Your eval harness scores the agent on a small frozen task set and gives you a repeatable number.
- [ ] (Graph) You can express the loop as nodes+edges and resume it from a checkpoint after a kill.

Any unchecked box is today's homework — fix it before the capstone, where all of these run at once.

---

## Step 4 — Quiz (5 min)

> **Why the questions jump around:** they cross concept boundaries deliberately. Answer each cold.

1. Your agent has become a tangle of nested `if/break`s and you need it to pause for human approval mid-run and resume tomorrow. What structural change enables this, and why can't a plain loop do it? *(Day 24)*
2. A tool times out; your harness retries and a duplicate record appears in the database. Which two mechanisms failed, and whose *design decision* (which earlier day) is the root cause? *(Day 20)*
3. You want to cut your agent's cost by 40%. What must you do *before* changing anything, and name two levers you'd then reach for. *(Day 22)*
4. Your agent passes your eval set but a stakeholder says it "feels worse" than last week. Reconcile — and say what this reveals about eval sets. *(Day 21 × Day 19)*
5. **Cross-concept:** You're building a long-running research agent that must (a) run for hours with human check-ins, (b) remember evolving facts across sessions, and (c) not hallucinate stale conclusions. Name which *graph* solves each of (a)–(c), and explain why control-flow graphs and context graphs are *different* graphs doing *different* jobs — not the same tool. *(Day 23 × Day 24)*

<details><summary>Answers</summary>

1. Express control as an explicit **control-flow graph** (nodes + conditional edges + shared state) with **checkpointing** after each node. A plain `while` loop's position is an ephemeral program counter lost on pause/crash; a graph's position is a saved `(node, state)` you can persist and resume, and a `human` node can halt the graph and route back in when the answer arrives.
2. **Retry** (retried a non-idempotent write) and **idempotency** (the write wasn't safe to run twice) failed. Root cause is a **Day 6** tool-design decision: idempotency is a property you build into a mutating tool at the boundary (via idempotency keys / check-then-act); it can't be bolted on in the retry layer, which can't tell a legitimate second write from a duplicate.
3. **Before:** instrument — get a trace/breakdown of where tokens and cost actually go per turn (Day 22); you can't optimize what you haven't measured, and the cost is usually concentrated somewhere surprising. **Then** levers: prompt/context **caching** (reuse a stable prefix), **model routing** (cheap model for easy turns, strong for hard), tighter **context budgets** (fewer tokens/turn via compaction), and reducing turns via better control.
4. Both can be true: the eval set measures *specific* tasks, and the agent can pass those while regressing on cases the set doesn't cover (or on subjective quality the graders don't capture). It reveals that an eval set is a *proxy* — only as good as its coverage — so "passes eval" ≠ "is good"; you expand the set with the newly-surfaced failure cases (and note multi-turn/subjective quality is hard to score, which is why some regressions escape). Eval is necessary, not sufficient.
5. **(a) run for hours with human check-ins → control-flow graph** (Day 24): checkpointable nodes + a human-in-the-loop node give durability and pause/resume. **(b) remember evolving facts across sessions → context graph** (Day 23): a temporal knowledge graph persists entities/relations across runs. **(c) not hallucinate stale conclusions → context graph's temporal edges** (Day 23): supersession/validity intervals mean recall returns the *current* fact, not an out-of-date one. They're **different graphs doing different jobs**: a *control-flow* graph structures *what the agent does* (steps and transitions over time within a run); a *context* graph structures *what the agent knows* (facts and their relations/validity). One is computation/control; the other is memory/knowledge. Conflating them (one giant graph) re-creates the tangle both were meant to prevent — control and memory want separate, clean structures.
</details>

---

## What's ahead

| Day | Topic | What you'll do |
|---|---|---|
| 26 | Capstone — Build & Harden an AI System | Assemble everything — harness, loop, context, memory/RAG, graphs, control, reliability, eval — into one real agent and defend every choice |

Today closed the gap between "I read the production arc" and "I can rebuild it cold." It opens the capstone: you now hold all four disciplines and their production hardening; tomorrow you compose them under real constraints.

---

## Connect it back

The back half gave you control, resilience, measurement, and graph structures — the difference between a demo and something you'd put in front of users. The single sharp question you carry into the capstone: *given a real task, which of the four disciplines does it stress most, and what is the smallest system that solves it while staying controlled, measured, and recoverable?*

---

## Navigation

← **Previous:** [Day 24 — Control-Flow Graphs](../../06-graph-engineering/days/day-24-control-flow-graphs.md)  
→ **Next:** [Day 26 — Capstone: Build & Harden an AI System](day-26-capstone-build-and-harden.md)
