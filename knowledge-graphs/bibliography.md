# Bibliography — Annotated Course Shelf

All sources cited in this course. Every entry includes a precise location (chapter / section / timestamp) for the content most relevant to the days that cite it.

---

## Foundational Books

### 1. Hogan, Aidan, Eva Blomqvist, Marc Groth, et al. *Knowledge Graphs.* MIT Press, 2021. ISBN 978-0-262-54238-8.

Also available as an arXiv preprint: arXiv:2003.02320 (freely accessible; identical to the book in content through the 2021 edition).

**What it covers:** The single most comprehensive modern treatment of knowledge graphs. Covers the full arc from graph data models (RDF, property graphs) through schema languages (RDFS, OWL, SHACL), knowledge graph construction (NLP extraction, entity linking), embeddings, and graph neural networks. Written by an international team of researchers who have built and studied production KGs.

**Whose intuition it favors:** Semantic web / linked data tradition, but Chapter 11 (embeddings) is squarely in the ML camp. Formal but readable; each chapter is largely self-contained.

**Where it's hardest:** Chapter 5 (Ontology-Mediated Query Answering) and Chapter 9 (Symbolic Reasoning) go deep into description logics — skip these for L1/L2 unless you need them.

**Days that rely on it most:** Days 1 (Ch. 1), 2 (Ch. 1–2), 4 (Ch. 2), 7 (Ch. 5 §5.1–5.3), 11 (Ch. 11), 13 (Ch. 3), 14 (Ch. 8).

---

### 2. Robinson, Ian, Jim Webber, and Emil Eifrem. *Graph Databases: New Opportunities for Connected Data.* O'Reilly Media, 2nd ed., 2015. ISBN 978-1-491-93200-1.

Free online edition at: neo4j.com/graph-databases-book/

**What it covers:** The definitive guide to the property graph model and Neo4j. Chapter 2 introduces the Labeled Property Graph (LPG) model. Chapter 3 covers Cypher in depth. Chapter 4 has excellent schema design patterns (entity-centric, hyper-relational, event-centric). Chapter 7 covers deployment and scaling.

**Whose intuition it favors:** Engineering and product-focused. Every concept is grounded in working Cypher and real use cases. Lighter on theory than Hogan et al.

**Where it's hardest:** Chapter 6 (Graph Model vs. Relational Model) is useful but can feel repetitive if you already understand the difference by Day 5.

**Days that rely on it most:** Days 3 (Ch. 2), 5 (Ch. 3), 8 (Ch. 3–4), 13 (Ch. 4), 14 (Ch. 7).

---

### 3. Heath, Tom, and Christian Bizer. *Linked Data: Evolving the Web into a Global Data Space.* Morgan & Claypool, 2011. DOI: 10.2200/S00334ED1V01Y201102WBE001.

Freely available as PDF: linkeddatabook.com/editions/1.0/

**What it covers:** A short (~160 pp) but foundational text on the Linked Data principles and the RDF data model. Chapter 2 explains IRIs and the triple model with clarity. Chapter 3 covers vocabularies and ontologies at an approachable level. Chapter 4 introduces SPARQL.

**Whose intuition it favors:** Web/semantic web tradition. Great for understanding *why* RDF was designed the way it was — the Web as a universal graph.

**Where it's hardest:** Chapter 5 (Linked Data Applications) is showing its age; the tooling examples are outdated. Read for principles, not for code.

**Days that rely on it most:** Days 2 (Ch. 2), 4 (Ch. 3), 5 (Ch. 4).

---

## Landmark Papers

### 4. Bordes, Antoine, Nicolas Usunier, Alberto Garcia-Duran, Jason Weston, and Oksana Yakhnenko. "Translating Embeddings for Modeling Multi-relational Data." *Advances in Neural Information Processing Systems (NeurIPS)*, 2013.

Search: "TransE NeurIPS 2013" or "Translating Embeddings Bordes 2013"

**What it covers:** Introduces TransE, the foundational geometric intuition for KG embeddings: a relation `r` between head `h` and tail `t` is modelled as a translation `h + r ≈ t` in a vector space. Simple, fast, and still the baseline everything else is measured against.

