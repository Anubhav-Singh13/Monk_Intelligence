# Bibliography — The Course Shelf

Every source below is real and verified. Citations give enough to locate the exact chapter, section, or timestamp. When you follow a "Suggested readings" link from a day page, this is where it points.

---

## Foundational books

**Chip Huyen, *AI Engineering: Building Applications with Foundation Models*, O'Reilly, 2025.**
The best single book on engineering *around* models rather than training them. Favors a systems-practitioner's intuition: evaluation, inference cost, latency, and reliability as first-class concerns. Hardest in its evaluation chapters, which repay the effort. **Course role:** spine for the production arc — Day 15 (evaluation), Day 16 (cost/observability). Skim Ch. 1 before Day 1 for the landscape.

**Martin Kleppmann, *Designing Data-Intensive Applications*, O'Reilly, 2017.**
Not about LLMs at all — about how reliable stateful systems are built. Favors the intuition that state and failure are the hard parts of any system. Hardest in the consistency/consensus chapters (not needed here). **Course role:** the reliability and state mindset behind Day 10 (memory/state) and Day 14 (failure). Read Ch. 1 ("Reliable, Scalable, Maintainable") as background for the whole production arc.

**Michael T. Nygard, *Release It!: Design and Deploy Production-Ready Software*, 2nd ed., Pragmatic Bookshelf, 2018.**
The canonical catalog of production stability patterns. Favors war-story intuition — every pattern comes from an outage. **Course role:** direct source for Day 14 — the Circuit Breaker, Bulkhead, Timeout, and Steady State patterns transfer almost verbatim to agent loops. Read the "Stability Patterns" chapter before Day 14.

---

## Landmark papers

**Yao, Zhao, Yu, Du, Shafran, Narasimhan, Cao, "ReAct: Synergizing Reasoning and Acting in Language Models," ICLR 2023, arXiv:2210.03629.**
Introduces the interleaving of reasoning traces and actions that defines the modern agent loop. **Course role:** backbone of Day 2 (why a loop) and Day 5 (first loop). Read §2 for the Thought/Action/Observation format you'll implement.

**Wei, Wang, Schuurmans, Bosma, Ichter, Xia, Chi, Le, Zhou, "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models," NeurIPS 2022, arXiv:2201.11903.**
Shows that generating intermediate reasoning tokens improves problem-solving — the substrate ReAct's "Thought" step relies on. **Course role:** Day 2, Day 5. Read §3 and the exemplars in the appendix.

**Schick, Dwivedi-Yu, Dessì, Raileanu, Lomeli, Zettlemoyer, Cancedda, Scialom, "Toolformer: Language Models Can Teach Themselves to Use Tools," 2023, arXiv:2302.04761.**
The foundational framing of tools as API calls a model can emit. **Course role:** Day 3 (tools), Day 6 (tool interface). Read §2 for the tool-call abstraction; skip the self-supervised training details unless curious.

**Shinn, Cassano, Berman, Gopinath, Narasimhan, Yao, "Reflexion: Language Agents with Verbal Reinforcement Learning," NeurIPS 2023, arXiv:2303.11366.**
An agent reflects in language on its failures and retries better. **Course role:** Day 14 (failure/recovery) — the model-side complement to engineered retries. Read §3 (the Reflexion loop).

**Liu, Lin, Hewitt, Paranjape, Bevilacqua, Petroni, Liang, "Lost in the Middle: How Language Models Use Long Contexts," TACL 2024, arXiv:2307.03172.**
Empirically: models attend well to the start and end of context and poorly to the middle. **Course role:** the evidence base for Day 4 (context matters) and Day 11 (where you place things matters). Read §3–4 and the U-shaped performance curve — it justifies half your compaction decisions.

**Packer, Wooders, Lin, Fang, Patil, Stoica, Gonzalez, "MemGPT: Towards LLMs as Operating Systems," 2023, arXiv:2310.08560.**
Virtual context management: tiered memory (in-context vs external) with the model paging data in and out via function calls and interrupts. Favors the operating-systems intuition explicitly. **Course role:** one of the most load-bearing papers here — Day 1 (harness-as-OS), Day 10 (memory tiers), Day 11 (paging = compaction). Read §3 (the memory hierarchy) closely.

**Sumers, Yao, Narasimhan, Griffiths, "Cognitive Architectures for Language Agents (CoALA)," TMLR 2024, arXiv:2309.02427.**
A clean formal vocabulary decomposing an agent into memory (working/episodic/semantic/procedural), action space, and decision procedure. **Course role:** the formal scaffold under Day 10 (memory) and Day 12 (decision/control). Read §3–4; use its taxonomy to name what you build.

**Park, O'Brien, Cai, Morris, Liang, Bernstein, "Generative Agents: Interactive Simulacra of Human Behavior," 2023, arXiv:2304.03442.**
The memory-stream + retrieval + reflection architecture, demonstrated in a believable simulated town. **Course role:** concrete long-term-memory design for Day 10. Read §4 (memory stream, retrieval scoring by recency/importance/relevance).

**Jimenez, Yang, Wettig, Yao, Pei, Press, Narasimhan, "SWE-bench: Can Language Models Resolve Real-World GitHub Issues?," ICLR 2024, arXiv:2310.06770.**
Evaluates agents on real repository issues with real test suites as the grader. **Course role:** Day 15 (evaluation) and the capstone target. Read §2–3 for the task construction and the pass criterion.

