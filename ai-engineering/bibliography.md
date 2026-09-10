# Bibliography — The Course Shelf

Every source is real and verified, with enough to locate the exact chapter/section/timestamp. "Suggested readings" links from day pages point here. Day numbers refer to the 30-day path.

---

## Foundational books

**Chip Huyen, *AI Engineering: Building Applications with Foundation Models*, O'Reilly, 2025.**
The defining book on engineering *around* foundation models. **Course role:** the spine — Day 1 (what AI eng is), Days 3–4 (model selection & failure modes), Day 5 (prompting), Day 6 (structured output), Day 17 (RAG), Day 23 (evaluation), Day 24 (cost). Skim Ch. 1 before Day 1.

**Martin Kleppmann, *Designing Data-Intensive Applications*, O'Reilly, 2017.**
How reliable stateful systems are built. **Course role:** the state/reliability mindset behind Day 16 (memory) and Day 22 (failure). Read Ch. 1 as background for the production arc.

**Michael T. Nygard, *Release It!*, 2nd ed., Pragmatic Bookshelf, 2018.**
The canonical stability-patterns catalog. **Course role:** direct source for Day 22 — Circuit Breaker, Bulkhead, Timeout, Steady State.

---

## Landmark papers

**Yao et al., "ReAct," ICLR 2023, arXiv:2210.03629.** The modern loop. **Course role:** Days 9–10. Read §2.

**Wei et al., "Chain-of-Thought Prompting," NeurIPS 2022, arXiv:2201.11903.** Why reasoning-in-tokens works. **Course role:** Days 5, 10. Read §3.

**Schick et al., "Toolformer," 2023, arXiv:2302.04761.** Tools as API calls the model emits. **Course role:** Days 7–8. Read §2.

**Shinn et al., "Reflexion," NeurIPS 2023, arXiv:2303.11366.** Verbal self-correction. **Course role:** Day 22. Read §3.

**Liu et al., "Lost in the Middle," TACL 2024, arXiv:2307.03172.** The U-curve of context attention. **Course role:** the evidence base for Day 4 (a measured failure mode), Day 11, Day 14; underlies Day 15's placement patterns. Read §3–4.

**Packer et al., "MemGPT," 2023, arXiv:2310.08560.** Tiered memory + paging. **Course role:** Day 2 (harness-as-OS), Day 16 (memory tiers), Day 14 (paging = compaction). Read §3.

**Sumers et al., "Cognitive Architectures for Language Agents (CoALA)," TMLR 2024, arXiv:2309.02427.** Formal vocabulary for memory/action/decision. **Course role:** Day 16 (memory), Day 19 (control). Read §3–4.

**Park et al., "Generative Agents," 2023, arXiv:2304.03442.** Memory-stream + retrieval + reflection. **Course role:** Day 16 (long-term memory); proto-graph memory for Day 25. Read §4.

**Lewis et al., "Retrieval-Augmented Generation," NeurIPS 2020, arXiv:2005.11401.** Parametric/non-parametric framing; origin of RAG. **Course role:** Day 17. Read §1.

**Jimenez et al., "SWE-bench," ICLR 2024, arXiv:2310.06770.** Real-repo trajectory eval. **Course role:** Day 23 and the capstone target. Read §2–3.

**Yao, Shinn, Razavi, Narasimhan, "τ-bench," 2024, arXiv:2406.12045.** Dynamic user conversations; the pass^k metric. **Course role:** Day 23 and the alternate capstone. Read §3.

**Wu et al., "AutoGen," 2023, arXiv:2308.08155.** Agents as conversational participants that delegate. **Course role:** Day 21 (multi-agent). Read §2–3.

