# Day 18 — Drill II: The Turn, End-to-End

> **Today:** No new concepts. Reps that fuse the whole bottleneck — assembly + memory + compaction + control — into one coherent turn under pressure.
> **Reading time:** ~45 min (the hardest gym day; expect to sweat)
> **Prereqs:** [Day 10](../../03-context-engineering/days/day-10-context-assembly.md), [Day 14](../../03-context-engineering/days/day-14-memory-and-state.md), [Day 12](../../03-context-engineering/days/day-12-compaction-lost-in-the-middle.md), [Day 17](day-17-loop-control-and-stopping.md)
> **Before you start:** Recall Day 17's load-bearing idea — one sentence, no looking: *the five ways a turn can end, and the three harness controls that catch the non-success ones?*

Days 10–17 gave you four subsystems. In a real agent they don't run in isolation — they all fire on *every single turn*, in a specific order, sharing state. Today you assemble them into one `turn()` function and then stress it with eight exercises that get progressively nastier. This is the integration your capstone rests on. Attempt each before opening the solution. If you're comfortable, you're going too easy.

The reference turn you're drilling (hold this in your head):

```python
def turn(state, memory, budget, verifier):
    # 1. CONTROL: check exits BEFORE spending a call
    if (why := budget.exceeded()):     return exit_budget(state, why)
    if is_stuck(state["recent"]):      return exit_stuck(state)
    # 2. COMPACTION: if state is over budget, shrink it (writes distilled detail to memory)
    if over_budget(state, budget):     compact_into_memory(state, memory)
    # 3. ASSEMBLY: build this turn's payload (recency + retrieved memory), placed by U-curve
    sys, tools, msgs = assemble_context(state, memory, budget)
    # 4. MODEL CALL
    resp = model(sys, tools, msgs); budget.charge(resp.usage)
    state["turns"].append(resp)
    # 5. TERMINATION: verify a 'done' claim before accepting it
    if resp.claims_done:
        ok, ev = verify(verifier, state)
        return success(state) if ok else keep_going(state, ev)
    # 6. ACT: dispatch tools (validated), append observations, remember durable facts
    for call in resp.tool_calls:
        state["recent"].append(call)
        obs = dispatch(call.name, call.args)
        state["turns"].append(observation(obs))
        maybe_remember(memory, call, obs)     # write durable signal to long-term memory
    return continue_loop(state)
```

---

### Drill 1 — Order of operations (warm-up)

Why does **control** (budget/stuck checks) run *before* the model call, but **termination-verification** run *after*? And why does **compaction** run before **assembly**, not after?

<details><summary>Answer</summary>Control runs **before** the call because the whole point is to *avoid spending* a call (and its tokens/cost) when you're already out of budget or stuck — checking after would waste the exact resource you're guarding. Verification runs **after** because it evaluates *this turn's* completion claim, which doesn't exist until the model has responded. Compaction runs **before** assembly because assembly builds the payload *from* state — if state is over budget, you must shrink it first so assembly has a fitting, high-signal set to select from; compacting after assembly would mean assembling an oversized payload you then have to rebuild. The ordering encodes data dependencies: guard → shrink → select → call → verify → act.</details>

### Drill 2 — Trace the token lifecycle

Follow one 1,800-token file read from the moment a tool returns it to the moment (10 turns later) it's gone from context but not lost. Name every subsystem it passes through.

<details><summary>Answer</summary>(1) **Act/dispatch** (Day 6): tool returns 1,800 tokens; appended to `state["turns"]` as an observation. (2) **Assembly** (Day 10): for the next few turns it's in the recency window, included in context. (3) **Compaction — extract** (Day 12): as it ages and budget tightens, `extract_key_lines` trims it to the ~40 signal lines. (4) **Compaction — summarize + memory-write** (Days 12/14): when it ages out of the recency window, its signal is folded into the running summary *and* `remember_note` writes a distilled version to long-term memory. (5) **Eviction** (Day 11): the raw 1,800-token turn is dropped from `state["turns"]`. (6) **Recall** (Day 14): 10 turns later, if the current step is relevant, `memory.recall` pages the distilled note back into context in a strong position. The token's *bulk* died at step 5; its *signal* survives in memory and returns on demand. That full path — context → extract → summarize → memory → recall — is the whole bottleneck in one trace.</details>

### Drill 3 — The interaction bug (integration)

Compaction summarizes turns 1–10 into a digest and drops them from `state["turns"]`. But `is_stuck` (Day 17) inspects `state["recent"]` actions to detect repetition. What breaks, and how do you fix the *interaction* without weakening either subsystem?

<details><summary>Answer</summary>What breaks: if `state["recent"]` is derived from `state["turns"]` and compaction drops old turns, stuck-detection *loses its history* — right after a compaction it sees only a few recent actions and can't detect a repetition pattern that spans the compaction boundary (agent was looping turns 8–14; compaction at turn 12 erases 8–11, so at turn 14 it looks fresh). Fix: **keep control's bookkeeping separate from the compactable transcript.** Maintain `state["recent"]` (and budget counters) as their *own* small, bounded, non-compacted ring buffer of action signatures — control state is tiny and must survive compaction, whereas the verbose transcript is what gets compacted. General principle: *compaction operates on the high-volume, low-density content (tool outputs, prose), never on the small, high-density control/accounting state.* Different data, different lifecycle.</details>

