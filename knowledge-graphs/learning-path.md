# Learning Path

Full day-by-day rationale. Each row is linked to its page.

---

## Why this sequence

The path is structured in three arcs:

**Arc 1 — Mental models (Days 1–5):** Build the vocabulary before the tooling. Every concept in this arc is a decision you'll make in a real project. You can't choose between RDF and property graphs (Day 3) if you don't understand triples (Day 2). You can't write a useful query (Day 5) if you haven't defined a schema (Day 4).

**Arc 2 — Construction (Days 7–11):** Flip from reading to building. Each day produces a working artifact: an extractor, a pipeline, a GraphRAG query, an agent tool, an embedding model. Day 6 is a deliberate pause before this arc begins.

**Arc 3 — Production (Days 13–15):** The gap between a demo KG and a live one. Schema design patterns, data quality, incremental updates, and the capstone project.

**Arc 4 — Time, Procedure and Reason (Days 16–23):** Three layers on top of the Day 15 system, in build order. Time comes first (Day 16) because every later layer depends on it: a decision only makes sense against what was true when it was made. Procedure comes next (Days 17–18) because a decision trace has to name the procedure version and steps it followed. Context (Days 19–20) records the reasons, and precedent (Day 21) is what an agent does with all three. Day 22 consolidates the arc and Day 23 builds it into your Day 15 system.

---

## Rate-limiting sub-skill

**Diagnosis:** Writing multi-hop Cypher queries under real constraints — not conceptual understanding of KGs. Learners with an agentic AI background typically absorb triples, property graphs, and ontologies quickly. The bottleneck appears between Days 8 and 9: the ingestion pipeline is running, data is in Neo4j, but the learner can't write or debug Cypher fast enough to verify that the graph looks correct or to adapt the GraphRAG retrieval queries. The symptoms feel like "Neo4j is confusing" but the root cause is insufficient Cypher fluency.

**How the course addresses it:** Day 5b (Drill: Cypher) is a dedicated gym session placed immediately after Cypher is introduced and before the rest day. It has seven progressively harder exercises covering the patterns that block most learners: `WITH` + aggregation boundaries, `OPTIONAL MATCH` + `DISTINCT` interactions, variable-length paths with bounds, and multi-hop patterns with filters on intermediate nodes. If you find Days 9–10 slow, return to Day 5b before continuing — not to re-read it, but to attempt the exercises again cold.

### Arc 4 rate-limiting sub-skills

**Diagnosis:** Two skills block learners in Arc 4, and neither is about tools.

1. **Turning a written SOP into a procedure graph.** Real procedures hide steps, have gaps and overlaps in their thresholds, and mix rules with judgment. Learners who skip this skill build graphs that look right but quietly drop branches, or let the LLM choose routes the procedure already decides.
2. **Designing decision traces.** The default is to log the outcome only. A trace that leaves out the policy version, the rule inputs or a snapshot of the evidence can't be audited, replayed or safely reused as precedent, and those gaps only show up months later.

**How the course addresses them:** Day 18 (Drill: SOP → Procedure Graph) and Day 20 (Drill: Designing Decision Traces) each come the day after their concept is introduced, with seven graded exercises across different industries. The hardest retrieval exercises on Days 21–23 come back to both skills. If Day 21's exercises or the Day 23 audit test feel slow, redo the relevant drill cold.

---

## Day-by-day table

