# Bibliography — The Course Shelf

Every source is real and verified, with enough to locate the exact chapter/section/timestamp. "Suggested readings" links from day pages point here.

---

## Foundational books

**Chip Huyen, *AI Engineering: Building Applications with Foundation Models*, O'Reilly, 2025.**
The defining book on engineering *around* foundation models. Favors a systems-practitioner's intuition. **Course role:** the spine — Day 1 (what AI engineering is), Day 3 (prompting), Day 4 (structured output), Day 15 (RAG), Day 21 (evaluation), Day 22 (cost). Skim Ch. 1 before Day 1.

**Martin Kleppmann, *Designing Data-Intensive Applications*, O'Reilly, 2017.**
How reliable stateful systems are built. **Course role:** the state/reliability mindset behind Day 14 (memory) and Day 20 (failure). Read Ch. 1 as background for the production arc.

**Michael T. Nygard, *Release It!: Design and Deploy Production-Ready Software*, 2nd ed., Pragmatic Bookshelf, 2018.**
The canonical stability-patterns catalog. **Course role:** direct source for Day 20 — Circuit Breaker, Bulkhead, Timeout, Steady State transfer almost verbatim to agent loops.

---

## Landmark papers

**Yao et al., "ReAct: Synergizing Reasoning and Acting in Language Models," ICLR 2023, arXiv:2210.03629.** Interleaving reasoning and action — the modern loop. **Course role:** Day 7 (why a loop), Day 8 (build it). Read §2.

**Wei et al., "Chain-of-Thought Prompting Elicits Reasoning in LLMs," NeurIPS 2022, arXiv:2201.11903.** Why reasoning-in-tokens works. **Course role:** Day 3 (prompting), Day 8. Read §3 + appendix exemplars.

**Schick et al., "Toolformer: Language Models Can Teach Themselves to Use Tools," 2023, arXiv:2302.04761.** Tools as API calls the model emits. **Course role:** Day 5 (tools), Day 6 (tool interface). Read §2.

**Shinn et al., "Reflexion: Language Agents with Verbal Reinforcement Learning," NeurIPS 2023, arXiv:2303.11366.** Verbal self-correction. **Course role:** Day 20 — the model-side complement to engineered retries. Read §3.

**Liu et al., "Lost in the Middle: How Language Models Use Long Contexts," TACL 2024, arXiv:2307.03172.** Models attend to start/end, not middle (the U-curve). **Course role:** the evidence base for Day 9 and Day 12; underlies Day 13's placement patterns. Read §3–4.

**Packer et al., "MemGPT: Towards LLMs as Operating Systems," 2023, arXiv:2310.08560.** Tiered memory + paging via function calls. **Course role:** Day 2 (harness-as-OS), Day 14 (memory tiers), Day 12 (paging = compaction). Read §3.

**Sumers, Yao, Narasimhan, Griffiths, "Cognitive Architectures for Language Agents (CoALA)," TMLR 2024, arXiv:2309.02427.** Formal vocabulary for memory/action/decision. **Course role:** the scaffold under Day 14 (memory) and Day 17 (control). Read §3–4.

**Park et al., "Generative Agents: Interactive Simulacra of Human Behavior," 2023, arXiv:2304.03442.** Memory-stream + retrieval + reflection. **Course role:** long-term-memory design for Day 14; proto-graph memory for Day 23. Read §4.

**Lewis et al., "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks," NeurIPS 2020, arXiv:2005.11401.** The parametric/non-parametric framing; origin of RAG. **Course role:** Day 15. Read §1 and the framing.

**Jimenez et al., "SWE-bench: Can Language Models Resolve Real-World GitHub Issues?," ICLR 2024, arXiv:2310.06770.** Real-repo trajectory eval. **Course role:** Day 21 and the capstone target. Read §2–3.

**Yao, Shinn, Razavi, Narasimhan, "τ-bench: A Benchmark for Tool-Agent-User Interaction in Real-World Domains," 2024, arXiv:2406.12045.** Dynamic user conversations; the pass^k reliability metric. **Course role:** Day 21 and the alternate capstone. Read §3.

**Wu et al., "AutoGen: Enabling Next-Gen LLM Applications via Multi-Agent Conversation Framework," 2023, arXiv:2308.08155.** Agents as conversational participants that delegate. **Course role:** Day 19 (multi-agent). Read §2–3; note the cost over a single loop.

**Edge et al., "From Local to Global: A Graph RAG Approach to Query-Focused Summarization," 2024, arXiv:2404.16130.** Builds an entity knowledge graph from a corpus, summarizes graph communities. **Course role:** Day 23 (context graphs) and Day 15 (the multi-hop limit of flat RAG). Read §2.

