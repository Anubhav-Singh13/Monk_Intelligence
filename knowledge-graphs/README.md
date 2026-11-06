# Knowledge Graphs: From Concept to Production

> **The promise:** By the end of this course, you can design, build, and serve a domain-specific knowledge graph that a real LLM agent queries as structured memory — and, after Arc 4, an agent that can explain any past decision from what it knew at the time, the procedure it followed and the reasons it recorded.

## Who this course is for

This course targets practitioners and builders (L1/L2) with a background in agentic AI and machine learning. You write Python daily, you've worked with LLMs and RAG pipelines, and you want to add knowledge graphs to your production toolkit — not just understand them abstractly, but build them from scratch and wire them into real agents.

You should *not* take this course if you want a deep theoretical treatment of description logics, a formal study of RDF semantics, or a research-level survey of KG embedding methods. This course optimises for one outcome: a working knowledge graph pipeline that an AI agent can depend on.

**Arc 4 (Days 16–23)** extends that outcome for anyone whose agents make decisions that must be explained later — in claims, lending, IT operations, healthcare administration, customer support or trust & safety. It adds three graph types on top of your Day 15 system: **temporal** graphs (two clocks on every fact), **procedural** graphs (business procedures as state machines, with the route in code), and **context** graphs (decision traces), plus precedent retrieval that uses all three. Same level (L1/L2), same stack (Python, Neo4j, LangGraph).

## The arc at a glance

On Day 1, you can articulate what a knowledge graph *is* and why it differs from a vector store or a relational table — but you cannot build one. By Day 6 you can query a small graph you've constructed by hand and begin to feel the difference between pattern-matching over structure and keyword search over text. The first half of the course is about earning the right vocabulary: triples, ontologies, property graphs, Cypher. These aren't academic formalities — each one is a tool decision you will make in a real project.

The second half flips from concept to construction. You build an ingestion pipeline that reads your own documents, extracts entities and relationships with an LLM, and loads them into Neo4j. You then wire that graph into a RAG pipeline and an agent memory system — exactly the two patterns that make KGs immediately valuable in production AI work. The course ends with you producing a working, domain-specific knowledge graph that a real agent can query. The single story tying the course together: *a knowledge graph is the difference between an agent that retrieves text and one that reasons over facts.*

Arc 4 starts where Day 15 ends: your graph knows the present, but not what it believed last month or why it acted. Days 16–18 add **time** (facts that open and close instead of being overwritten) and **procedure** (playbooks compiled into graphs, where code decides the route and the LLM only exercises judgment inside a step). Days 19–21 add **reason**: every decision becomes a trace tied to the evidence as it stood, the procedure version, the rules that fired and who signed off, and past decisions become precedent only when they are both similar *and* still applicable. By Day 23 your agent can answer: *"What did we believe about X on date D, which procedure did we follow, why did we decide what we did, and would we decide the same today?"* The arc's story: **a knowledge graph tells an agent what is true; time, procedure and context tell it what was true, what to do, and why it was done.**

## How to use this course

- **Rhythm:** one page per day, 30–45 minutes of focused reading. Don't skip exercises — for L2, they are where the learning actually happens.
- **Exercises:** Every page has 3–4 exercises. If you're targeting L1, do at least the first two. If L2, do all of them including the stretch. The capstone on Day 15 requires the stretch exercises from Days 8–11.
- **Bibliography:** When a page says "required if you have 15 extra minutes," do it. When it says "deep version," bookmark it for after the course. Don't derail your momentum by rabbit-holing into sources mid-course.
- **Drill days (5b, 18 and 20):** no reading, only graded exercises on a bottleneck skill. Attempt each before opening its hint; redo any you got wrong cold the next day.
- **Rest & Synthesize days (6, 12 and 22):** These are not days off. Re-derive the ideas without looking at the pages. Build the mini-project. If you can't, that's information — go back one day before proceeding.
- **If life intervenes:** Missing a day is fine. Don't skip rest days to catch up. If you miss more than 3 days in a row, re-read the most recent rest-day page before resuming.

## The learning path