### Drill 4 — Budget-aware assembly (write it)

Assembly must fit context into `budget`. But the *model's output* also costs tokens, and tool results you're about to fetch are unpredictable. Write the budget split: given a hard limit `L`, allocate the assembly budget so you don't truncate output or blow the window mid-turn.

<details><summary>Hint</summary>Reserve for: model output (max_tokens), a safety margin, and expected incoming tool-result size. Assembly gets what's left.</details>

<details><summary>Solution</summary>

```python
def assembly_budget(L, max_output_tokens, expected_tool_result=4000, safety=0.10):
    reserved = max_output_tokens + expected_tool_result + int(L * safety)
    budget = L - reserved
    if budget < L * 0.3:
        raise ValueError("reservations too large; raise L or shrink max_output/tool results")
    return budget
# e.g. L=200_000, output=8_000, tool=4_000, safety=20_000 -> assembly gets ~168_000
```

The point the drill drives home: **assembly's budget is not `L`.** You run assembly against a *reduced* budget that pre-subtracts output, incoming tool results, and a safety margin — otherwise a turn that assembles to 195K, then generates 8K output plus a 6K tool result, overflows mid-turn and truncates. Budgeting is a whole-turn accounting problem, not a context-only one. (This connects to Day 22: these reservations are cost knobs you'll tune by measurement.)</details>

### Drill 5 — Memory poisoning meets verification (callback to Day 14 + 12)

`maybe_remember` wrote a *summary* note on turn 5: "the fix is to increment depth in parse_expr." It was wrong (the real bug was elsewhere). Now on turn 20, `recall` surfaces this note into context every time the agent looks at the parser. Describe the failure loop, and give two defenses drawn from different days.

<details><summary>Answer</summary>Failure loop: the wrong note is retrieved into a *strong* context position every relevant turn, so the agent repeatedly re-tries the disproven fix — a self-reinforcing rut where *memory feeds the loop bad guidance and the loop keeps acting on it.* Worse, verification (Day 17) may keep failing without the agent connecting the failure to the poisoned memory. Two defenses: **(1) From Day 17 — let verification update memory.** When `verify` rejects a claim tied to a remembered hypothesis, *invalidate or annotate that memory* ("tried depth-increment fix; verified FAILED — do not repeat"). Memory should record *disproven* hypotheses, not just proposed ones, so recall surfaces "don't do this" rather than "do this." **(2) From Day 14/12 — prefer storing *facts and observations* over *conclusions* in durable memory.** "test_nested fails with off-by-one at line 44" (an observation) ages well; "the fix is X" (a conclusion) can become poison once disproven. Store the evidence; let the model re-derive conclusions against fresh state. The meta-lesson: memory + control are coupled — an agent that remembers must also *forget/correct* when the loop proves a memory wrong.</details>

### Drill 6 — Place five things (callback to Day 9)

This turn's assembled context contains: [system+tools], [running summary, 3K], [pinned constraint: "output must be valid JSON"], [recent 4 turns], [just-retrieved memory note], [the latest failing test output]. Put them in order and justify the two strong-position choices and the one middle sacrifice.