**Rasmussen et al., "Zep: A Temporal Knowledge Graph Architecture for Agent Memory," 2025, arXiv:2501.13956.** Agent memory as a temporal knowledge graph — facts carry validity intervals + provenance. **Course role:** the technical backbone of Day 23. Read §3. Open-source engine: **Graphiti** ([github.com/getzep/graphiti](https://github.com/getzep/graphiti)).

**Malewicz et al., "Pregel: A System for Large-Scale Graph Processing," SIGMOD 2010, pp. 135–146.** Vertex-centric graph computation (BSP / supersteps) — the compute model LangGraph inherits. **Course role:** Day 24 (control-flow graphs). Read the intro + vertex-centric model.

---

## Video lectures / talks

**Andrej Karpathy, "Software Is Changing (Again)," YC AI Startup School, June 2025** — [youtube.com/watch?v=LCEmiRjPEtQ](https://www.youtube.com/watch?v=LCEmiRjPEtQ). "LLMs are the runtime, agents the unit of abstraction." **Course role:** Day 1 and Day 2 framing. First ~20 min.

**Dex Horthy (HumanLayer), "12-Factor Agents: Patterns of Reliable LLM Applications," AI Engineer, 2025** — [youtube.com/watch?v=8kMaTybvDUw](https://www.youtube.com/watch?v=8kMaTybvDUw); repo [github.com/humanlayer/12-factor-agents](https://github.com/humanlayer/12-factor-agents). Production harness/loop patterns (own your context/control flow, stateless reducer). **Course role:** recurring spine — Days 8, 10, 14, 17, 18, 20.

**Barry Zhang (Anthropic), "How We Build Effective Agents," AI Engineer Summit, 2025** — [youtube.com/watch?v=D7_ipDqhtwk](https://www.youtube.com/watch?v=D7_ipDqhtwk). Don't over-use agents; keep them simple; think from the agent's perspective. **Course role:** Day 9 and Day 19.

**DeepLearning.AI, "Functions, Tools and Agents with LangChain," 2024** — [deeplearning.ai](https://www.deeplearning.ai/short-courses/functions-tools-agents-langchain/). Hands-on tool-calling. **Course role:** optional companion to Days 5–6.

**DeepLearning.AI, "AI Agents in LangGraph," 2024** (Harrison Chase & Rotem Weiss) — [deeplearning.ai](https://www.deeplearning.ai/courses/ai-agents-in-langgraph/). Build an agent from scratch, then rebuild in LangGraph. **Course role:** companion to Day 24 — the framework version of the graph you hand-roll.

---

## Essential engineering writing

**Anthropic, "Building Effective Agents," Dec 2024** — [link](https://www.anthropic.com/engineering/building-effective-agents). Workflow-vs-agent patterns. **Course role:** Days 13, 17, 19, 24; the capstone's vocabulary.

**Anthropic, "Effective Harnesses for Long-Running Agents," 2025** — [link](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents). Keeping long runs coherent and controlled. **Course role:** Days 17, 20, 22, 24.

**Anthropic, "Effective Context Engineering for AI Agents," Sept 2025** — [link](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents). Context editing, compaction, memory tools. **Course role:** the bottleneck spine — Days 9, 10, 12, 13.

**Anthropic, "Writing Effective Tools for AI Agents," 2025** — [link](https://www.anthropic.com/engineering/writing-tools-for-agents). Designing tool interfaces a model can use. **Course role:** Days 5, 6.

**Anthropic, "Prompt Engineering" documentation** — docs.claude.com. System prompts, being clear and direct, multishot examples. **Course role:** Day 3.

**Hamel Husain, "Your AI Product Needs Evals," hamelhusain.substack.com, 2024** — [link](https://hamelhusain.substack.com/p/evals). Why eval systems beat vibes. **Course role:** Day 21 spine.

**swyx (Shawn Wang), "The Rise of the AI Engineer," Latent.Space, June 2023** — [link](https://www.latent.space/p/ai-engineer). The essay that named the discipline. **Course role:** Day 1.

---

## Optional deeper dives

- **Madaan et al., "Self-Refine," NeurIPS 2023, arXiv:2303.17651** — model-side iterative improvement; complements Day 20.
- **Wang et al., "Voyager," 2023, arXiv:2305.16291** — a skill library as growing procedural memory; extends Day 14.
- **Wang, Ma, Feng, et al., "A Survey on LLM-based Autonomous Agents," 2023, arXiv:2308.11432** — orientation map; skim before Day 8.
- **Yao et al., "Tree of Thoughts," NeurIPS 2023, arXiv:2305.10601** — search over reasoning states; an advanced control structure beyond Days 17/24.
