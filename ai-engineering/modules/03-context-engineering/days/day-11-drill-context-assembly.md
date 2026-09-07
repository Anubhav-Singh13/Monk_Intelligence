# Day 11 — Drill I: Context Assembly

> **Today:** No new concepts. Pure reps on the bottleneck — selecting, ordering, and budgeting the model's payload under pressure.
> **Reading time:** ~40 min (this is a gym, not a library — expect to sweat)
> **Prereqs:** [Day 10](day-10-context-assembly.md), [Day 9](day-09-context-is-everything.md)
> **Before you start:** Recall Day 10's load-bearing idea — one sentence, no looking: *what is the difference between state and context, and what three jobs does `assemble_context` do in priority order?*

You built `assemble_context` yesterday. Today you'll do eight exercises that get progressively nastier, all on that one function. No narrative, no new ideas — just the skill you named as your bottleneck, drilled until it's reflex. Do them in order; each assumes the last. Attempt every one *before* opening its solution. If you're not slightly uncomfortable, go faster or raise the difficulty.

Set up a shared fixture so every drill is concrete:

```python
# A synthetic "state" from a 20-turn coding-agent run. Token counts are illustrative.
STATE = {
    "system": "You are a coding agent. Fix the failing test. Never touch files outside /workspace.",  # ~40 tok
    "task": "Make test_parser.py::test_nested pass.",                                                   # ~15 tok
    "turns": [
        {"id": 1,  "kind": "tool_result", "tokens": 1800, "summary": "full contents of parser.py (400 lines)"},
        {"id": 2,  "kind": "assistant",   "tokens": 120,  "summary": "hypothesis: bug in tokenize()"},
        {"id": 3,  "kind": "tool_result", "tokens": 60,   "summary": "test output: AssertionError line 44"},
        {"id": 4,  "kind": "assistant",   "tokens": 90,   "summary": "plan: add depth tracking to parse_expr"},
        # ... turns 5-17: exploration, several large file reads, a few dead ends ...
        {"id": 18, "kind": "tool_result", "tokens": 1500, "summary": "full contents of tokenizer.py (re-read)"},
        {"id": 19, "kind": "assistant",   "tokens": 80,   "summary": "the fix: increment depth on '(' in parse_expr"},
        {"id": 20, "kind": "tool_result", "tokens": 55,   "summary": "test still failing: off-by-one at line 44"},
    ],
}
```

---

### Drill 1 — Budget arithmetic (warm-up)

The model's hard limit is 200,000 tokens. You want 40% headroom reserved for the model's *output* and unpredictable tool results. Turns 1–20 total 48,000 tokens; system+task = 55. What is your assembly budget `L_budget`, and does the full history fit?

<details><summary>Answer</summary>Headroom of 40% means `L_budget = 200,000 × 0.60 = 120,000` tokens. Full history (48,000 + 55 ≈ 48,055) fits comfortably under 120,000 — *today.* The drill's point: it fits now, but the run isn't over, and turns 1 and 18 (two 1,500–1,800-token file dumps) are the kind of thing that, multiplied over a long run, blows the budget. Budget is a moving target you check every turn, not once.</details>

### Drill 2 — Rank by keep-priority

Without looking at token counts, rank these five turns by how much you'd fight to keep them in context on turn 21: `{system}`, `{task}`, turn 1 (full parser.py), turn 3 (AssertionError line 44), turn 20 (test still failing, off-by-one line 44). Justify with Day 9's positional logic.

<details><summary>Answer</summary>Keep-priority (highest first): **system** and **task** (tie — standing instructions + goal, must always be present, strong start position) → **turn 20** (freshest evidence, the current failure, strong end position) → **turn 3** (the specific assertion, still relevant and cheap at 60 tok) → **turn 1** (full parser.py — *most* droppable despite being "important-sounding," because it's 1,800 tokens of which you need maybe 15 lines, and it's re-derivable via a re-read). The lesson: *keep-priority is not the same as importance-sounding.* A huge, stale, reproducible blob is the first to go even if it's "the main file."</details>

### Drill 3 — Order for the U-curve

You've selected: system, task, turn 3, turn 19 (the fix idea), turn 20 (still failing). Arrange them in the message list to respect Lost-in-the-Middle. Which one goes in the weak middle, and why is that the safe choice?

