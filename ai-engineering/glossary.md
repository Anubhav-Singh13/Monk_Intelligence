# Glossary

Each term: plain-English definition · formal definition · *introduced: Day N*.

---

**Agentic loop** — Plain: the repeat-until-done cycle letting a model take many steps. Formal: an iterative control structure alternating model inference with environment interaction (observe → decide → act → observe) until a termination condition holds. *Introduced: Day 9.*

**AI engineering** — Plain: building reliable systems *around* a model you didn't train. Formal: building applications on pre-trained foundation models, adapting them without changing weights and making the system reliable, evaluable, economical. *Introduced: Day 1.*

**Budget (loop budget)** — Plain: hard ceilings on what one run may consume. Formal: harness-enforced limits on turns, tokens, cost, and wall-clock, counted from real usage and checked each turn. *Introduced: Day 19.*

**Business rule** — Plain: domain logic that must hold ("completed ⟹ paid ∧ shipped"). Formal: a conditional constraint over domain entities, enforced at the write boundary; violations are repaired (extraction error) or routed (real anomaly). *Introduced: Day 28.*

**Chunking** — Plain: splitting documents into passages for retrieval. Formal: segmenting a corpus into retrievable units on semantic/structural boundaries, balancing recall against context cost. *Introduced: Day 17.*

**Checkpoint / resumability** — Plain: saving the agent's position so it can resume after a crash or pause. Formal: persisting `(node, state)` after each graph node so execution restarts from the last completed step. *Introduced: Day 26.*

**Circuit breaker** — Plain: after a tool fails repeatedly, stop calling it for a while. Formal: a stability pattern that trips open after N consecutive failures, fast-failing during a cooldown before a trial call. *Introduced: Day 22.*

**Class (ontology)** — Plain: a *kind* of thing in your domain (Customer, Order). Formal: a concept in an ontology, often hierarchical (is-a), carrying attributes and participating in typed relations. *Introduced: Day 27.*

**Compaction** — Plain: shrinking history when it won't fit — summarize, extract, dedupe, or drop. Formal: any transformation reducing the token footprint of accumulated state while preserving needed signal. *Introduced: Day 14.*

**Competency question** — Plain: a question the system must be able to answer, used to scope an ontology. Formal: a query that defines an ontology's boundary — model exactly enough to answer it, no more. *Introduced: Day 27.*

**Constraint** — Plain: an executable rule that rejects data violating your domain's logic. Formal: a structural (cardinality/type/enum) or business check applied at the write boundary; the ontology's enforcement mechanism (cf. SHACL). *Introduced: Day 28.*

**Context (context window)** — Plain: everything the model sees on a turn — its whole mind. Formal: the ordered token sequence supplied at inference, bounded by a fixed maximum length. *Introduced: Day 11.*

**Context assembly** — Plain: the code that builds the message list each turn. Formal: the per-turn function selecting, ordering, and fitting system/history/memory/tool-results into the payload under a budget. *Introduced: Day 12.*

**Context engineering** — Plain: designing what set of tokens the model sees. Formal: optimizing the configuration of context (instructions, history, memory, retrieved facts, order) against the model's constraints. *Introduced: Day 11.*

**Context graph** — Plain: memory stored as a graph of entities and relations you traverse, with time. Formal: a (temporal) knowledge graph used as agent memory — timestamped triples supporting multi-hop, time-filtered recall. *Introduced: Day 25.*

**Control-flow graph** — Plain: the agent's loop as nodes (steps) and edges (transitions). Formal: an explicit state machine `(nodes, edges, state)` where nodes transform shared state and conditional edges route. *Introduced: Day 26.*

**Done-verification** — Plain: checking a "task complete" claim before accepting it. Formal: an objective test (run tests, compare state) gating termination on a completion claim. *Introduced: Day 19.*

**Drill day** — Plain: a gym day — repeated reps on one sub-skill, no new concepts. Formal: a consolidation page of progressive exercises targeting the rate-limiting sub-skill. *Introduced: Day 13.*

**Embedding** — Plain: a vector capturing text meaning, so similar texts sit near each other. Formal: a dense vector representation used to score semantic similarity for retrieval. *Introduced: Day 17.*

**Few-shot (in-context learning)** — Plain: showing the model examples in the prompt to steer output. Formal: providing input/output exemplars in context to condition behavior for the current call without changing weights. *Introduced: Day 5.*

**Foundation model** — Plain: a large pre-trained model you rent and build on. Formal: a general model trained by a third party, consumed via API or self-hosted weights; the Capability layer's subject. *Introduced: Day 3.*

**Harness** — Plain: the code wrapped around an LLM that turns it into something that does things. Formal: the runtime managing inference, tool dispatch, context assembly, state, control flow, and I/O. *Introduced: Day 2.*

**Idempotency** — Plain: making an action safe to run twice, so a retry can't double-apply it. Formal: repeated application yields the same result as one; enforced via keys or check-then-act. *Introduced: Day 22.*

**Jagged intelligence** — Plain: capability that's spiky — brilliant and broken side by side. Formal: uneven task-competence where reliable "peaks" (generation, code) sit next to unreliable "pits" (arithmetic, counting, recall); design routes pit-work to tools/retrieval/verification. *Introduced: Day 4.*

**Loop control** — Plain: the logic deciding whether to keep going, stop, or bail. Formal: the policy governing continuation, termination, budgets, and runaway prevention. *Introduced: Day 19.*

**Memory (long-term)** — Plain: info the agent keeps outside the context window and pulls back when relevant. Formal: an external store (vector, key-value, file, graph) written and read to persist state beyond one window. *Introduced: Day 16.*

