# Glossary

Each term: plain-English definition · formal definition · *introduced: Day N*.

---

**Agentic loop** — Plain: the repeat-until-done cycle letting a model take many steps toward a goal. Formal: an iterative control structure alternating model inference with environment interaction (observe → decide → act → observe) until a termination condition holds. *Introduced: Day 7.*

**AI engineering** — Plain: building reliable systems *around* a model you didn't train. Formal: the practice of building applications on pre-trained foundation models, adapting them without changing weights and making the system reliable, evaluable, and economical. *Introduced: Day 1.*

**Budget (loop budget)** — Plain: hard ceilings on what one run may consume before the harness stops it. Formal: harness-enforced limits on turns, tokens, cost, and wall-clock, counted from real usage and checked each turn. *Introduced: Day 17.*

**Chunking** — Plain: splitting documents into passages for retrieval. Formal: segmenting a corpus into retrievable units, ideally on semantic/structural boundaries, balancing recall against context cost. *Introduced: Day 15.*

**Checkpoint / resumability** — Plain: saving the agent's position so it can resume after a crash or pause. Formal: persisting `(node, state)` after each graph node so execution can restart from the last completed step. *Introduced: Day 24.*

**Circuit breaker** — Plain: after a tool fails repeatedly, stop calling it for a while. Formal: a stability pattern that trips open after N consecutive failures, fast-failing during a cooldown before a trial call. *Introduced: Day 20.*

**Compaction** — Plain: shrinking history when it won't fit — summarize, extract, dedupe, or drop. Formal: any transformation reducing the token footprint of accumulated state while preserving needed signal. *Introduced: Day 12.*

**Context (context window)** — Plain: everything the model sees on a turn — its whole mind. Formal: the ordered token sequence supplied at inference, bounded by a fixed maximum length. *Introduced: Day 9.*

**Context assembly** — Plain: the code that builds the message list each turn. Formal: the per-turn function selecting, ordering, and fitting system/history/memory/tool-results into the payload under a budget. *Introduced: Day 10.*

**Context engineering** — Plain: designing what set of tokens the model sees to get the behavior you want. Formal: optimizing the configuration of context (instructions, history, memory, retrieved facts, order) against the model's constraints. *Introduced: Day 9.*

**Context graph** — Plain: memory stored as a graph of entities and relations you traverse, with time. Formal: a (temporal) knowledge graph used as agent memory — timestamped triples (subject, relation, object, valid-from/to, provenance) supporting multi-hop, time-filtered recall. *Introduced: Day 23.*

**Control-flow graph** — Plain: the agent's loop expressed as nodes (steps) and edges (transitions). Formal: an explicit state machine `(nodes, edges, state)` where nodes transform shared state and conditional edges route between them. *Introduced: Day 24.*

**Done-verification** — Plain: checking a "task complete" claim is true before accepting it. Formal: an objective test (run tests, compare state) gating loop termination on a completion claim. *Introduced: Day 17.*

**Drill day** — Plain: a gym day — repeated isolated reps on one sub-skill, no new concepts. Formal: a consolidation page of progressive exercises targeting the rate-limiting sub-skill. *Introduced: Day 11.*

**Embedding** — Plain: a vector that captures a text's meaning, so similar texts sit near each other. Formal: a dense vector representation used to score semantic similarity for retrieval. *Introduced: Day 15.*

**Few-shot (in-context learning)** — Plain: showing the model examples in the prompt to steer its output. Formal: providing input/output exemplars in context to condition behavior for the current call without changing weights. *Introduced: Day 3.*

**Harness** — Plain: the code wrapped around an LLM that turns it into something that does things. Formal: the runtime managing inference, tool dispatch, context assembly, state, control flow, and I/O for a model. *Introduced: Day 2.*

**Idempotency** — Plain: making an action safe to run twice, so a retry can't double-apply it. Formal: the property that repeated application yields the same result as one; enforced via keys or check-then-act. *Introduced: Day 20.*

**Loop control** — Plain: the logic deciding whether to keep going, stop, or bail. Formal: the policy governing continuation, termination, budgets, and runaway prevention in an agentic loop. *Introduced: Day 17.*

**Memory (long-term)** — Plain: info the agent keeps outside the context window and pulls back when relevant. Formal: an external store (vector, key-value, file, graph) written and read by the harness to persist state beyond one window. *Introduced: Day 14.*

**Multi-agent orchestration** — Plain: several agents, one coordinating and delegating to others. Formal: a topology where an orchestrator loop decomposes a task and dispatches subtasks to worker loops with isolated context. *Introduced: Day 19.*