**Why it matters for this course:** Day 11's intuition section is built around this paper's geometric picture. Reading the first 4 pages (through §3) is enough to get the full insight.

**Limitation to be aware of:** TransE cannot model symmetric relations (if `h + r ≈ t`, it cannot also have `t + r ≈ h` unless `r = 0`). Day 11 covers this and introduces RotatE as the fix.

---

### 5. Sun, Zhiqing, Zhi-Hong Deng, Jian-Yun Nie, and Jian Tang. "RotatE: Knowledge Graph Embedding by Relational Rotation in Complex Space." *International Conference on Learning Representations (ICLR)*, 2019. arXiv:1902.10197.

**What it covers:** Extends TransE by modelling relations as rotations in the complex plane rather than translations in real space. This allows RotatE to represent symmetric, antisymmetric, inverse, and composition relations — all the patterns TransE cannot handle.

**Why it matters for this course:** Paired with TransE on Day 11 to show what an embedding model needs to express when the world has symmetric relationships (e.g., `is_sibling_of`, `co-author_with`).

**Key section:** §3 (Model) and §4 (Relation Patterns) are the essential read — about 3 pages.

---

### 6. Vrandecic, Denny, and Markus Krötzsch. "Wikidata: A Free Collaborative Knowledgebase." *Communications of the ACM* 57, no. 10 (2014): 78–85. DOI: 10.1145/2629489.

**What it covers:** Describes the design and evolution of Wikidata, the largest freely available knowledge graph (~100M items, ~1B statements). Explains how the ontology was designed to be multilingual and maintainable at scale.

**Why it matters for this course:** Used on Day 4 (ontology design) and Day 14 (production concerns) as a concrete existence proof that large-scale KG ontology maintenance is possible. The "data model" section (§3) is the key read.

---

### 7. Edge, Darren, Ha Trinh, Newman Cheng, Joshua Bradley, Alex Chao, Apurva Mody, Steven Truitt, and Jonathan Larson. "From Local to Global: A Graph RAG Approach to Query-Focused Summarization." Microsoft Research, 2024. arXiv:2404.16130.

Code: github.com/microsoft/graphrag

**What it covers:** Introduces GraphRAG — a retrieval approach that first extracts a KG from a corpus, then uses community detection to build hierarchical summaries, and finally retrieves over the graph structure rather than raw text. The central insight: some questions require global reasoning over a corpus that no single chunk answers.

**Why it matters for this course:** Primary source for Day 9. Section 3 ("Method") is the essential read. The GitHub repo is the working implementation adapted in Days 9–10.

**Key limitation:** GraphRAG's community-summarisation step is expensive (many LLM calls). Day 9 discusses when to use it vs. simpler KG-backed retrieval.

---

### 8. Wang, Quan, Zhendong Mao, Bin Wang, and Li Guo. "Knowledge Graph Embedding: A Survey of Approaches and Applications." *IEEE Transactions on Knowledge and Data Engineering* 29, no. 12 (2017): 2724–2743. DOI: 10.1109/TKDE.2017.2754499.

**What it covers:** A comprehensive survey of the KG embedding landscape: translation-based models (TransE, TransR, TransH), semantic matching models (RESCAL, DistMult, ComplEx), and applications (link prediction, triple classification, relation extraction).

**Why it matters for this course:** Used on Day 11 as the "map of the territory." Table 1 (model comparison) and §4 (applications) are the key reads if you want to know which embedding model to reach for given specific constraints.

---

### 9. Pan, Shirui, Linhao Luo, Yufei Wang, Chen Chen, Jiapu Wang, and Xindong Wu. "Unifying Large Language Models and Knowledge Graphs: A Roadmap." *IEEE Transactions on Knowledge and Data Engineering* 36, no. 7 (2024): 3580–3599. DOI: 10.1109/TKDE.2023.3352100. arXiv:2306.08302.

**What it covers:** A systematic survey of the intersection between LLMs and KGs. Categorises three synergy directions: (1) KG-enhanced LLMs (grounding LLM outputs in structured facts), (2) LLM-augmented KGs (using LLMs for KG construction and completion), and (3) synergised KG + LLM systems (where both operate together, as in GraphRAG and agent memory). Covers relevant benchmarks, datasets, and open challenges as of mid-2024.