**Model selection** — Plain: choosing the cheapest model that clears your task's bar. Formal: filtering candidates by hard constraints (privacy/latency/modality), then a bake-off on your own eval scoring success/cost/latency. *Introduced: Day 3.*

**Multi-agent orchestration** — Plain: several agents, one coordinating and delegating. Formal: a topology where an orchestrator loop decomposes a task and dispatches subtasks to worker loops with isolated context. *Introduced: Day 21.*

**Observability** — Plain: making a run's invisible steps visible to debug and optimize. Formal: instrumentation (spans/traces, metrics) capturing timing, tokens, cost, and structure of each operation. *Introduced: Day 24.*

**Observation** — Plain: what comes back after an action — a tool result or environment response. Formal: the environment output appended to context after an action, forming the next decision's input. *Introduced: Day 9.*

**Ontology** — Plain: the shared, explicit model of what things exist in your domain and how they relate. Formal: a domain model of classes, typed relations, attributes, instances, and axioms — the schema of shared meaning an AI system extracts and reasons against. *Introduced: Day 27.*

**Output contract** — Plain: the required shape of the model's answer. Formal: an explicit specification (format, length, "only output X") the prompt imposes so code can consume the output. *Introduced: Day 5.*

**Parametric / non-parametric memory** — Plain: what the model knows from training vs. what you retrieve externally. Formal: knowledge in frozen weights vs. an external index queried at inference; RAG combines them. *Introduced: Day 17.*

**Parse boundary** — Plain: the checkpoint where soft model text becomes hard typed data. Formal: the point at which raw output is parsed and validated against a schema before any code consumes it. *Introduced: Day 6.*

**pass^k** — Plain: how often an agent passes a task on *all* k tries — reliability, not just capability. Formal: the fraction of tasks for which all k independent runs succeed (τ-bench). *Introduced: Day 23.*

**Prompt (context) caching** — Plain: paying less to re-process a stable prefix sent every turn. Formal: provider-side reuse of a byte-stable context prefix's computation across calls, billed at a discount. *Introduced: Day 24.*

**Prompting (prompt engineering)** — Plain: writing the model's input as a deliberate spec. Formal: constructing input tokens (role, instructions, examples, output contract) to narrow a probabilistic model toward a target. *Introduced: Day 5.*

**ReAct** — Plain: think, then act, then see the result, in a loop. Formal: an agent method interleaving reasoning traces (Thought), actions (Act), and observations (Obs), per Yao et al. 2023. *Introduced: Day 9.*

**Recall (retrieval)** — Plain: pulling relevant stored facts into context for the current step. Formal: relevance-scored selection from long-term memory injected into assembled context. *Introduced: Day 16.*

**Relation (ontology)** — Plain: how two classes connect ("Customer places Order"). Formal: a typed property with domain, range, and cardinality between ontology classes. *Introduced: Day 27.*

**Resilient call** — Plain: a tool/model call wrapped so failure is absorbed, not fatal. Formal: a wrapper adding timeout, failure classification, backoff retry, and a circuit breaker, returning an observation for every outcome. *Introduced: Day 22.*

**Retrieval-Augmented Generation (RAG)** — Plain: look up relevant text and hand it to the model with the question. Formal: grounding generation in non-parametric memory by retrieving query-relevant passages into context at inference. *Introduced: Day 17.*

**Running summary** — Plain: one evolving digest of everything old, kept in a strong position. Formal: a maintained compaction artifact folding newly-aged turns into a constant-size summary. *Introduced: Day 14.*

**SHACL** — Plain: a standard language for writing graph constraints ("shapes"). Formal: the W3C Shapes Constraint Language for validating RDF/knowledge graphs against cardinality/type/value constraints. *Introduced: Day 28.*

**State (agent state)** — Plain: what the agent knows and has done so far. Formal: the full information carried across turns — transcript, memory, control/accounting — maintained by the harness. *Introduced: Day 10.*

**Stateless (model)** — Plain: the model remembers nothing between calls. Formal: a function with no persistent internal state across invocations; output depends only on current input tokens. *Introduced: Day 2.*

**Structured output** — Plain: model output in a strict, machine-parseable format. Formal: generation constrained to a schema (JSON mode / schema-constrained / tool-calling) so code can consume it reliably. *Introduced: Day 6.*

**Stuck-detection** — Plain: noticing the agent isn't progressing (repeating, oscillating) and stopping it. Formal: a harness check over recent actions/results identifying no-progress states before budget exhaustion. *Introduced: Day 19.*

**System prompt** — Plain: standing instructions at the top of context that shape behavior. Formal: the leading, typically fixed context segment setting role, constraints, and available tools. *Introduced: Day 5.*

**Temporal knowledge graph** — Plain: a knowledge graph where every fact carries when it was (and stopped being) true. Formal: a knowledge graph with bitemporal validity intervals and provenance per edge, enabling as-of and current-state queries. *Introduced: Day 25.*

**Tool** — Plain: a function the model can ask the harness to run. Formal: a named, schema-described operation the model invokes by emitting a structured call, executed by the harness with the result returned as an observation. *Introduced: Day 7.*

**Tool call** — Plain: the model's request to run a tool, with arguments. Formal: a structured output (name + schema-conforming arguments) the harness parses, validates, and dispatches. *Introduced: Day 7.*

**Trajectory evaluation** — Plain: scoring the whole sequence of steps, not just the final answer. Formal: evaluating an agent's run (actions + outcome) against a frozen task set with automatic graders. *Introduced: Day 23.*

**Turn** — Plain: one trip around the loop — a model call plus what the harness does with its output. Formal: one iteration comprising context assembly, inference, and action/observation handling. *Introduced: Day 10.*