| Day | Title | One idea | Core source |
|-----|-------|----------|-------------|
| [1](days/day-01-what-is-a-knowledge-graph.md) | What Is a Knowledge Graph? | A KG is a directed, labeled multigraph where edges carry named semantics | Hogan et al., Ch. 1 |
| [2](days/day-02-triples-atomic-unit.md) | Triples: The Atomic Unit | Every fact is a `(subject, predicate, object)` triple | Heath & Bizer, Ch. 2 |
| [3](days/day-03-property-graphs-in-practice.md) | Property Graphs in Practice | A property graph attaches key-value metadata to nodes and edges | Robinson et al., Ch. 2 |
| [4](days/day-04-ontologies-grammar-of-a-graph.md) | Ontologies: The Grammar | An ontology defines allowed types and constraints | Hogan et al., Ch. 2 |
| [5](days/day-05-querying-cypher-sparql.md) | Querying Your Graph | Cypher and SPARQL are declarative pattern-matching languages | Robinson et al., Ch. 3 |
| [5b](days/day-05b-drill-cypher.md) | **Drill: Cypher** | 7 exercises on multi-hop traversal, aggregation, and path-finding — no new concepts | — |
| [6](days/day-06-rest-and-synthesize-i.md) | Rest & Synthesize I | Build a 10-node KG by hand and query it — no new material | — |
| [7](days/day-07-entity-relation-extraction.md) | Entity & Relation Extraction | NLP pipelines convert unstructured text into triples | Hogan et al., Ch. 5 |
| [8](days/day-08-ingestion-pipeline.md) | The Ingestion Pipeline | A KG lives inside a loader → transformer → writer pipeline | — |
| [9](days/day-09-graphrag.md) | GraphRAG | KG-backed retrieval answers multi-hop questions flat vector search cannot | Edge et al., 2024 |
| [10](days/day-10-kgs-as-agent-memory.md) | KGs as Agent Memory | A KG is the ideal persistent, queryable, updatable agent memory substrate | — |
| [11](days/day-11-kg-embeddings.md) | Knowledge Graph Embeddings | Mapping entities and relations to vectors enables ML reasoning | Bordes et al., 2013 |
| [12](days/day-12-rest-and-synthesize-ii.md) | Rest & Synthesize II | Re-derive the end-to-end pipeline: docs → triples → graph → agent | — |
| [13](days/day-13-schema-design-patterns.md) | Schema Design Patterns | Entity-centric vs. event-centric patterns determine KG maintainability | Robinson et al., Ch. 4 |
| [14](days/day-14-production-concerns.md) | Production Concerns | Incremental updates, data quality, and schema evolution | Hogan et al., Ch. 8 |
| [15](days/day-15-capstone.md) | Capstone | Design, build, and serve a domain KG for an AI agent — end-to-end | All days |
| | **Arc 4 — Time, Procedure and Reason** | | |
| [16](days/day-16-temporal-graphs.md) | Temporal Graphs: Two Clocks on Every Fact | Every fact has a valid window and a record window; changes retire versions, never delete them | Rasmussen et al. (Zep), 2025 |
| [17](days/day-17-procedural-graphs.md) | Procedural Graphs: Put the Route in Code | A procedure is a state machine of typed steps; code picks the route, the LLM works inside a step | Schluntz & Zhang (Anthropic), 2024 |
| [18](days/day-18-drill-sop-to-procedure-graph.md) | **Drill: SOP → Procedure Graph** | 7 exercises: typing steps, complete guards, human gates, versioning, conformance — no new concepts | SOPStruct, 2025 |
| [19](days/day-19-context-graphs.md) | Context Graphs: Record the Why | A decision trace links evidence as it stood, procedure and policy version, rules fired and approvers; supersede, never edit | W3C PROV-O, 2013 |
| [20](days/day-20-drill-decision-traces.md) | **Drill: Designing Decision Traces** | 7 exercises: outcome-only traces, evidence choices, replay, exceptions, spans, FHIR, a new domain | HL7 FHIR Provenance |
| [21](days/day-21-precedent-retrieval.md) | Precedent: Retrieving How We Decided Before | A precedent is similar *and* still applicable; repeated patterns become proposals, not automatic changes | Zhao et al. (ExpeL), 2024 |
| [22](days/day-22-rest-and-synthesize-iii.md) | Rest & Synthesize III | Rebuild the arc's stack from memory — no new material | — |
| [23](days/day-23-capstone-ii.md) | Capstone II | Extend your Day 15 system with time, procedure, traces and precedent — and pass the audit test | All Arc 4 sources |