**Whose intuition it favors:** Systems-level synthesis rather than any single model. The taxonomy in Figure 1 (§2) is the clearest single diagram for understanding where this course's work sits in the broader landscape.

**Where it's hardest:** §4–5 (KG-enhanced LLMs and downstream tasks) assume familiarity with fine-tuning and RLHF — skim these if your focus is the practitioner use case.

**Days that rely on it most:** Days 9 (§3.2, LLM-augmented KG construction mirrors the GraphRAG pipeline) and 10 (§3.3, synergised systems covers agent memory architectures directly). Recommended reading after Day 10 as a "where to go next" map.

---

### 10. Lewis, Patrick, Ethan Perez, Aleksandra Piktus, Fabio Petroni, Vladimir Karpukhin, Naman Goyal, Heinrich Küttler, Mike Lewis, Wen-tau Yih, Tim Rocktäschel, Sebastian Riedel, and Douwe Kiela. "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks." *Advances in Neural Information Processing Systems (NeurIPS)*, 2020. arXiv:2005.11401.

**What it covers:** Introduces Retrieval-Augmented Generation (RAG) — the architecture that combines a parametric LLM with a non-parametric retrieval component (a dense vector index over a document corpus). Establishes the baseline that Day 9's GraphRAG is designed to improve upon.

**Whose intuition it favors:** NLP research tradition. The paper's framing of "open-domain QA as a retrieval + generation problem" is the cleanest intellectual predecessor to the "KG-backed retrieval" idea.

**Where it's hardest:** §3 (model architecture) is relevant if you want to understand RAG mechanistically; the experiments (§4–5) are useful for understanding what benchmark tasks plain RAG handles well vs. poorly — which is precisely what motivates GraphRAG.

**Days that rely on it most:** Day 9. The hook on Day 9 ("plain vector RAG fails for global questions") only lands with full force if you understand what plain RAG is doing and why it was considered a breakthrough in 2020. Read §1–2 (3 pages) before or alongside Day 9 if you want the intellectual lineage clear.

---

### 11. Yao, Shunyu, Jeffrey Zhao, Dian Yu, Nan Du, Izhak Shafran, Karthik Narasimhan, and Yuan Cao. "ReAct: Synergizing Reasoning and Acting in Language Models." *International Conference on Learning Representations (ICLR)*, 2023. arXiv:2210.03629.

**What it covers:** Introduces ReAct — a prompting framework that interleaves *reasoning traces* (chain-of-thought) with *actions* (tool calls and environment observations). The agent alternates between thinking ("I need to look up Alice's employer") and acting (calling a search or database tool). This interleaving substantially outperforms both pure chain-of-thought and pure action-only agents on multi-hop QA and decision tasks.

**Whose intuition it favors:** Language model agents and tool use. The worked examples in §3 (HotpotQA traces) are the clearest illustration of why structured memory (like a KG) is more useful to a ReAct agent than a flat text corpus.

**Where it's hardest:** §4 (ALFWorld) covers embodied agents in a simulated household — tangential for this course. Focus on §2–3 (method and QA experiments).

**Days that rely on it most:** Day 10. The `MemoryAgent` loop in Day 10 is exactly a ReAct agent where the "action" is a Cypher query and the "observation" is the graph result. Understanding that Day 10's architecture has a named, well-studied origin helps the learner situate it and extends naturally to frameworks like LangGraph and LlamaIndex that implement ReAct natively. Read §1–3 (6 pages) alongside or after Day 10.

---

## Video Lectures / Courses

### 9. Leskovec, Jure. *CS224W: Machine Learning with Graphs.* Stanford University, 2021.

Website: cs224w.stanford.edu  
YouTube: search "CS224W 2021 Stanford" for the full playlist.

**What it covers:** The best graph ML course available. Covers graph representation, spectral graph theory, node2vec, GNNs, KG completion, and reasoning. Lectures 1–3 (graph introduction, properties, and motifs) support Days 1–3. Lectures 7–9 (KG embeddings and completion) are required background for Day 11.

**Why it matters for this course:** Provides the mathematical foundations behind KG embeddings in a way that connects to your ML background. The lecture on "Knowledge Graphs" (Lecture 7) pairs perfectly with Day 11.