<details><summary>Answer</summary>Order: **[system]** (start, strongest) → **[task re-pinned]** (start) → **[turn 19: the fix idea]** (middle — the safe sacrifice) → **[turn 3: the assertion]** (approaching end) → **[turn 20: still failing]** (end, strongest). Turn 19 goes in the weak middle because it's the *least* immediately actionable of the three: it's a hypothesis already tried (and turn 20 shows it didn't fully work). The two things the model most needs sharp — the concrete current failure (turn 20) and the goal (task) — sit in the two strong positions. You deliberately put the most "background" item where attention is weakest.</details>

### Drill 4 — The forced drop

New turn 21 arrives: a 1,600-token tool result (another file read). Adding it exceeds `L_budget`. You must drop ~1,600 tokens of *existing* selected content. Turn 1 (parser.py, 1,800 tok) is currently included. Do you drop turn 1, or drop several small recent turns totaling 1,600? Defend the choice and name what you must NOT drop.

<details><summary>Answer</summary>Drop **turn 1** (the 1,800-token parser.py dump): one eviction, frees more than enough, and it's stale + reproducible (the agent can re-read parser.py if it needs it again). Dropping "several small recent turns" is strictly worse — recent turns are your freshest reasoning and evidence (strong end position, high signal per token), and scattering the cuts damages the coherent recent narrative the model is mid-reasoning through. What you must **never** drop to make room: the **system prompt**, the **task**, and the **latest observation (turn 20/21)**. Rule of thumb: evict the *largest, stalest, most reproducible* item first, not the *most numerous small* items.</details>

### Drill 5 — Recency-only fails (callback to Day 10 stretch)

Your assembler keeps "the last 8 turns by recency." Turn 1's tool result contained the line "the config schema requires field `depth`, default 0." That detail is the actual cause of the off-by-one. By turn 21, turn 1 is long dropped. Describe the failure this produces and the minimal fix — *without* raising the budget.

<details><summary>Answer</summary>Failure: the agent keeps proposing fixes to `parse_expr` (turns 19–20) but never reconsiders the *default value* of `depth`, because the load-bearing fact (schema default 0, when it should initialize to a different base) fell out of the recency window and is now invisible. It loops on the wrong hypothesis — a silent degradation, not a crash. Minimal fix (no budget increase): **importance-pinning / extraction.** When turn 1 was about to be dropped, extract and pin the *high-signal line* ("config schema: `depth` default 0") as a durable note that's always included, discarding the other 1,790 tokens. This is recency **plus** importance — and generalizes to Day 14 (long-term memory) and Day 12 (compaction = keep the signal, drop the bulk).</details>

### Drill 6 — Write the eviction function

Implement `fit_to_budget(selected, budget)` that, given an ordered list of messages with `tokens` fields and a budget, evicts items to fit — but **never** evicts anything marked `pinned=True`, and prefers evicting the *largest* non-pinned, non-latest item first. Return the surviving list plus a note of what was dropped.

<details><summary>Hint</summary>Separate pinned/latest (never evict) from evictable. While over budget, pop the largest evictable by `tokens`. Keep a dropped-count for the "[N omitted]" marker.</details>

<details><summary>Solution</summary>

```python
def fit_to_budget(selected, budget, overhead=0):
    """selected: list of dicts with 'tokens', optional 'pinned'; last item is the latest (protected)."""
    protected_idx = {len(selected) - 1}                      # latest observation, never drop
    survivors = list(selected)
    def total():
        return overhead + sum(m["tokens"] for m in survivors)
    dropped = 0
    while total() > budget:
        # candidates: not pinned, not protected
        candidates = [(i, m) for i, m in enumerate(survivors)
                      if not m.get("pinned") and i not in protected_idx]
        if not candidates:
            raise ValueError("cannot fit: only pinned/latest remain — budget too small or too much pinned")
        # evict the LARGEST candidate
        i, _ = max(candidates, key=lambda t: t[1]["tokens"])
        survivors.pop(i)
        protected_idx = {len(survivors) - 1}                 # recompute protected latest
        dropped += 1
    return survivors, dropped
```

Two things the drill wants you to notice: (1) the `raise` — if pinned+latest alone exceed budget, no eviction can save you; that's a *design* error (too much pinned, or budget too small) that should surface loudly, not silently truncate. (2) "largest first" is a heuristic, not gospel — Drill 8 challenges it.</details>

### Drill 7 — The token counter is lying (efficiency)

You call `count_tokens` on the *entire* candidate list every time you test adding a message (like Day 10's naïve loop). On a 40-turn run that's O(n²) counting calls, and each is a network round-trip. Rewrite the counting so assembly is O(n) per turn. What do you cache?

<details><summary>Answer</summary>Cache **per-message token counts** the first time you see each message, since a message's own token count never changes once created. Store `msg["tokens"]` at append time (or memoize by message id). Then assembly is a sum over cached integers — O(n) additions, **zero** network calls for already-counted messages, plus one count for the single new message this turn. The Day 10 pattern of re-counting the whole growing list each turn is the trap: it turns a cheap arithmetic problem into a quadratic pile of API calls. (This foreshadows Day 22: measure before you optimize, and token-counting is itself a cost to instrument.)</details>

### Drill 8 — Adversarial ordering (stretch, combines Day 9 + Day 10)

Someone proposes: "Attention is U-shaped, so always put the single most important item *last* and the second-most *first*, and sort everything else by importance toward the middle." Give one concrete case where this rule *hurts* a coding agent, and state the better principle.

<details><summary>Answer</summary>It hurts when items have **dependencies or narrative order** that the model must follow. Example: a multi-step stack trace, or a diff that only makes sense read top-to-bottom, or a plan whose steps reference each other. Shuffling these by "importance toward the middle" shreds the causal/temporal structure the model needs to reason correctly — the model gets the "most important" line but can't reconstruct how it relates to the rest. Better principle: **respect semantic coherence first, then optimize position within coherent blocks.** Keep naturally-ordered units (a trace, a file, a plan, a recent-turn narrative) intact and contiguous; place the *whole block* by its importance; only reorder *across* independent blocks. Position is a tiebreaker among independent chunks, not a license to atomize structured content. (This is why real assemblers work at the granularity of coherent messages/turns, not individual tokens.)</details>

---

## Cooldown — retrieve the drill's lesson

Close everything. In four bullets, from memory, state the eviction rules you now hold as reflex: (1) what always stays, (2) what goes first, (3) recency vs. importance, (4) coherence vs. position.

<details><summary>Compare to these</summary>

1. **Always stays:** system prompt, task/goal, the latest observation. These are non-negotiable and sit in strong positions.
2. **Goes first:** the largest, stalest, most *reproducible* item (a big old file dump you can re-read) — not many small recent ones.
3. **Recency vs. importance:** recency alone silently drops old load-bearing facts; combine it with importance-pinning (extract and keep the high-signal line, discard the bulk).
4. **Coherence vs. position:** keep semantically-ordered blocks (traces, plans, recent narrative) intact and contiguous; use positional (U-curve) optimization only to order *independent* blocks, never to shuffle within a coherent one.
</details>

> **Transfer — apply it:** Open a real transcript from any agent you've run (or your Day 8 agent's logged history). Pick the turn where it was longest. Apply Drills 2–4 by hand: what would you pin, what would you evict first, how would you reorder? Write the three decisions. If you can't decide what to evict, that transcript is telling you your tools return too much low-signal output — a Day 6/Day 22 problem.

## Connect it back

No new ideas today — you drilled the one skill the whole course orbits until eviction, pinning, and U-curve ordering are reflexes rather than deliberations ([the bottleneck seeded on Day 9](day-09-context-is-everything.md), built on [Day 10](day-10-context-assembly.md)). Tomorrow you rest and synthesize the entire foundations arc (Days 2–11) from memory — no reading, just retrieval — before we add the second half of the bottleneck: *memory and state*. The question you can now answer under pressure: *when the budget forces a cut, what goes first, what never goes, and why is "keep the last N turns" a trap?*

## Suggested readings for today

**Required if you have 15 extra minutes:** Re-skim Anthropic, "Effective Context Engineering," 2025 — [link](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) — the "context editing" section, now that you've drilled eviction by hand. You'll read it as *techniques you just practiced* rather than abstract advice.

**If you want the deep version:** Liu et al., "Lost in the Middle," arXiv:2307.03172, §4 — the position-vs-accuracy data that Drills 3 and 8 are built on. Revisit the U-curve knowing you now make eviction decisions against it.

---

## Navigation

← **Previous:** [Day 10 — Context Assembly](day-10-context-assembly.md)  
→ **Next:** [Day 12 — Compaction & Lost-in-the-Middle](day-12-compaction-lost-in-the-middle.md)
