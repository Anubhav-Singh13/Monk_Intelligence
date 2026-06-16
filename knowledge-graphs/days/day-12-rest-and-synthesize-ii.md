*[Knowledge Graphs: From Concept to Production](../README.md) · Day 12 of 15*

# Day 12 — Rest & Synthesize II

> **Today:** No new concepts. Only consolidation of the construction arc.
> **Format:** Re-state → Re-derive → Full pipeline run → Quiz
> **Reading time:** ~35 min (longer if gaps surface — that's the point)
> **Prereqs:** [Days 7–11](../README.md)

---

Days 7–11 built five things. Today you verify they cohere into one system before the production arc (Days 13–14) and the capstone (Day 15).

Close all previous pages. Work from memory first.

---

## Step 1 — Re-state the five construction arc ideas (5 min)

> **Why this table is not in day order:** Recalling Day 7 first, then Day 8, then Day 9 lets you use sequence as a cue — which is how you memorised it, not how you'll use it. The scrambled order below forces you to retrieve each concept on its own merits. This discomfort is the point.

Write one sentence for each without looking anything up. Do them in the order given — resist the urge to reorder:

| Concept | Your sentence |
|---------|--------------|
| KG Embeddings / TransE (Day 11) | |
| GraphRAG (Day 9) | |
| Ingestion Pipeline (Day 8) | |
| KG as Agent Memory (Day 10) | |
| Information Extraction (Day 7) | |

<details>
<summary>Compare to these</summary>

1. **TransE / KG Embeddings:** Represent entities and relations as vectors in ℝᵈ where `vec(head) + vec(relation) ≈ vec(tail)` for true triples; training pushes true triples to score high and corrupted triples to score low; the resulting vectors enable link prediction (finding missing edges) and entity similarity.

2. **GraphRAG:** A retrieval approach that builds a KG from a corpus, runs community detection to create hierarchical clusters, summarises each community with an LLM, and retrieves either community summaries (global questions) or entity subgraphs (local questions) as context for generation.

3. **Ingestion Pipeline:** A three-stage architecture — Loader (read raw documents), Transformer (extract + normalise triples), Writer (load into Neo4j using MERGE for idempotency) — is the engineering wrapper that turns extractions into a live, queryable graph.

4. **KG as Agent Memory:** An agent reads relevant facts from the KG at the start of each turn, uses them in reasoning, then extracts and writes new facts back to the KG after each turn — creating a persistent, cross-session, structured memory that grows with every interaction.

5. **Information Extraction:** NLP pipelines (NER + Relation Extraction, or LLM prompting with structured output) convert unstructured text into `(subject, predicate, object)` triples suitable for loading into a KG.
</details>

---

## Step 2 — Re-derive the end-to-end flow (10 min)

Draw this pipeline on paper from memory. Every arrow should be labelled with what data crosses it.

```
Raw documents
    │
    │ (what format? what API?)
    ▼
[    LOADER    ]
    │
    │ (what does it output?)
    ▼
[  TRANSFORMER  ]
    │
    │ (what does it output?)
    ▼
[    WRITER    ]
    │
    │ (what database? what command?)
    ▼
Neo4j Graph
    │           │               │
    │           │               │
    ▼           ▼               ▼
[GraphRAG]  [Agent       [Embeddings]
[retrieval]  Memory]     [link pred.]
    │           │               │
    │           │               │
    ▼           ▼               ▼
LLM answer  Per-turn     Missing edge
            context      predictions
```

<details>
<summary>Filled-in diagram</summary>

```
Raw documents (PDF, .txt, markdown, DB records)
    │
    │ {"id": "doc1", "text": "..."}
    ▼
[LOADER — TextLoader.load()]
    │
    │ [{"id": "doc1", "text": "..."}]
    ▼
[TRANSFORMER — LLMExtractor + Transformer.transform()]
    │  • LLM prompt → raw JSON triples
    │  • entity canonicalisation (fuzzy matching)
    │  • validation (skip null/short fields)
    │ [Triple(subject, predicate, object, subject_type, ...)]
    ▼
[WRITER — Neo4jWriter.write() using MERGE]
    │
    │ MERGE (s:SubjectType {name: $subject})
    │ MERGE (o:ObjectType  {name: $object})
    │ MERGE (s)-[:PREDICATE]->(o)
    ▼
Neo4j Graph (live, queryable, idempotent)
    │              │                │
    │              │                │
    ▼              ▼                ▼
[GraphRAG]    [KGMemory]     [PyKEEN TransE]
community     read: MATCH    score_t(h, r)
detection     write: MERGE   → rank entities
+ LLM sum     + episode log  → link prediction
    │              │                │
    ▼              ▼                ▼
LLM answers   Agent gets     Missing edges
global Qs     memory ctx     ranked by prob
```
</details>

**Questions to answer without looking:**

1. Why does the Writer use MERGE instead of CREATE?
2. What does the Transformer do with "Alice Chen" and "A. Chen" if they appear in different documents?
3. What is the difference between a local GraphRAG query and a global one?
4. When the MemoryAgent runs `_build_memory_context("What does Alice work on?")`, what Cypher query does it (approximately) run?
5. What does `vec(Alice) + vec(co_authored)` produce, and why is it useful?

<details>
<summary>Answers</summary>

1. MERGE is idempotent: running the pipeline twice on the same documents produces exactly the same graph. CREATE would add duplicate nodes on each run.

2. Fuzzy matching via `SequenceMatcher` — the similarity ratio between "alice chen" and "a. chen" may or may not exceed the 0.88 threshold. If it does, they're merged to one canonical name. If not, two separate entities are created. The threshold is a tuneable parameter.

3. Local: extract entities from the query → find their subgraph in Neo4j → feed subgraph to LLM. Global: score community summaries against the query → feed top summaries to LLM via map-reduce. Local is for specific entity questions; global is for corpus-wide synthesis questions.

4. Approximately: `MATCH (n {name: "Alice"})-[r*1..2]-(m) RETURN n.name, type(last(r)), m.name LIMIT 50`. Then the results are formatted into a "memory context" block injected into the LLM prompt.

5. `vec(Alice) + vec(co_authored)` is a point in ℝᵈ near where co-authored entities of Alice should cluster. Finding the entity whose embedding is nearest to this point gives the top-predicted co-author — a missing edge that may be true but wasn't in the source documents.
</details>

---

## Step 3 — Run the full pipeline (10 min)

If you have Neo4j running: run the complete pipeline end-to-end on your own domain documents. If you don't: do this on paper.

**Checklist:**
- [ ] Load 3+ documents through `KGIngestionPipeline`
- [ ] Verify node and edge counts in Neo4j Browser (`MATCH (n) RETURN count(n)`)
- [ ] Run one local GraphRAG query and one global query
- [ ] Start a `MemoryAgent` session, teach it one fact, close the session, start a new session and verify the fact persists
- [ ] (Optional) Train TransE on the loaded graph and predict one missing edge

If any step fails, that's your diagnostic. Fix it before Day 13.

---

## Step 4 — Quiz (5 min)

> **Why the questions jump around:** Five questions in day-order lets you answer each one by position ("this is the Day 8 question, so it's about MERGE"). The questions below deliberately cross concept boundaries. Answer each cold.

Answer before looking:

1. An agent learns `(Alice, leads, Project X)` on turn 3. Three sessions later, you ask "Who leads Project X?" Does it answer correctly? What single infrastructure fact must be true for this to work?

2. You train TransE on your KG and find that the `co_authored_with` relation vector collapses toward zero after training. What does this reveal about the relation — and which model would handle it correctly?

3. Your NLP extractor produces `{"subject": "Alice", "predicate": "co_authored", "object": "Bob"}`. Name two things wrong with this triple for a production KG, and state what the Writer would do with each problem.

4. Your GraphRAG global search returns a community summary: "This community covers transformer architectures including BERT, GPT, and T5." A user asks: "What is the exact training loss of BERT at convergence?" Should you answer from this summary? In one sentence, explain the architectural reason why not.

5. **Cross-concept:** Your agent uses KG memory (Day 10) and GraphRAG retrieval (Day 9) on the same Neo4j graph. A user asks a global question: "What are the main research themes in our corpus?" Which system should handle it — memory retrieval or GraphRAG global search — and why does the other one fail here?

<details>
<summary>Answers</summary>

1. Yes — *if and only if* the `KGMemory` instance connects to the same persistent Neo4j database across sessions. The fact `(Alice)-[:LEADS]->(Project X)` was written with MERGE on turn 3 and lives in Neo4j, not in memory. On session 4, `_build_memory_context` retrieves it via a Cypher MATCH. If Neo4j is ephemeral (in-memory, Docker with no volume mount), the fact is lost.

2. `co_authored_with` is a *symmetric* relation: `(Alice, co_authored_with, Bob)` and `(Bob, co_authored_with, Alice)` are both training triples. TransE learns a translation `vec(co_authored_with)` such that `vec(Alice) + vec(r) ≈ vec(Bob)` AND `vec(Bob) + vec(r) ≈ vec(Alice)`. The only vector satisfying both is `r ≈ 0`. This is a known TransE limitation. Use **RotatE**, which represents relations as complex-plane rotations and handles symmetric relations naturally.

3. Two problems: (a) `"Bob"` is ambiguous — a bare first name won't deduplicate with "Bob Kumar" in other documents; the entity resolution step will create a separate node. (b) The triple lacks `subject_type` and `object_type`; the Writer will label both nodes `:Entity` instead of `:Person`, breaking queries that filter on `(p:Person)`. Both are caught by a `TripleValidator` if it checks for minimum name length and required type fields.

4. No. Community summaries are LLM-generated high-level syntheses of a cluster of entities — exact numerical values (training loss, parameter counts, dates) are not preserved in the summarisation step. The answer should be: "I don't have that level of detail in the community summary — try a direct source query." GraphRAG's global search answers *thematic* questions; it is not a precision fact lookup.

5. **GraphRAG global search.** The question asks for a corpus-wide synthesis ("what are the main themes") — exactly the class of global question that no single retrieved entity or edge answers. KG memory retrieval works by fetching the subgraph around matched entities; with no specific entity to anchor on, it would return everything or nothing. GraphRAG's community summaries are pre-built for exactly this: they aggregate the themes across the entire corpus so the LLM can answer without reading every document.
</details>

---

## What's ahead

The final three days complete the course:

| Day | Topic | What you'll decide |
|-----|-------|-------------------|
| 13 | Schema Design Patterns | How to structure your KG so it stays queryable at scale |
| 14 | Production Concerns | How to keep a live KG correct, fast, and maintainable |
| 15 | Capstone | Build a domain KG for a real agent — end-to-end |

Day 15 will ask you to produce a working system. Start thinking now about your domain:

- What corpus will you use? (documents you already have)
- What agent will query the KG? (LangChain, LlamaIndex, or your own)
- What's the multi-hop question that flat RAG cannot answer?

---

## Connect it back

Days 7–11 gave you five building blocks. Today you verified they connect. The construction arc answer to "how do I build a KG?" is: extract → normalise → merge → retrieve → remember → embed. Day 13 now asks a design question: before you extract anything, how should you structure what you're building?

**The question you carry into Day 13:** *If you're building a KG of customer support interactions, should a "ticket" be a node, an edge, or something else — and what determines that choice?*

---

← [Day 11 — KG Embeddings](day-11-kg-embeddings.md) &nbsp;|&nbsp; [Day 13 — Schema Design Patterns →](day-13-schema-design-patterns.md)