**Suggested viewing schedule:** Lecture 1 (first 20 min) alongside Day 1. Lecture 7 alongside Day 11.

---

### 10. Neo4j Graph Academy. *Neo4j Fundamentals + Cypher Fundamentals.* Free self-paced courses.

URL: graphacademy.neo4j.com

**What it covers:** Hands-on Cypher labs that pair with Days 3, 5, and 8. "Neo4j Fundamentals" (~1 hour) covers the property graph model interactively. "Cypher Fundamentals" (~2 hours) is the most efficient way to learn Cypher syntax with immediate feedback.

**Why it matters for this course:** These are labs, not lectures — use them as a companion to Days 3–5, not a substitute. Complete "Neo4j Fundamentals" before or alongside Day 3.

---

### 11. Microsoft Research. *GraphRAG: Unlocking LLM Discovery on Narrative Private Data.* 2024.

Blog post: microsoft.com/en-us/research/blog/graphrag-unlocking-llm-discovery-on-narrative-private-data  
GitHub: github.com/microsoft/graphrag  
YouTube: search "GraphRAG Microsoft Research 2024"

**What it covers:** The blog post is the best first read for Day 9 — it explains the motivation and pipeline in plain language before you read the paper. The GitHub repo is the implementation you will adapt on Days 9–10.

**Why it matters for this course:** Treat the repo README and `graphrag/index/` source code as required reading alongside Day 9. Understanding what the library does under the hood is the difference between using GraphRAG and being able to debug and extend it.

---

## Arc 4 — Time, Procedure and Reason (Days 16–23)

Sources added for the extension arc. Entries marked `[VERIFY]` have a detail (usually a chapter or section number) that could not be confirmed online — check before citing.

### A1. Snodgrass, Richard T. *Developing Time-Oriented Database Applications in SQL.* Morgan Kaufmann, 1999. ISBN 978-1-55860-436-0.

Free PDF hosted by the author (search "Snodgrass tdbbook").

**What it covers:** The classic engineering treatment of valid-time, transaction-time and bitemporal tables — how to store them, query them, and modify them without corrupting history.

**Whose intuition it favors:** Database engineering. Every idea arrives as SQL and an edge case.

**Where it's hardest:** The bitemporal modification chapters — four timestamps and every combination of overlap. Read after Day 16's exercises, not before. `[VERIFY chapter numbers]`

**Days that rely on it most:** Day 16 (deep reading), Day 22.

---

### A2. Johnston, Tom. *Bitemporal Data: Theory and Practice.* Morgan Kaufmann, 2014. ISBN 978-0-12-408067-6.

**What it covers:** A modern, book-length theory of bitemporal data, including how it relates to the SQL:2011 standard.

**Whose intuition it favors:** Theory-first and terminology-heavy; the most rigorous treatment on the shelf.

**Where it's hardest:** The formal vocabulary takes time to absorb. Use it as a reference when Snodgrass or Fowler leave a case unexplained. `[VERIFY chapter list]`

**Days that rely on it most:** Day 16 (optional depth).

---

### A3. Fowler, Martin. "Bitemporal History." martinfowler.com, 7 April 2021. martinfowler.com/articles/bitemporal-history.html

**What it covers:** The two dimensions of history ("actual" and "record"), retroactive changes, correcting a correction, and append-only storage — in one short article with a payroll example.

**Whose intuition it favors:** Practitioner intuition; no formalism. The best 15-minute introduction to two clocks.

**Where it's hardest:** "Changing the Retroactive Change" — the case where a correction itself is corrected.

**Days that rely on it most:** Day 16 (required reading).

---

### A4. Rasmussen, Preston, Pavlo Paliychuk, Travis Beauvais, Jack Ryan, and Daniel Chalef. "Zep: A Temporal Knowledge Graph Architecture for Agent Memory." arXiv:2501.13956, 2025.

**What it covers:** The architecture behind Graphiti, an open-source temporal knowledge graph for agent memory. §2.1 defines the two timelines (T for events, T′ for ingestion); §2.2.3 covers temporal extraction and edge invalidation — how a new fact closes a contradicted edge instead of deleting it.

