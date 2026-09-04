# Glossary

Each term: plain-English definition · formal definition · *introduced: Day N*. Grows as the course does.

---

**Agentic loop** — Plain: the repeat-until-done cycle that lets a model take many steps toward a goal. Formal: an iterative control structure that alternates model inference with environment interaction (observe → decide → act → observe) until a termination condition holds. *Introduced: Day 2.*

**Budget (loop budget)** — Plain: hard ceilings on what one run may consume before the harness stops it. Formal: harness-enforced limits on turns, tokens, cost, and wall-clock time, counted from real usage and checked each turn. *Introduced: Day 12.*

**Circuit breaker** — Plain: after a tool fails repeatedly, stop calling it for a while instead of hammering a dead service. Formal: a stability pattern that "trips open" after N consecutive failures, fast-failing calls during a cooldown before allowing a trial call. *Introduced: Day 14.*

**Compaction** — Plain: shrinking the history when it won't fit — by summarizing, extracting, deduping, or dropping. Formal: any transformation reducing the token footprint of accumulated state while preserving the information the loop needs. *Introduced: Day 11.*

**Context (context window)** — Plain: everything the model can "see" on a given turn — its whole mind for that step. Formal: the ordered token sequence supplied to the model at inference, bounded by a fixed maximum length. *Introduced: Day 4.*

**Context assembly** — Plain: the code that builds the message list you send each turn. Formal: the per-turn function that selects, orders, and formats system instructions, history, memory, and tool results into the payload, subject to a budget. *Introduced: Day 7.*

**Context engineering** — Plain: designing what set of tokens the model sees to get the behavior you want. Formal: the discipline of optimizing the configuration of context (instructions, history, memory, retrieved facts, order) against the model's constraints. *Introduced: Day 4 (named Day 7).*

**Done-verification** — Plain: checking that a "task complete" claim is actually true before accepting it. Formal: an objective test (run the suite, compare state) gating loop termination on a completion claim. *Introduced: Day 12.*

**Drill day** — Plain: a gym day — repeated isolated exercises on one sub-skill, no new concepts. Formal: a consolidation page of progressive exercises targeting the rate-limiting sub-skill. *Introduced: Day 8.*

**Eval (evaluation harness)** — Plain: a repeatable test that scores your agent on a fixed set of tasks. Formal: a system running an agent over a frozen task set with automatic graders, producing comparable metrics (success, pass^k, cost). *Introduced: Day 15.*

**Harness** — Plain: the code wrapped around an LLM that turns it into something that does things. Formal: the runtime managing inference calls, tool dispatch, context assembly, state, control flow, and I/O for a language model. *Introduced: Day 1.*

**Idempotency** — Plain: making an action safe to run twice, so a retry can't double-apply it. Formal: the property that repeated application of an operation yields the same result as one application; enforced via keys or check-then-act. *Introduced: Day 14.*

**Loop control** — Plain: the logic deciding whether to keep going, stop, or bail out. Formal: the policy governing continuation, termination, iteration budgets, and runaway prevention in an agentic loop. *Introduced: Day 12.*

**Memory (long-term)** — Plain: information the agent keeps outside the context window and pulls back when relevant. Formal: an external store (vector, key-value, file, or database) written and read by the harness to persist state beyond a single context window. *Introduced: Day 10.*

**Observability** — Plain: making the invisible steps of a run visible so you can debug and optimize. Formal: instrumentation (spans/traces, metrics) capturing timing, tokens, cost, and structure of each operation for inspection and replay. *Introduced: Day 16.*

**Observation** — Plain: what comes back after an action — a tool result or environment response. Formal: the environment's output appended to context following an action, forming the input to the next decision step. *Introduced: Day 2.*

**Orchestrator-workers** — Plain: one coordinating agent that hands subtasks to worker sub-agents. Formal: a multi-agent topology where an orchestrator loop decomposes a task and dispatches subtasks to worker loops, treating each as a tool. *Introduced: Day 17.*