| Day | Page Title | Load-Bearing Idea | Why It Comes Now | Prerequisite Pages | Callbacks (revisits Day N) |
|-----|-----------|-------------------|------------------|--------------------|---------------------------|
| [1](days/day-01-what-is-a-knowledge-graph.md) | What Is a Knowledge Graph? | A KG is a directed, labeled multigraph where edges carry named semantics — not just connections, but *meaning* | Establishes the mental scaffold everything else hangs on; no tools yet | None | — |
| [2](days/day-02-triples-atomic-unit.md) | Triples: The Atomic Unit | Every fact is a `(subject, predicate, object)` triple; this universality is what makes KGs composable across sources | Grounds abstraction in one transferable unit before any tooling | Day 1 | — |
| [3](days/day-03-property-graphs-in-practice.md) | Property Graphs in Practice | A property graph attaches key-value metadata directly to nodes and edges — the model powering Neo4j and most live systems | Introduces the model you'll actually use before query languages | Days 1–2 | — |
| [4](days/day-04-ontologies-grammar-of-a-graph.md) | Ontologies: The Grammar | An ontology defines allowed types, relationships, and constraints; without it a graph is just a bag of labeled arrows | Needed before querying — a schema is what turns traversal into reasoning | Days 1–3 | — |
| [5](days/day-05-querying-cypher-sparql.md) | Querying Your Graph | Cypher and SPARQL are pattern-matching languages that traverse structure; they are fundamentally unlike SQL | First moment the reader can ask a KG a real question | Days 1–4 | — |
| [5b](days/day-05b-drill-cypher.md) | **Drill: Cypher** | 7 isolated Cypher exercises targeting the rate-limiting sub-skill — multi-hop traversal, aggregation, and the `WITH` boundary | Bottleneck drill before rest day; builds fluency that Days 8–9 assume | Day 5 | Day 5 (Cypher syntax → now pattern-matching intuition) |
| [6](days/day-06-rest-and-synthesize-i.md) | **Rest & Synthesize I** | No new material — re-derive the five core ideas; build a 10-node KG by hand and query it | Prevents accumulated half-understanding before the building arc | Days 1–5b | Days 1–5 (all five concepts retrieved from memory) |
| [7](days/day-07-entity-relation-extraction.md) | Entity & Relation Extraction | NLP pipelines (spaCy + LLM prompting) convert raw text into `(subject, predicate, object)` triples automatically | First practical building block: populating a KG from real documents | Days 1–5 | — |
| [8](days/day-08-ingestion-pipeline.md) | The Ingestion Pipeline | A KG lives inside a *loader → transformer → writer* pipeline; building it end-to-end is the core engineering skill | Connects extraction (Day 7) to a running graph store | Days 1–7 | — |
| [9](days/day-09-graphrag.md) | GraphRAG | GraphRAG retrieves multi-hop structured context that flat vector search cannot; this is why KGs matter for agents now | Directly bridges agentic AI background to KGs | Days 1–8 | **Day 4** (+5 days): ontology as schema for communities — Exercise 5 |
| [10](days/day-10-kgs-as-agent-memory.md) | KGs as Agent Memory | A KG is the ideal persistent, queryable, incrementally updatable memory substrate for a multi-step AI agent | Closes the loop to the primary domain; the "aha" page | Days 1–9 | **Day 5** (+5 days): Cypher as the agent's query interface — Exercise 6 |
| [11](days/day-11-kg-embeddings.md) | Knowledge Graph Embeddings | Mapping entities and relations to vectors (TransE, RotatE) enables ML reasoning and link prediction over incomplete graphs | Bridges KG structure to ML background; needed for Day 14 patterns | Days 1–5 + ML background | **Day 2** (+9 days): triples as the training unit; negative sampling — Exercise 6 |
| [12](days/day-12-rest-and-synthesize-ii.md) | **Rest & Synthesize II** | Re-derive the full pipeline: raw docs → triples → graph store → Cypher query → agent memory; nothing new | Cements the end-to-end arc before production concerns | Days 1–11 | Days 7–11 (full construction arc retrieved from memory) |
| [13](days/day-13-schema-design-patterns.md) | Schema Design Patterns | Entity-centric, event-centric, and provenance patterns determine how queryable and maintainable a KG is at scale | Cannot build a production KG without choosing the right pattern upfront | Days 1–8 | — |
| [14](days/day-14-production-concerns.md) | Production Concerns | Incremental updates, data quality, schema evolution, and serving under load are the gap between a demo and a live system | Practical capstone preparation — the hard problems of keeping a KG alive | Days 1–13 | **Day 1** (+13 days): Open World Assumption → staleness logic — Exercise 6 |
| [15](days/day-15-capstone.md) | **Capstone** | Design, build, and serve a domain-specific KG that an LLM agent queries as structured memory — end-to-end, live data | Uses everything; produces a portfolio artifact | All days | Days 8–10 (pipeline + GraphRAG + agent memory integrated in the build) |
| | **Arc 4 — Time, Procedure and Reason** | | | | |
| [16](days/day-16-temporal-graphs.md) | Temporal Graphs: Two Clocks on Every Fact | Every fact carries a valid window and a record window; changes retire versions instead of deleting edges, so the graph can answer "true on D1, as known on D2" | Time sits under every later layer: a decision only makes sense against what was true when it was made | Days 3, 5, 13, 14 | **Day 3**: edge properties hold the windows · **Day 14**: deletion replaced by retirement — Exercise 4 · **Day 1**: Open World Assumption and open ends |
| [17](days/day-17-procedural-graphs.md) | Procedural Graphs: Put the Route in Code | A procedure is a state machine of typed steps (rule, tool, model, LLM, human); code decides the route, the LLM only writes state inside a step | A trace needs an executable procedure to refer to | Days 10, 16 | **Day 10**: revises "procedural memory isn't a KG job" · **Day 5b**: Cypher for run analysis — Exercise 3 · **Day 16** (+1): procedure versions — Exercise 4 |
| [18](days/day-18-drill-sop-to-procedure-graph.md) | **Drill: SOP → Procedure Graph** | 7 graded exercises across banking, lending, pharmacy, finance, insurance and IT: segment, type, complete guards, human gates, versioning, conformance | Bottleneck #1, drilled the day after it's introduced | Days 16, 17 | **Day 17** (+1): step types cold · **Day 16** (+2): versioning — Exercise 6 |
| [19](days/day-19-context-graphs.md) | Context Graphs: Record the Why | A decision trace links a decision to evidence as it stood, the procedure and policy version, rules evaluated, steps run and approvers; supersede, never edit | Ties the two layers below to the decisions organisations actually argue about | Days 13, 16, 17 | **Day 13** (+6): provenance → traces — Exercise 4 · **Day 16** (+3): occurred vs recorded — Exercise 4 · **Day 18**: run state — opening recall |
| [20](days/day-20-drill-decision-traces.md) | **Drill: Designing Decision Traces** | 7 graded exercises across support, insurance, underwriting, sales, observability, healthcare and trust & safety | Bottleneck #2, drilled the day after it's introduced | Days 16, 17, 19 | **Day 19** (+1): checklist cold · **Day 17** (+3): run linkage — Exercise 1 · **Day 16** (+4): `known_at` evidence — Exercise 2 |
| [21](days/day-21-precedent-retrieval.md) | Precedent: Retrieving How We Decided Before | A precedent is similar *and* still applicable; candidates from graph + embeddings, filtered by standing and compatible versions; repeated patterns become proposals | Puts the three layers to work as memory an agent can act on | Days 9, 11, 16–20 | **Day 16** (+5): as-of precedent — Exercise 4 · **Day 17** (+4): proposals → procedure versions — Exercise 3 · **Day 11** (+10): TransE vs card embeddings — Exercise 4 · **Day 9**: neighbourhood candidates |
| [22](days/day-22-rest-and-synthesize-iii.md) | **Rest & Synthesize III** | No new material: rebuild the stack from memory (time under everything → procedure → trace → precedent) | Consolidates the arc before the capstone | Days 16–21 | Days 16–21, scrambled, with a cross-layer question |
| [23](days/day-23-capstone-ii.md) | **Capstone II** | Extend the Day 15 system with bitemporal edges, a real SOP compiled into a versioned procedure graph, decision traces and precedent retrieval — and pass the audit test | Uses everything in the system you already built | All days | **Days 1, 14, 15** (backfill and migration) · **Days 16–21** integrated |