<details><summary>Answer</summary>Order (start→end): **[system+tools]** (fixed, strongest start) → **[pinned constraint]** (must never be missed; strong start) → **[running summary]** (background narrative; the safe **middle** sacrifice — it's context, not the current action) → **[retrieved memory note]** (relevant-now, moving toward the end) → **[recent 4 turns]** (freshest reasoning) → **[latest failing test output]** (the thing the next action must respond to; strongest **end**). Strong-position justifications: the **pinned constraint** goes near the start because a violated output-format constraint fails the whole task and it must survive attention; the **latest test output** goes at the very end because it's the immediate stimulus for the next decision. The **running summary** takes the middle because it's the least immediately-actionable item — useful background the model can skim, whose individual details, if momentarily under-attended, won't derail the next action (and its critical bits are separately pinned/retrieved anyway).</details>

### Drill 7 — Full turn dry-run (the big one)

Given this state, hand-execute `turn()` and state the exit: `budget`: 28/30 turns, $4.90/$5.00; `recent`: [run_tests, run_tests] (2, not yet 3); context would assemble to 210K but `L`=200K; model will (hypothetically) claim done; verifier = pytest, currently red. Walk all six phases and name the exit.

<details><summary>Answer</summary>Phase 1 **Control:** `budget.exceeded()`? Turns 28<30 (ok), cost $4.90<$5.00 (ok) — *passes, barely.* `is_stuck`? Only 2 identical recent actions, window is 3 — *not yet stuck.* Proceeds (note how close both are — this turn is on a knife's edge). Phase 2 **Compaction:** context would assemble to 210K > 200K `L` → *fires*: compact old turns into memory, bringing state under budget. Phase 3 **Assembly:** builds a fitting payload against the *reduced* assembly budget (Drill 4), not 200K. Phase 4 **Model call:** charges usage — this likely trips the $5.00 cost budget *after* charging (say $4.90 → $5.05). Phase 5 **Termination:** model claims done → `verify` runs pytest → **red** → claim *rejected*, feeds "verification failed" observation. Phase 6 would continue — **but** the next turn's Phase 1 control check now sees cost ≥ $5.00 → **exit_budget("cost budget")**. Final exit: **BudgetHit (cost)**, with an honest hand-off summary. The drill's lesson: the subsystems interact across turns — compaction saved this turn from overflow, verification caught a false done, and the cost budget (not the turn budget) is what actually stops the run. You must reason about *which* constraint binds first, and it's rarely `max_turns`.</details>

### Drill 8 — Design critique (stretch, combines everything)

A teammate proposes: "Simplify — one `history` list. Compaction, assembly, stuck-detection, and budgeting all read and mutate it directly. Fewer moving parts." Give the three concrete bugs this monolith invites, each tied to a specific subsystem interaction, and state the design principle it violates.

<details><summary>Answer</summary>Three bugs: **(1) Compaction vs. control (Drill 3):** compaction mutating the one list erases the action history stuck-detection needs → missed ruts across compaction boundaries. **(2) Assembly vs. budgeting (Drill 4):** if assembly reads/trims the same list that budgeting counts, you conflate "what's stored" with "what's sent this turn" — you lose the ability to keep full state while sending a reduced view (the Day 10 state-vs-context distinction collapses), and output/tool reservations have nowhere clean to live. **(3) Memory vs. compaction (Drill 5):** with no separation between the ephemeral transcript and durable memory, compaction either destroys facts permanently (no recovery) or memory bloats the same list it's meant to offload. Principle violated: **separation of concerns / single-writer per state.** Each subsystem owns a distinct slice of state with a distinct lifecycle — *transcript* (compactable), *durable memory* (persistent, curated), *control/accounting* (small, never compacted), and *this-turn context* (derived, ephemeral). The monolith couples four lifecycles into one mutable blob, so every subsystem's edits corrupt another's assumptions. "Fewer moving parts" here means "more undebuggable interactions" — the classic false economy. The four-slice separation *is* the architecture your capstone needs.</details>

---

## Cooldown — retrieve the integration

Close everything. From memory, write the six phases of `turn()` in order, and next to each name the day it came from and the *one* state slice it touches.

<details><summary>Compare to these</summary>

1. **Control** (Day 17) — reads control/accounting slice (budget counters, recent-actions ring).
2. **Compaction** (Day 12) — reads/shrinks the transcript slice, writes durable-memory slice.
3. **Assembly** (Days 10/14) — reads transcript + durable memory, produces the ephemeral this-turn-context slice.
4. **Model call** — consumes this-turn-context, updates accounting (usage).
5. **Termination/verify** (Day 17) — reads verifier + state, decides success/continue.
6. **Act/remember** (Days 6/14) — appends to transcript, writes durable memory, updates recent-actions.

Four state slices, four lifecycles: transcript (compactable), durable memory (persistent), control/accounting (tiny, never compacted), this-turn context (derived, ephemeral).
</details>

> **Transfer — apply it:** Sketch the four state slices for an agent in your domain. Name one concrete item that lives in each slice, and one bug you'd get if two of them shared storage. This sketch is the skeleton you'll formalize in the capstone.

## Connect it back

You've now integrated the entire bottleneck: four subsystems, one ordered turn, four separated state slices — and drilled the *interactions* that a monolith would corrupt ([assembly](../../03-context-engineering/days/day-10-context-assembly.md) × [memory](../../03-context-engineering/days/day-14-memory-and-state.md) × [compaction](../../03-context-engineering/days/day-12-compaction-lost-in-the-middle.md) × [control](day-17-loop-control-and-stopping.md)). This architecture is what the capstone builds on. The production arc begins tomorrow: your agent manages context and terminates honestly, but it still assumes tools and models *don't fail* — and in production they fail constantly. Next: **failure & recovery**. The question you can now answer cold: *why must compaction, stuck-detection, memory, and assembly each own a separate slice of state — what breaks when they share one list?*

## Suggested readings for today

**Required if you have 15 extra minutes:** Re-watch/skim Dex Horthy, "12-Factor Agents" — [talk](https://www.youtube.com/watch?v=8kMaTybvDUw) — now through the lens of state separation. His "stateless reducer" and "own your context/control flow" factors are the same four-slice discipline you just drilled.

**If you want the deep version:** Anthropic, "Effective Harnesses for Long-Running Agents," 2025 — [link](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) — re-read for how production harnesses keep these subsystems coordinated over long runs.

---

## Navigation

← **Previous:** [Day 17 — Loop Control & Stopping](day-17-loop-control-and-stopping.md)  
→ **Next:** [Day 19 — Multi-Agent Orchestration](day-19-multi-agent-orchestration.md)