**Whose intuition it favors:** Agent-memory engineering; the closest match to this course's Neo4j stack (Graphiti runs on Neo4j).

**Where it's hardest:** The LLM-based contradiction-detection pipeline, which today's page deliberately skips.

**Days that rely on it most:** Day 16 (primary source), Day 21.

---

### A5. Taylor, Jeremy. "Reconstructing History with XTDB." CMU Future Data Systems seminar, 24 November 2025. youtube.com/watch?v=zzqDBSVljsQ

**What it covers:** A database with both clocks built into every row (`_valid_from/_valid_to`, `_system_from/_system_to`), and its SQL:2011-style query syntax.

**Whose intuition it favors:** Database internals — what a system gives you when bitemporality is built in rather than added on.

**Where it's hardest:** The storage-engine segments; skip them on first viewing.

**Days that rely on it most:** Day 16 (deep version).

---

### A6. van der Aalst, Wil M. P. *Process Mining: Data Science in Action.* 2nd ed., Springer, 2016. ISBN 978-3-662-49850-7. DOI: 10.1007/978-3-662-49851-4.

**What it covers:** Discovering processes from event logs, checking logs against a process model (conformance checking), and improving the model.

**Whose intuition it favors:** Process science, with Petri nets as the formal base. Rigorous but full of worked examples.

**Where it's hardest:** The alignment-based conformance chapters. Day 18's Exercise 7 is a deliberately simple version. `[VERIFY chapter numbers]`

**Days that rely on it most:** Day 18 (Exercise 7), Day 23 (process discovery as the next step).

---

### A7. Schluntz, Erik, and Barry Zhang. "Building effective agents." Anthropic Engineering, 19 December 2024. anthropic.com/engineering/building-effective-agents

**What it covers:** When to use fixed workflows and when to use autonomous agents, plus five workflow patterns: prompt chaining, routing, parallelization, orchestrator-workers, evaluator-optimizer.

**Whose intuition it favors:** Practitioner: start with the simplest structure that works.

**Where it's hardest:** It's easy to read; the hard part is accepting how much of the advice is about structure rather than models.

**Days that rely on it most:** Day 17 (primary source).

---

### A8. Ye, Anbang, Qianran Ma, Jia Chen, et al. "SOP-Agent: Empower General Purpose AI Agent with Domain-Specific SOPs." arXiv:2501.09316, 2025.

**What it covers:** Natural-language SOPs turned into a decision graph that the agent traverses, with only the current node's tools available at each step. Includes a grounded customer-service benchmark.

**Whose intuition it favors:** Agent research; the closest published match to Day 17's design.

**Where it's hardest:** The traversal and evaluation details.

**Days that rely on it most:** Day 17 (deep version).

---

### A9. Garg, Deepeka, Sihan Zeng, Sumitra Ganesh, and Leo Ardon. "Generating Structured Plan Representation of Procedures with LLMs." arXiv:2504.00029, 2025.

Referred to in this course as the SOPStruct paper, after the pipeline it describes.

**What it covers:** An LLM pipeline that segments an SOP, turns each segment into subtask nodes (name, inputs, outputs, dependencies, category), and checks the result: a PDDL planner for soundness, an LLM for completeness. Its node categories are Human Input, Information Processing, Information Extraction, Knowledge and Decision.

**Whose intuition it favors:** Enterprise operations; structured, verifiable plans over free-form agent behaviour.

**Where it's hardest:** The PDDL verification step, if you haven't met planning languages.

**Days that rely on it most:** Day 18 (primary source).

---

### A10. W3C. *PROV-O: The PROV Ontology.* W3C Recommendation, 30 April 2013. Eds. Timothy Lebo, Satya Sahoo, Deborah McGuinness. w3.org/TR/prov-o/ — with *PROV-DM: The PROV Data Model*, w3.org/TR/prov-dm/

**What it covers:** The standard vocabulary for provenance: Entity, Activity, Agent; `used`, `wasGeneratedBy`, `wasAssociatedWith`, `actedOnBehalfOf`, `wasDerivedFrom`; and qualified relations, including `hadPlan`.

**Whose intuition it favors:** Semantic web; carries straight over to property graphs.

**Where it's hardest:** The qualified terms (how to attach a role, plan or time to an association). `[VERIFY section numbers]`

