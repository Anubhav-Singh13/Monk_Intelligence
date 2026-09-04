# Day 9 — Rest & Synthesize I

> **Today:** No new concepts. Only consolidation of the **foundations arc** (Days 1–8).
> **Format:** Re-state → Re-derive → Full run → Quiz
> **Reading time:** ~35 min (longer if gaps surface — that's the point)
> **Prereqs:** [Days 1–8](../README.md)

The foundations arc built a complete, safe agent and named the one problem that will dominate the rest of the course: managing what flows through the loop. Today you verify it holds together *in your memory*, not just on the page, before we add memory and state.

Close all previous pages. Work from memory first. The discomfort of a blank is the signal telling you exactly what to re-study.

---

## Step 1 — Re-state the 7 foundations ideas (5 min)

> **Why this table is not in day order:** recalling Day 1, then Day 2, then Day 3 lets you ride sequence as a crutch — you'd be reciting the order you learned, not retrieving each idea on its own. The scrambled order below forces each concept to stand alone. Resist reordering.

Write one sentence for each, without looking anything up. Do them top to bottom as listed:

| Concept (Day) | Your sentence |
|---|---|
| Context assembly: state vs. context (Day 7) | |
| Tools are requests the harness runs (Day 3) | |
| Harness = OS; model = stateless CPU (Day 1) | |
| The smallest complete agent is a loop (Day 5) | |
| Why iteration beats a bigger model (Day 2) | |
| The tool boundary is a trust boundary (Day 6) | |
| Context is everything the model sees (Day 4) | |

<details><summary>Compare to these</summary>

1. **Context assembly (Day 7):** *State* is the full growing record in the harness; *context* is the budgeted, ordered view you rebuild each turn (select → order → fit). State is the database, context is the query result.
2. **Tools (Day 3):** A tool is a schema + implementation + binding; the model only *emits a request*, and the harness executes the real function and feeds the result back as an observation.
3. **Harness = OS (Day 1):** The LLM is a stateless pure function (`tokens_in → tokens_out`); the harness is the OS around it, supplying memory, hands (tools), a clock (the loop), and protection. All state lives in the harness.
4. **Smallest agent (Day 5):** A working agent is a `while` loop — assemble → call → decide (done vs. tool) → dispatch → append — where the `history` list you own *is* the agent's entire memory.
5. **Iteration beats size (Day 2):** Capability comes from observe→decide→act→observe with feedback accumulating in state, not from any single call; many sighted decisions beat one blind one.
6. **Trust boundary (Day 6):** Model output is untrusted input, so the tool path is describe → parse → *validate* → dispatch, with validation in the harness (deterministic code), and every outcome returned as an observation rather than a crash.
7. **Context is everything (Day 4):** The model's whole mind on a turn is the assembled payload; it's finite (limit `L`) and positional (Lost-in-the-Middle: strong start/end, weak middle), so more context can mean worse performance.
</details>

---

## Step 2 — Re-derive the end-to-end flow (10 min)

Fill the blanks in one turn of a *safe, context-managed* agent loop, from memory. Each blank names what it wants.

```
state = { system, task, turns[], tools }          # the full record (lives in the ______[A])

one turn:
    system, tools, messages = __________(state)    # [B] name this function; its 3 jobs are __,__,__ [C]
    response = model(system, tools, messages)      # the model is ______ [D] — it remembers nothing
    append response to state.turns                 # who owns this list? ______ [E]
    if response is a final answer:  return it      # this check is ______ [F] (a whole later day)
    for each tool_call in response:
        obs = dispatch(call.name, call.args)       # before running, dispatch must ______ [G] the args
        append obs to state.turns                  # every outcome (success/error) becomes an ______ [H]
    # loop guard: stop after ______ [I] to prevent runaway
```

<details><summary>Filled-in</summary>

- **[A]** harness (not the model — the model is stateless).
- **[B]** `assemble_context`.
- **[C]** select, order, fit (in priority order).
- **[D]** stateless (a pure function of its input tokens).
- **[E]** the harness / your loop owns `state.turns`; it's the agent's memory.
- **[F]** loop control / termination (Day 12).
- **[G]** validate (shape + values; e.g. path confinement) — treat as untrusted input.
- **[H]** observation (fed back into context; never crash the loop).
- **[I]** `max_turns` (a turn budget) — the runaway guard.
</details>

**Questions to answer without looking** (mechanisms, not definitions):

1. Why must `assemble_context` run *every* turn rather than once at the start?
2. In the U-shaped attention curve, where do you place the task and the latest observation, and why?
3. When the budget forces an eviction, what three things never get dropped?
4. Why is "the API's native tool calling parsed the arguments" not sufficient before dispatch?
5. What is the single Day 5 line that guarantees you'll need Days 7–13, and what does it do wrong?

<details><summary>Answers</summary>

1. Because the model is stateless (rebuilt anyway) *and* what it needs to see changes as the task progresses — per-turn assembly adapts and stays under budget; a one-time build overflows and rots.
2. Task and latest observation go in the **strong positions** — task re-pinned near the start, latest observation at the end — because the middle is under-attended, and those two are what the model most needs sharp.
3. System prompt, task/goal, and the latest observation.
4. Native tool calling validates *shape*, not *meaning*; the argument *values* are still untrusted (e.g. `path="../../etc/passwd"`), so the harness must validate values at the boundary.
5. `messages=history` — it sends the entire state forever, which overflows the finite window and buries the goal in the weak middle (context rot).
</details>

---

## Step 3 — Run it (10 min)

Hands-on verification that your foundations code actually works end to end. Check off each:

- [ ] Your Day 5 loop runs a multi-step task to completion and stops cleanly on `max_turns` when given an unsolvable one.
- [ ] Your `dispatch` (Day 6) returns a clean observation for: unknown tool, missing arg, path-escape attempt, and a thrown exception — **no crash** on any.
- [ ] `_safe_path` rejects `../` escapes and accepts legitimate relative paths (you can prove this with **zero** LLM calls — it's plain code).
- [ ] Your Day 7 `assemble_context` keeps total tokens *plateauing* near a budget instead of climbing unbounded across a 12+ turn run.
- [ ] You can point at the exact line where state becomes context, and the exact line where a model request becomes a real action.

If any box fails, that's your diagnostic — fix it today before moving on. A shaky foundation compounds into undebuggable behavior once memory (Day 10) and control (Day 12) pile on.

---

## Step 4 — Quiz (5 min)

> **Why the questions jump around:** day-ordered questions let you answer by position. These cross boundaries deliberately. Answer each cold, before looking.

1. Your validated `dispatch` still lets the agent read a secret file the user said to avoid, because the "don't read it" instruction was 15 turns ago and got evicted. Whose failure is this — the tool boundary or context assembly — and what's the fix? *(Day 6 × Day 7)*
2. A colleague says "our agent is weak; let's upgrade to a bigger model." Give the one-sentence reason this may not help, grounded in where agent capability comes from. *(Day 2)*
3. You include a critical fact in a 150K-token context and the model ignores it. Give the most likely mechanical reason and the cheapest fix. *(Day 4)*
4. The model is stateless, yet your agent clearly "remembers" what it did ten turns ago. Reconcile these two facts precisely. *(Day 1 × Day 5)*
5. **Cross-concept:** Explain how Day 4's Lost-in-the-Middle result *directly constrains* the design of Day 7's `assemble_context` — name two specific assembly decisions that would be different if attention were uniform across the context window.

<details><summary>Answers</summary>

1. **Context assembly's** failure, not the tool boundary's. The boundary correctly ran a validated call; the problem is the *standing constraint* fell out of the recency window. Fix: **pin** standing constraints as always-included/important, independent of recency (Day 7 stretch → Day 10 memory).
2. Capability comes mostly from *iteration with feedback*, not single-call quality; if the agent is weak because it can't gather/verify information across steps, a bigger model gives better *blind* single answers but doesn't fix the loop.
3. Mechanism: the fact is likely in the low-attention **middle** of a large context (Lost-in-the-Middle). Cheapest fix: move it to a **strong position** — re-pin it near the start (system) or the end — rather than adding more tokens or a sterner prompt.
4. The *model* remembers nothing; the *harness* reconstructs the memory each turn by assembling prior turns into the context it sends. "The agent remembers" = "the harness re-shows the model a curated view of its past." Statelessness is about the model function; memory is a harness responsibility.
5. If attention were uniform, position would be irrelevant, so `assemble_context` would **(a)** not bother placing the task/goal and latest observation in start/end "strong" slots (any position would do), and **(b)** not re-pin the goal near the top of long runs (no drift into a "dead middle" to counteract). Because attention is *not* uniform (U-shaped), assembly must both order by positional utility and actively re-pin high-priority items — two decisions that exist *only* because of the Day 4 result. (Eviction under budget still exists either way — that's driven by finiteness, not position.)
</details>

---

## What's ahead

| Day | Topic | What you'll decide or build |
|---|---|---|
| 10 | Memory & State | How to persist facts *outside* the context window and retrieve them (fixes recency-only) |
| 11 | Compaction | How to *shrink* what you keep instead of dropping it (summaries, not deletions) |
| 12 | Loop Control | How the loop decides it's done — and refuses to run forever |

Today closed the gap between "I read the foundations" and "I can rebuild them cold." It opens the next arc: you've mastered *selecting and ordering* context, but you're still only working with *recent history*. Days 10–13 give you the other half — durable memory, compression, and the control that keeps a long loop coherent.

---

## Connect it back

The foundations arc gave you a safe, looping, context-aware agent you can build from a blank file and defend line by line. The single sharp question you carry into the memory arc: *if context is only ever a budgeted view of state, where does state live when it's too big or too important to keep re-showing — and how does the agent get it back exactly when it's needed?*

---

← [Day 8 — Drill I: Context Assembly](day-08-drill-context-assembly.md) &nbsp;|&nbsp; [Day 10 — Memory & State Across Turns →](day-10-memory-and-state.md)