## The Course Shelf

1. **Hogan et al., *Knowledge Graphs* (MIT Press, 2021)** — The definitive textbook; spine of Days 1–5, 11–14.
2. **Robinson, Webber & Eifrem, *Graph Databases* (O'Reilly, 2015)** — Practical Neo4j guide; spine of Days 3, 5, 8, 13–14.
3. **Heath & Bizer, *Linked Data* (Morgan & Claypool, 2011)** — Foundation for the RDF/triple mental model; Days 2, 4–5.
4. **Edge et al., "From Local to Global: A Graph RAG Approach" (arXiv:2404.16130, 2024)** — Primary source for Day 9.
5. **Bordes et al., "Translating Embeddings…" (NeurIPS, 2013)** — TransE; primary source for Day 11.

**Arc 4 additions:**

6. **Fowler, "Bitemporal History" (martinfowler.com, 2021)** — the best short introduction to two clocks; required reading for Day 16.
7. **Rasmussen et al., "Zep: A Temporal Knowledge Graph Architecture for Agent Memory" (arXiv:2501.13956, 2025)** — temporal edges and invalidation in agent memory; Days 16, 21, 23.
8. **Schluntz & Zhang, "Building effective agents" (Anthropic, 2024)** — when to use fixed workflows instead of agents; Day 17.
9. **W3C, *PROV-O* (W3C Recommendation, 2013)** — the standard vocabulary for decision traces; Days 19–20.
10. **Zhao et al., "ExpeL" (AAAI 2024, arXiv:2308.10144)** — agents learning from past experience; Day 21.

Full annotated shelf in [bibliography.md](bibliography.md).

## The capstone

On Day 15 you will choose a domain from your own work (a codebase, a research area, a product domain), extract 200+ entities and relationships from real documents using an LLM pipeline, load them into Neo4j, write 5 Cypher queries that answer questions a human expert would ask, and expose the graph as a tool available to a LangChain or LlamaIndex agent. "Done" means: the agent correctly answers a multi-hop question that would fail with plain vector RAG.

**Capstone II (Day 23)** extends the same system. You make its edges bitemporal, compile one real written procedure from your domain into a LangGraph procedure graph stored in Neo4j with versions, write a decision trace from every run, and give the agent a precedent tool that excludes overturned and out-of-date decisions. "Done" means the agent passes the **audit test**: for a decision later affected by a corrected fact, a procedure change and an overturned precedent, it explains — from the graph alone — what it believed at the time, which procedure it followed, why it decided as it did, and whether it would decide the same today.

## Glossary

[glossary.md](glossary.md)

## Meta

- **Depth level:** L1 (Practitioner) + L2 (Builder)
- **Estimated total hours:** Days 1–15: ~9 hours reading + ~6 hours exercises = 15 hours (16 days including the drill day). Arc 4 (Days 16–23): ~5 hours reading and drills + ~4 hours exercises and capstone build = 9 hours. Whole course: ~24 hours over 24 days.
- **Rate-limiting sub-skill:** Writing multi-hop Cypher queries under real constraints — not conceptual KG understanding. Most learners with an agentic AI background grasp triples and ontologies quickly, then stall between Days 8–9 because they can't write or debug Cypher fast enough to validate their pipeline output. Day 5b (Drill: Cypher) targets this directly. If Day 9's exercises feel slow or unclear, return to Day 5b before continuing.
- **Arc 4 rate-limiting sub-skills:** (1) turning a written SOP into a complete, typed, versioned procedure graph — drilled on Day 18; (2) designing decision traces that can be audited, replayed and reused — drilled on Day 20. If Day 21 or the Day 23 audit test feels slow, redo the relevant drill cold.
- **Last updated:** 2026-11-06 (Arc 4 added)
- **Feedback:** Open an issue in the course repository or flag corrections inline.