**Days that rely on it most:** Day 19 (primary source), Day 20.

---

### A11. Garg, Ashu, and Jaya Gupta. "AI's trillion-dollar opportunity: Context graphs." Foundation Capital, 26 December 2025. (Also at ashugarg.substack.com.)

**What it covers:** The argument that systems of record capture what happened but not why, and that decision traces are the missing layer. Uses a renewal-discount exception as its worked example.

**Whose intuition it favors:** Venture investing. It's a thesis, not a standard, and it names startups the authors back.

**Where it's hardest:** Telling which parts are argument and which are evidence.

**Days that rely on it most:** Day 19 (hook and required reading), Days 20–21.

---

### A12. HL7 International. *FHIR R4: Provenance* resource. hl7.org/fhir/R4/provenance.html

**What it covers:** A deployed healthcare standard for provenance, built on W3C PROV: version-specific `target`, separate `occurred` and `recorded` times, `policy`, `reason`, `activity`, `agent` (`role`, `who`, `onBehalfOf`), `entity.role` and `signature`. Provenance covers changes; AuditEvent covers access.

**Whose intuition it favors:** Interoperability engineering.

**Where it's hardest:** Choosing between Provenance and AuditEvent, and between entity roles.

**Days that rely on it most:** Day 20 (primary source), Day 19.

---

### A13. Zhao, Andrew, et al. "ExpeL: LLM Agents Are Experiential Learners." AAAI 2024. arXiv:2308.10144.

**What it covers:** Agents that gather experience, extract insights by comparing successes with failures, and retrieve successful past trajectories for new tasks, without updating model weights.

**Whose intuition it favors:** Agent research.

**Where it's hardest:** It retrieves by similarity alone. Day 21 adds the applicability filter it lacks.

**Days that rely on it most:** Day 21 (primary source).

---

### A14. Wang, Zora Zhiruo, Jiayuan Mao, Daniel Fried, and Graham Neubig. "Agent Workflow Memory." arXiv:2409.07429, 2024.

**What it covers:** Inducing reusable workflows from past agent runs, offline from training examples or online from test queries, and supplying the relevant ones to later tasks.

**Whose intuition it favors:** Agent research (web navigation benchmarks).

**Where it's hardest:** Mapping its automatic induction onto an organisation where a person owns the procedure.

**Days that rely on it most:** Day 21 (Exercise 3).

---

### A15. Fang, Runnan, Yuan Liang, Xiaobin Wang, et al. "Memp: Exploring Agent Procedural Memory." Findings of ACL 2026. arXiv:2508.06433.

**What it covers:** Procedural memory distilled from trajectories into step-by-step and script-like forms, with strategies for building, retrieving, updating and deprecating it. Memory built by a stronger model also helps a weaker one.

**Whose intuition it favors:** Agent research.

**Where it's hardest:** The update and deprecation strategies, which are where Day 16's lessons apply.

**Days that rely on it most:** Day 21.

---

### A16. Blumenfeld, Zach (Neo4j). "Why your agents need decision traces, not just documents." AI Engineer Europe, 2026. youtube.com/watch?v=B9h9ovW5H9U

**What it covers:** A trace schema that separates entities, events and context, links events with `CAUSED` and `NEXT`, and defines the ontology before ingestion.

**Whose intuition it favors:** Graph-database practitioner (the speaker works for the vendor).

**Where it's hardest:** Separating the schema advice from the product material.

**Days that rely on it most:** Days 19–20.

---

### Arc 4 implementation references (optional)