**Yao, Shinn, Razavi, Narasimhan, "τ-bench: A Benchmark for Tool-Agent-User Interaction in Real-World Domains," 2024, arXiv:2406.12045.**
Evaluates agents in dynamic user conversations under domain policies; introduces the pass^k reliability metric. **Course role:** Day 15 — the reliability/consistency angle on eval, and the alternate capstone target. Read §3 (methodology) and the pass^k definition.

**Wu, Bansal, Zhang, Wu, Zhang, Zhu, Li, Jiang, Zhang, Wang, "AutoGen: Enabling Next-Gen LLM Applications via Multi-Agent Conversation Framework," 2023, arXiv:2308.08155.**
Agents as conversational participants that delegate to one another. **Course role:** Day 17 (multi-agent). Read §2–3 for the conversation abstraction; note where it adds cost over a single loop.

---

## Video lectures / talks

**Andrej Karpathy, "Software Is Changing (Again)," YC AI Startup School, June 2025** — [youtube.com/watch?v=LCEmiRjPEtQ](https://www.youtube.com/watch?v=LCEmiRjPEtQ).
"English is the interface, LLMs are the runtime, agents the unit of abstraction." **Course role:** the Day 1 harness-as-OS framing, updated from his 2023 LLM talk. Watch the first ~20 min (the Software 1.0/2.0/3.0 framing).

**Dex Horthy (HumanLayer), "12-Factor Agents: Patterns of Reliable LLM Applications," AI Engineer, 2025** — [youtube.com/watch?v=8kMaTybvDUw](https://www.youtube.com/watch?v=8kMaTybvDUw); companion repo [github.com/humanlayer/12-factor-agents](https://github.com/humanlayer/12-factor-agents).
The most directly on-topic talk in the field: patterns distilled from ~100 production agents (own your context window, own your control flow, agents as stateless reducers, tools as structured output). **Course role:** recurring spine — Days 5, 7, 10, 12, 14. Watch the whole talk after Day 5; re-skim the repo's factor list before Day 13.

**Barry Zhang (Anthropic), "How We Build Effective Agents," AI Engineer Summit, 2025** — [youtube.com/watch?v=D7_ipDqhtwk](https://www.youtube.com/watch?v=D7_ipDqhtwk).
Three principles: don't use agents for everything, keep them simple, think from the agent's perspective. **Course role:** Day 4 ("think from the agent's perspective" = the model only sees the payload) and Day 17 (when *not* to add agents).

**DeepLearning.AI, "Functions, Tools and Agents with LangChain," 2024** — [deeplearning.ai](https://www.deeplearning.ai/short-courses/functions-tools-agents-langchain/).
Hands-on tool-calling and function schemas. **Course role:** optional practical companion to Day 6.

**DeepLearning.AI, "AI Agents in LangGraph," 2024** (Harrison Chase & Rotem Weiss) — [deeplearning.ai](https://www.deeplearning.ai/courses/ai-agents-in-langgraph/).
Build an agent from scratch, then rebuild it in LangGraph; covers control flow, agentic memory, human-in-the-loop. **Course role:** optional companion to Day 12 — useful contrast between your hand-rolled loop and a graph framework's version.

---

## Essential engineering writing (the field's living canon)

**Anthropic, "Building Effective Agents," anthropic.com/engineering, Dec 2024** — [link](https://www.anthropic.com/engineering/building-effective-agents).
The workflow-vs-agent distinction and a catalog of composable patterns (prompt chaining, routing, orchestrator-workers, evaluator-optimizer). **Course role:** Day 12 and Day 17; the capstone's architecture vocabulary.

**Anthropic, "Effective Harnesses for Long-Running Agents," anthropic.com/engineering, 2025** — [link](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents).
Directly on this course's topic: keeping an agent coherent and controlled over long runs. **Course role:** production-arc spine — Days 12, 14, 16.

**Anthropic, "Effective Context Engineering for AI Agents," anthropic.com/engineering, Sept 2025** — [link](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents).
The shift from prompt to context engineering: context editing, compaction, memory tools, context-awareness. **Course role:** the current best-practice reference for the bottleneck — Days 4, 7, 11.

**Anthropic, "Writing Effective Tools for AI Agents," anthropic.com/engineering, 2025** — [link](https://www.anthropic.com/engineering/writing-tools-for-agents).
How to design tool interfaces a model can actually use reliably. **Course role:** Day 6.

**Hamel Husain, "Your AI Product Needs Evals," hamelhusain.substack.com, 2024** — [link](https://hamelhusain.substack.com/p/evals).
Why eval systems (not vibes) separate products that ship from those that don't; unit-test-style assertions and LLM-as-judge. **Course role:** Day 15 spine.

**swyx (Shawn Wang), "The Rise of the AI Engineer," Latent.Space, June 2023** — [link](https://www.latent.space/p/ai-engineer).
The essay that named the discipline. **Course role:** framing for Day 1 — why the harness is now the job.

---

## Optional deeper dives

- **Madaan et al., "Self-Refine: Iterative Refinement with Self-Feedback," NeurIPS 2023, arXiv:2303.17651** — model-side iterative improvement; complements Day 14.
- **Wang et al., "Voyager: An Open-Ended Embodied Agent with LLMs," 2023, arXiv:2305.16291** — a skill library as growing procedural memory; extends Day 10.
- **Wang, Ma, Feng, et al., "A Survey on Large Language Model based Autonomous Agents," 2023, arXiv:2308.11432** — orientation map (profile/memory/planning/action); skim before Day 5.
- **Yao et al., "Tree of Thoughts," NeurIPS 2023, arXiv:2305.10601** — search over reasoning states; a more advanced control structure beyond Day 12.