**Edge et al., "From Local to Global: A Graph RAG Approach," 2024, arXiv:2404.16130.** Entity knowledge graph from a corpus + community summaries. **Course role:** Day 25 (context graphs), Day 17 (flat-RAG's multi-hop limit), Day 27 (ontology as extraction target). Read §2.

**Rasmussen et al., "Zep: A Temporal Knowledge Graph Architecture for Agent Memory," 2025, arXiv:2501.13956.** Agent memory as a temporal knowledge graph. **Course role:** backbone of Day 25; entity/edge typing for Day 27. Read §3. Engine: **Graphiti** ([github.com/getzep/graphiti](https://github.com/getzep/graphiti)).

**Malewicz et al., "Pregel: A System for Large-Scale Graph Processing," SIGMOD 2010, pp. 135–146.** Vertex-centric graph computation (BSP/supersteps) — the compute model LangGraph inherits. **Course role:** Day 26 (control-flow graphs). Read the intro + vertex-centric model.

---

## Foundational references (ontology / shared meaning)

**Noy & McGuinness, "Ontology Development 101: A Guide to Creating Your First Ontology," Stanford KSL Technical Report KSL-01-05, 2001.**
The most practical on-ramp to building an ontology: the class/relation/instance vocabulary and a 7-step method scoped by competency questions. **Course role:** Days 27–28. Read the method; skip the Protégé-specific bits.

**W3C, "Shapes Constraint Language (SHACL)," W3C Recommendation, 2017** — [w3.org/TR/shacl](https://www.w3.org/TR/shacl/).
The standard vocabulary for validating knowledge graphs against constraints (cardinality, value-type, value-range, node shapes). **Course role:** Day 28 — the checklist of what to validate; you can hand-roll the same logic.

---

## Video lectures / talks

**Andrej Karpathy, "Software Is Changing (Again)," YC AI Startup School, June 2025** — [youtube.com/watch?v=LCEmiRjPEtQ](https://www.youtube.com/watch?v=LCEmiRjPEtQ). "LLMs are the runtime; agents the unit of abstraction"; the "jagged intelligence" framing. **Course role:** Days 1–2 framing, Day 4 (jagged capability). First ~20 min.

**Dex Horthy (HumanLayer), "12-Factor Agents," AI Engineer, 2025** — [youtube.com/watch?v=8kMaTybvDUw](https://www.youtube.com/watch?v=8kMaTybvDUw); repo [github.com/humanlayer/12-factor-agents](https://github.com/humanlayer/12-factor-agents). Production harness/loop patterns. **Course role:** recurring spine — Days 10, 12, 16, 19, 20, 22.

**Barry Zhang (Anthropic), "How We Build Effective Agents," AI Engineer Summit, 2025** — [youtube.com/watch?v=D7_ipDqhtwk](https://www.youtube.com/watch?v=D7_ipDqhtwk). **Course role:** Day 11 ("think from the agent's perspective") and Day 21 (when not to add agents).

**DeepLearning.AI, "AI Agents in LangGraph," 2024** (Harrison Chase & Rotem Weiss) — [deeplearning.ai](https://www.deeplearning.ai/courses/ai-agents-in-langgraph/). Build an agent from scratch, then rebuild in LangGraph. **Course role:** companion to Day 26.

**DeepLearning.AI, "Functions, Tools and Agents with LangChain," 2024** — [deeplearning.ai](https://www.deeplearning.ai/short-courses/functions-tools-agents-langchain/). **Course role:** optional companion to Days 7–8.

---

## Essential engineering writing

**Anthropic, "Building Effective Agents," Dec 2024** — [link](https://www.anthropic.com/engineering/building-effective-agents). Workflow-vs-agent patterns. **Course role:** Days 15, 21, 26; the capstone's vocabulary.

**Anthropic, "Effective Harnesses for Long-Running Agents," 2025** — [link](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents). **Course role:** Days 19, 22, 24, 26.

**Anthropic, "Effective Context Engineering for AI Agents," Sept 2025** — [link](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents). **Course role:** the bottleneck spine — Days 11, 12, 14, 15.

**Anthropic, "Writing Effective Tools for AI Agents," 2025** — [link](https://www.anthropic.com/engineering/writing-tools-for-agents). **Course role:** Days 7–8.

**Anthropic, "Prompt Engineering" documentation** — docs.claude.com. **Course role:** Day 5.

**Hamel Husain, "Your AI Product Needs Evals," hamelhusain.substack.com, 2024** — [link](https://hamelhusain.substack.com/p/evals). **Course role:** Day 23 spine.

**swyx (Shawn Wang), "The Rise of the AI Engineer," Latent.Space, June 2023** — [link](https://www.latent.space/p/ai-engineer). The essay that named the discipline. **Course role:** Day 1.

---

## Optional deeper dives

- **Madaan et al., "Self-Refine," NeurIPS 2023, arXiv:2303.17651** — model-side iterative improvement; complements Day 22.
- **Wang et al., "Voyager," 2023, arXiv:2305.16291** — a skill library as growing procedural memory; extends Day 16.
- **Wang, Ma, Feng, et al., "A Survey on LLM-based Autonomous Agents," 2023, arXiv:2308.11432** — orientation map; skim before Day 10.
- **Yao et al., "Tree of Thoughts," NeurIPS 2023, arXiv:2305.10601** — search over reasoning states; an advanced control structure beyond Days 19/26.