**A note on Arc 4 callbacks:** the arc is 8 days long, so Day 16's ideas can't get a +14 callback inside it. Instead, Days 21–23 each include an "as of" exercise, and both drills mix in temporal edge cases.

---

## The learning arc narrative

On Day 1, you can articulate what a knowledge graph *is* and why it differs from a vector store or a relational table — but you cannot build one. By Day 6 you can query a small graph you've constructed by hand and begin to feel the difference between pattern-matching over structure and keyword search over text. The first half of the course is about earning the right vocabulary: triples, ontologies, property graphs, Cypher. These aren't academic formalities — each one is a tool decision you will make in a real project.

The second half flips from concept to construction. You build an ingestion pipeline that reads your own documents, extracts entities and relationships with an LLM, and loads them into Neo4j. You then wire that graph into a RAG pipeline and an agent memory system — exactly the two patterns that make KGs immediately valuable in production AI work. The course ends with you producing a working, domain-specific knowledge graph that a real agent can query. The single story tying the course together: *a knowledge graph is the difference between an agent that retrieves text and one that reasons over facts.*

On Day 16 you have a working knowledge graph that an agent queries, but it only knows the present. It can't say what it believed last month, or why it acted on that belief. Days 16–18 add the two things a real organisation runs on: **time** (facts that open and close rather than being overwritten) and **procedure** (playbooks compiled into graphs, where code decides the route and the LLM only exercises judgment inside a step). The SOP drill turns this into a skill: given a messy written runbook or policy, you can produce a typed, versioned procedure graph you can check against past runs.

Days 19–23 add **reason**. Each decision becomes a trace tied to the evidence as it stood, the policy version, the steps it followed and who approved it, so it can be audited and retrieved as precedent. By Day 23 your agent can answer the question that holds the arc together: *"What did we believe about X on date D, which procedure did we follow, why did we decide what we did, and would we decide the same today?"* **A knowledge graph tells an agent what is true; time, procedure and context tell it what was true, what to do, and why it was done.**