**Observability** — Plain: making a run's invisible steps visible to debug and optimize. Formal: instrumentation (spans/traces, metrics) capturing timing, tokens, cost, and structure of each operation. *Introduced: Day 22.*

**Observation** — Plain: what comes back after an action — a tool result or environment response. Formal: the environment output appended to context after an action, forming the next decision's input. *Introduced: Day 7.*

**Output contract** — Plain: the required shape of the model's answer. Formal: an explicit specification (format, length, "only output X") the prompt imposes so downstream code can consume the output. *Introduced: Day 3.*

**Parametric / non-parametric memory** — Plain: what the model knows from training vs. what you retrieve from an external corpus. Formal: knowledge in the frozen weights vs. an external index queried at inference; RAG combines them. *Introduced: Day 15.*

**Parse boundary** — Plain: the checkpoint where soft model text becomes hard typed data your code can trust. Formal: the point at which raw output is parsed and validated against a schema before any code consumes it. *Introduced: Day 4.*

**pass^k** — Plain: how often an agent passes a task on *all* k tries — reliability, not just capability. Formal: the fraction of tasks for which all k independent runs succeed (τ-bench). *Introduced: Day 21.*

**Prompt (context) caching** — Plain: paying less to re-process a stable prefix you send every turn. Formal: provider-side reuse of a byte-stable context prefix's computation across calls, billed at a discount. *Introduced: Day 22.*

**Prompting (prompt engineering)** — Plain: writing the model's input as a deliberate spec, not a wish. Formal: constructing the input tokens (role, instructions, examples, output contract) to narrow a probabilistic model's output toward a target. *Introduced: Day 3.*

**ReAct** — Plain: think, then act, then see the result, in a loop. Formal: an agent method interleaving reasoning traces (Thought), actions (Act), and observations (Obs), per Yao et al. 2023. *Introduced: Day 7.*

**Recall (retrieval)** — Plain: pulling the most relevant stored facts into context for the current step. Formal: relevance-scored selection from long-term memory (similarity/recency/importance) injected into assembled context. *Introduced: Day 14.*

**Resilient call** — Plain: a tool/model call wrapped so failure is absorbed, not fatal. Formal: a wrapper adding timeout, failure classification, backoff retry, and a circuit breaker, returning an observation for every outcome. *Introduced: Day 20.*

**Retrieval-Augmented Generation (RAG)** — Plain: look up relevant text and hand it to the model with the question. Formal: grounding generation in non-parametric memory by retrieving query-relevant passages into context at inference. *Introduced: Day 15.*

**Running summary** — Plain: one evolving digest of everything old, kept in a strong context position. Formal: a maintained compaction artifact folding newly-aged turns into a constant-size summary. *Introduced: Day 12.*

**State (agent state)** — Plain: what the agent knows and has done so far. Formal: the full information carried across turns — transcript, memory, control/accounting — maintained by the harness. *Introduced: Day 8.*

**Stateless (model)** — Plain: the model remembers nothing between calls. Formal: a function with no persistent internal state across invocations; output depends only on current input tokens. *Introduced: Day 2.*

**Structured output** — Plain: model output in a strict, machine-parseable format. Formal: generation constrained to a schema (JSON mode / schema-constrained / tool-calling) so code can consume it reliably. *Introduced: Day 4.*

**Stuck-detection** — Plain: noticing the agent isn't progressing (repeating, oscillating) and stopping it. Formal: a harness check over recent actions/results identifying no-progress states before budget exhaustion. *Introduced: Day 17.*

**System prompt** — Plain: the standing instructions at the top of context that shape behavior. Formal: the leading, typically fixed context segment setting role, constraints, and available tools. *Introduced: Day 3.*

**Temporal knowledge graph** — Plain: a knowledge graph where every fact carries when it was (and stopped being) true. Formal: a knowledge graph with bitemporal validity intervals and provenance per edge, enabling as-of and current-state queries. *Introduced: Day 23.*

**Tool** — Plain: a function the model can ask the harness to run. Formal: a named, schema-described operation the model invokes by emitting a structured call, executed by the harness with the result returned as an observation. *Introduced: Day 5.*

**Tool call** — Plain: the model's request to run a tool, with arguments. Formal: a structured output (name + schema-conforming arguments) the harness parses, validates, and dispatches. *Introduced: Day 5.*

**Trajectory evaluation** — Plain: scoring not just the final answer but the whole sequence of steps. Formal: evaluating an agent's run (actions + outcome) against a frozen task set with automatic graders. *Introduced: Day 21.*

**Turn** — Plain: one trip around the loop — a model call plus what the harness does with its output. Formal: one iteration comprising context assembly, inference, and action/observation handling. *Introduced: Day 8.*