**pass^k** — Plain: how often an agent passes a task on *all* k tries — a measure of reliability, not just capability. Formal: the fraction of tasks for which all k independent runs succeed (τ-bench). *Introduced: Day 15.*

**Prompt (context) caching** — Plain: paying reduced price to re-process a stable prefix of context you send every turn. Formal: provider-side reuse of a byte-stable context prefix's computation across calls, billed at a discount. *Introduced: Day 16.*

**ReAct** — Plain: the pattern of letting the model think, then act, then see the result, in a loop. Formal: an agent method interleaving reasoning traces (Thought) and actions (Act) with observations (Obs), per Yao et al. 2023. *Introduced: Day 2.*

**Recall (retrieval)** — Plain: pulling the most relevant stored facts back into context for the current step. Formal: relevance-scored selection from long-term memory (e.g. semantic similarity, recency, importance) injected into the assembled context. *Introduced: Day 10.*

**Resilient call** — Plain: a tool/model call wrapped so failure is absorbed, not fatal. Formal: a call wrapper adding timeout, failure classification, backoff retry, and a circuit breaker, returning an observation for every outcome. *Introduced: Day 14.*

**Running summary** — Plain: a single evolving digest of everything old, kept in a strong context position. Formal: a maintained compaction artifact folding newly-aged turns into a constant-size summary. *Introduced: Day 11.*

**Span (trace)** — Plain: a timed, labeled record of one operation, nested to mirror the loop. Formal: a unit of a distributed trace capturing an operation's duration, attributes (tokens, cost), and parent, aggregated into a run's trace. *Introduced: Day 16.*

**State (agent state)** — Plain: what the agent knows and has done so far. Formal: the full set of information carried across turns — transcript, memory, control/accounting — that the harness maintains. *Introduced: Day 10.*

**Stateless (model)** — Plain: the model remembers nothing between calls; each call is fresh. Formal: a function with no persistent internal state across invocations — output depends only on the current input tokens. *Introduced: Day 1.*

**Stuck-detection** — Plain: noticing the agent isn't making progress (repeating, oscillating) and stopping it. Formal: a harness check over recent actions/results identifying no-progress states before budget exhaustion. *Introduced: Day 12.*

**Sub-agent** — Plain: another full agent loop that a parent agent calls like a tool, with its own fresh context. Formal: a nested agentic loop invoked via a tool interface, returning a distilled result across a serialized boundary. *Introduced: Day 17.*

**System prompt** — Plain: the standing instructions at the top of context that shape how the model behaves. Formal: the leading, typically fixed segment of context setting role, constraints, and available tools. *Introduced: Day 4.*

**Tool** — Plain: a function the model can ask the harness to run on its behalf. Formal: a named, schema-described operation the model invokes by emitting a structured call, executed by the harness with the result returned as an observation. *Introduced: Day 3.*

**Tool call** — Plain: the model's request to run a tool, with arguments. Formal: a structured output (name + arguments conforming to a schema) the harness parses, validates, and dispatches. *Introduced: Day 3.*

**Trajectory** — Plain: the whole path an agent took — every tool call and decision — not just its final answer. Formal: the ordered sequence of turns (actions, observations, state) from task to termination; the unit an agent eval grades. *Introduced: Day 15.*

**Trust boundary** — Plain: the seam where untrusted model output meets code that touches the world; validate here. Formal: the interface at which model-emitted tool calls are validated before dispatch, treated as untrusted input. *Introduced: Day 6.*

**Turn** — Plain: one trip around the loop — one model call plus whatever the harness does with its output. Formal: a single iteration of the agentic loop comprising context assembly, inference, and action/observation handling. *Introduced: Day 5.*

**Working memory** — Plain: the context window — the small, fast scratchpad the model sees right now. Formal: the in-context state available to the model on the current turn, as opposed to external long-term memory. *Introduced: Day 10.*