- **Graphiti** — github.com/getzep/graphiti — the open-source temporal context graph from source A4, with an MCP server. Day 16.
- **XTDB documentation, key concepts** — docs.xtdb.com/concepts/key-concepts.html — four hidden temporal columns, closed-open intervals. Day 16.
- **MariaDB, bitemporal tables** — mariadb.com/kb/en/bitemporal-tables/ — SQL:2011 application time plus system versioning. Day 16.
- **openEHR, context and attribution** — openehr.atlassian.net (Resources wiki) — clinical context time vs. `time_committed`. Day 16.
- **LangGraph documentation** — docs.langchain.com/oss/python/langgraph/interrupts and …/persistence — `interrupt()`, checkpointers, `thread_id`. Days 17–18.
- **Rasa CALM Flows** — rasa.com/docs/reference/primitives/flows — deterministic flows with an LLM that only issues commands. Day 17.
- **HL7 CPG-on-FHIR implementation guide** — build.fhir.org/ig/HL7/cqf-recommendations/ — computable clinical guidelines (`PlanDefinition`, CQL). Day 17. (Continuous build; check the published version.)
- **Pei, Changhua, et al., "Flow-of-Action: SOP Enhanced LLM-Based Multi-Agent System for Root Cause Analysis"**, WWW 2025 (Industry Track), arXiv:2502.08224 — SOP-constrained root-cause analysis, 64.01% vs. 35.50% for ReAct. Day 17.
- **Xia, Chunqiu Steven, Yinlin Deng, Soren Dunn, and Lingming Zhang, "Agentless: Demystifying LLM-based Software Engineering Agents"**, arXiv:2407.01489, 2024 — a fixed localize → repair → validate pipeline for coding. Day 17.
- **Nandi, Subhrangshu, Arghya Datta, Rohith Nama, et al., "SOP-Bench: Complex Industrial SOPs for Evaluating LLM Agents"**, arXiv:2506.08119, 2025 — industrial SOP benchmark. v1: 1,800+ tasks, 10 domains, function-calling 27% / ReAct 48%; v2 (Feb 2026): 2,000+ tasks, 12 domains. Day 18.
- **OMG, BPMN 2.0.2** (formal/13-12-09, 2014; ISO/IEC 19510) — omg.org/spec/BPMN — gateways and user tasks. Days 17–18.
- **Neo4j Labs, `neo4j-agent-memory`** — neo4j.com/labs/agent-memory — short-term, long-term and reasoning memory in one graph. Days 19, 21. Check method names in the reference docs.
- **Nygard, "Documenting Architecture Decisions"**, Cognitect blog, 15 Nov 2011 — ADRs and the superseded status. Day 19.
- **CFPB Circular 2022-03** (26 May 2022) — specific reasons for adverse action, even for complex models. Days 19–20.
- **OpenLineage object model** — openlineage.io/docs/spec/object-model — Job, Run, Dataset, run facets. Day 19.
- **OpenTelemetry GenAI agent spans** — github.com/open-telemetry/semantic-conventions-genai — `invoke_agent`, `execute_tool`, opt-in content. Day 20. (Status: Development.)
- **CourtListener citations API (v4)** — wiki.free.law/c/courtlistener/help/api/rest/v4/citations — a public precedent graph. Day 21.
- **Tomicevic, Dominik (CEO, Memgraph), "Contexts graphs, AI memory, and enterprise knowledge: Are decision traces enough?"** *(headline as published)*, InfoWorld, 19 May 2026 — critique of traces as the whole of agent memory. Days 21, 23.
- **Cai et al., "A Survey on Temporal Knowledge Graph: Representation Learning and Applications"**, arXiv:2403.04782, 2024 — the embedding and forecasting side of "temporal KGs", which this course doesn't cover. Day 16 pointer.

---

## Optional Deeper Dives

- **Pan, Jeff Z., et al. (eds.) *Exploiting Linked Data and Knowledge Graphs in Large Organisations.* Springer, 2017.** Enterprise-scale KG deployment patterns. Useful after Day 14 if you're architecting a multi-team KG.

- **Hamilton, William L. *Graph Representation Learning.* Morgan & Claypool, 2020. arXiv:2104.13478.** Deep dive into GNNs on KGs; relevant if you continue past this course toward graph neural networks and reasoning.

- **spaCy documentation — Relation Extraction projects.** spacy.io/usage/projects — Practical reference for the NLP pipeline on Day 7.

- **LlamaIndex — Property Graph Index documentation.** docs.llamaindex.ai — Ready-made KG + LLM integration. Inspect the source code during Days 9–10 to understand what it does under the hood before trusting it.

- **Angles, Renzo, and Claudio Gutierrez. "Survey of Graph Database Models." *ACM Computing Surveys* 40, no. 1 (2008): 1–39.** The canonical taxonomy of graph data models, pre-property-graph era. Historical context for why the LPG model won.
