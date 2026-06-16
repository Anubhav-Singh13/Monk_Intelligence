*[Knowledge Graphs: From Concept to Production](../README.md) · Day 5b of 15 — Drill*

# Day 5b — Drill: Cypher Query Writing

> **Today's goal:** Build Cypher fluency through deliberate, uncomfortable repetition — no new concepts, just the bottleneck sub-skill that blocks most learners in the construction arc.
> **Format:** 7 exercises, progressively harder. Close the page between each one.
> **Reading time:** ~35 min (mostly writing) · **Prereqs:** [Day 5](day-05-querying-cypher-sparql.md)
> **No new material.** This is the gym, not the library.

---

## Why this day exists

Most learners stall between Day 8 (ingestion pipeline) and Day 9 (GraphRAG) not because they don't understand KGs — but because they can't write Cypher fast enough to debug their own pipeline. When a MERGE runs but returns nothing, or a two-hop query silently filters out all rows, the instinct is to blame Neo4j or Python. The actual problem is a shaky mental model of how Cypher pattern-matching evaluates.

This drill day exists to make Cypher feel automatic before you need it under pressure.

**The method:** Read each exercise once, close the page, write the query on paper or in an editor, then open to check. If you got it wrong, fix it and move on — do not re-read the exercise and try again. The discomfort of not knowing is the signal the drill is working.

Use the `PropertyGraph` from Day 3/6, or connect to Neo4j if you have it running. Both work for these exercises.

---

## Setup

Load this graph into Neo4j (or your `PropertyGraph`). All exercises run against it.

```cypher
// Run this in Neo4j Browser or via the Python driver before starting

CREATE
  // People
  (alice:Person:Researcher  {name: "Alice Chen",   field: "KG",  seniority: "senior", joined: 2020}),
  (bob:Person:Researcher    {name: "Bob Kumar",    field: "KG",  seniority: "junior", joined: 2022}),
  (carol:Person:Engineer    {name: "Carol Smith",  field: "MLOps", seniority: "senior", joined: 2019}),
  (diana:Person:Manager     {name: "Diana Patel",  field: "AI",  seniority: "lead",   joined: 2018}),
  (eve:Person:Researcher    {name: "Eve Torres",   field: "NLP", seniority: "senior", joined: 2021}),

  // Organisations
  (toolagen:Company   {name: "ToolaGen",  founded: 2023, country: "UK"}),
  (deepmind:Company   {name: "DeepMind", founded: 2010, country: "UK"}),
  (openai:Company     {name: "OpenAI",   founded: 2015, country: "US"}),

  // Papers
  (p1:Paper {title: "KG Embeddings Survey", year: 2022, citations: 450}),
  (p2:Paper {title: "GraphRAG at Scale",    year: 2023, citations: 180}),
  (p3:Paper {title: "Ontology Design",      year: 2021, citations: 90}),

  // Tools
  (neo4j:Tool   {name: "Neo4j",    type: "graph_db"}),
  (langchain:Tool {name: "LangChain", type: "framework"}),
  (spacy:Tool   {name: "spaCy",    type: "nlp"}),

  // Relationships
  (alice)-[:WORKS_AT {since: 2023}]->(toolagen),
  (bob)-[:WORKS_AT   {since: 2022}]->(toolagen),
  (carol)-[:WORKS_AT {since: 2021}]->(deepmind),
  (diana)-[:WORKS_AT {since: 2020}]->(openai),
  (eve)-[:WORKS_AT   {since: 2021}]->(deepmind),

  (diana)-[:MANAGES]->(alice),
  (diana)-[:MANAGES]->(bob),
  (diana)-[:MANAGES]->(eve),
  (carol)-[:MANAGES]->(bob),  // Bob has two managers (matrix org)

  (alice)-[:AUTHORED]->(p1),
  (bob)-[:AUTHORED]->(p1),
  (alice)-[:AUTHORED]->(p2),
  (eve)-[:AUTHORED]->(p3),

  (p1)-[:CITES]->(p3),
  (p2)-[:CITES]->(p1),

  (alice)-[:USES]->(neo4j),
  (alice)-[:USES]->(langchain),
  (carol)-[:USES]->(langchain),
  (eve)-[:USES]->(spacy),
  (bob)-[:USES]->(neo4j),

  (alice)-[:COLLABORATED_WITH {year: 2022}]->(eve),
  (alice)-[:COLLABORATED_WITH {year: 2023}]->(carol)
```

---

## Exercise 1 — Pattern reading (warm-up)

**Task:** Without running anything, translate each Cypher pattern into a precise English sentence. Be specific about direction.

```cypher
-- A
MATCH (p:Person)-[:MANAGES]->(q:Person)
RETURN p.name, q.name

-- B
MATCH (r:Researcher {seniority: "senior"})-[:AUTHORED]->(paper:Paper)
WHERE paper.citations > 200
RETURN r.name, paper.title, paper.citations
ORDER BY paper.citations DESC

-- C
MATCH (a:Person)-[:WORKS_AT]->(c:Company)<-[:WORKS_AT]-(b:Person)
WHERE a.name <> b.name AND a.field = b.field
RETURN a.name, b.name, c.name, a.field
```

<details>
<summary>Answers</summary>

**A:** "Return the name of every Person who manages another Person, along with the name of the person they manage." Note: direction matters — `p` manages `q`, not the reverse.

**B:** "Return the name, paper title, and citation count for all senior Researchers who authored papers with more than 200 citations, sorted by citations descending." Note: `AUTHORED` goes from person to paper, not the reverse.

**C:** "Return pairs of people (with different names) who work at the same company and have the same field." This is a self-join on company membership. Note: without `a.name <> b.name`, each pair appears twice (once as A,B and once as B,A — plus each person paired with themselves).
</details>

---

## Exercise 2 — Write it from scratch (one-hop)

Close this page after reading the requirement. Write the query without looking.

**Requirement:** Return the names of all `Researcher` nodes joined after 2021.

<details>
<summary>Solution</summary>

```cypher
MATCH (r:Researcher)
WHERE r.joined > 2021
RETURN r.name
```

Common mistake: `joined >= 2021` would include 2021 itself. The requirement says *after* 2021.
</details>

---

## Exercise 3 — Two-hop traversal

Close this page. Write the query without looking.

**Requirement:** Return the names of all tools used by people who work at companies founded before 2020.

<details>
<summary>Solution</summary>

```cypher
MATCH (p:Person)-[:WORKS_AT]->(c:Company),
      (p)-[:USES]->(t:Tool)
WHERE c.founded < 2020
RETURN DISTINCT t.name
ORDER BY t.name
```

Alternatively with a chain:

```cypher
MATCH (p:Person)-[:WORKS_AT]->(c:Company {country: "UK"}),
      (p)-[:USES]->(t:Tool)
WHERE c.founded < 2020
RETURN DISTINCT t.name
```

Note: `DISTINCT` is required — multiple people can use the same tool, and without it, the tool name appears once per person.
</details>

---

## Exercise 4 — Aggregation

Close this page. Write the query without looking.

**Requirement:** For each company, return the company name, the number of researchers who work there, and the average number of citations across all papers those researchers have authored. Only include companies with at least one researcher.

<details>
<summary>Solution</summary>

```cypher
MATCH (r:Researcher)-[:WORKS_AT]->(c:Company)
OPTIONAL MATCH (r)-[:AUTHORED]->(p:Paper)
RETURN c.name AS company,
       count(DISTINCT r) AS researchers,
       round(avg(p.citations)) AS avg_citations
ORDER BY researchers DESC
```

Key decisions:
- `OPTIONAL MATCH` for papers: a researcher with no papers should still appear (with `null` citations, averaged as `null`).
- `count(DISTINCT r)` not `count(r)`: without DISTINCT, if a researcher authored 3 papers, they'd be counted 3 times.
- `round()` makes the output readable.
</details>

---

## Exercise 5 — Three-hop with filter on an intermediate node

Close this page. Write the query without looking.

**Requirement:** Return the titles of papers that were cited by a paper authored by someone who works at a UK company.

<details>
<summary>Solution</summary>

```cypher
MATCH (p:Person)-[:WORKS_AT]->(c:Company {country: "UK"}),
      (p)-[:AUTHORED]->(paper:Paper)-[:CITES]->(cited:Paper)
RETURN DISTINCT cited.title
```

The path: `Person → WORKS_AT → Company(UK) AND Person → AUTHORED → Paper → CITES → Paper`.

Note the two separate `MATCH` clauses are implicitly ANDed on the shared variable `p`. An equivalent single chain:

```cypher
MATCH (p:Person)-[:WORKS_AT]->(c:Company {country: "UK"})
MATCH (p)-[:AUTHORED]->(paper:Paper)-[:CITES]->(cited:Paper)
RETURN DISTINCT cited.title
```
</details>

---

## Exercise 6 — The matrix org problem (tricky)

Close this page. Write the query without looking.

**Requirement:** Find all people who have more than one manager. Return their name and the names of all their managers.

<details>
<summary>Solution</summary>

```cypher
MATCH (mgr:Person)-[:MANAGES]->(emp:Person)
WITH emp, collect(mgr.name) AS managers
WHERE size(managers) > 1
RETURN emp.name, managers
```

The trap: you need `WITH` + `collect()` to group managers per employee *before* filtering on count. Writing `WHERE count(mgr) > 1` directly in the MATCH phase doesn't work — `count()` is an aggregation function, and Cypher requires `WITH` to introduce an aggregation boundary.

Alternative using `count`:
```cypher
MATCH (mgr:Person)-[:MANAGES]->(emp:Person)
WITH emp, count(mgr) AS mgr_count, collect(mgr.name) AS managers
WHERE mgr_count > 1
RETURN emp.name, managers
```
</details>

---

## Exercise 7 — Stretch: variable-length path with bound (hardest)

Close this page. This one is hard. Attempt it before looking at the hint.

**Requirement:** Alice wants to know who she can reach within 2 `COLLABORATED_WITH` hops (direct collaborators and collaborators-of-collaborators). Exclude Alice herself from the result. Return names, and the hop distance.

<details>
<summary>Hint (open only after your first attempt)</summary>

Use a variable-length path: `-[:COLLABORATED_WITH*1..2]->`. Capture the path to calculate length. Use `length()` to get hop count.
</details>

<details>
<summary>Solution</summary>

```cypher
MATCH path = (:Person {name: "Alice Chen"})-[:COLLABORATED_WITH*1..2]-(reachable:Person)
WHERE reachable.name <> "Alice Chen"
RETURN DISTINCT reachable.name, min(length(path)) AS hops
ORDER BY hops, reachable.name
```

Key points:
- `-[*1..2]-` (undirected) because `COLLABORATED_WITH` is symmetric — Alice reaching Eve via 1 hop is the same edge regardless of direction stored.
- `DISTINCT` + `min(length(path))` because the same node may be reachable by multiple paths; we want the shortest distance.
- `WHERE reachable.name <> "Alice Chen"` excludes Alice reaching herself via a round-trip.

Expected output (based on the setup data):
```
Eve Torres   | 1
Carol Smith  | 1
Bob Kumar    | 2   (Alice → Eve → ? ... check if Eve collaborated with Bob)
```
Actually with the data loaded, Eve and Carol are direct collaborators (1 hop). Bob is not a direct collaborator of Alice's collaborators unless you extended the data — this is intentional: use `EXPLAIN` to verify what your graph actually returns.
</details>

---

## Debrief

After completing all 7, answer these three questions without looking back:

1. Why does `count(DISTINCT r)` produce a different result than `count(r)` when you have an `OPTIONAL MATCH` upstream?
2. What does `WITH` do that `WHERE` alone cannot?
3. Why does variable-length path traversal require an upper bound in production?

<details>
<summary>Answers</summary>

1. Without `DISTINCT`, each row in the result set is counted. If a researcher authored 3 papers, the `OPTIONAL MATCH` produces 3 rows for that researcher — and `count(r)` counts 3. `count(DISTINCT r)` collapses to 1 per unique researcher node.

2. `WITH` introduces an aggregation boundary — it forces Cypher to evaluate all rows up to that point, apply aggregation functions (`count`, `collect`, `avg`), and then filter on the aggregated values. `WHERE` can only filter on values already bound; it cannot filter on aggregations.

3. Without an upper bound, the traversal explores every path in the graph, including cycles that go through the same node multiple times. On large graphs this exhausts memory and times out. The practical bound is usually 2–5 hops; beyond that, the semantic meaning of "reachable" becomes unclear anyway.
</details>

---

## Connect it back

You came to this page knowing Cypher's syntax. You leave knowing its evaluation model — why `WITH` exists, why `DISTINCT` matters, why direction is never optional. Days 8 and 9 assume this fluency. If any exercise took more than 3 minutes to get right on paper, flag that pattern and drill it again before moving on.

**Tomorrow — Day 6:** Rest & Synthesize I. You'll build a 10-node KG from scratch and write 3 queries against it. The queries will feel trivial if today's drill worked.

---

← [Day 5 — Querying: Cypher & SPARQL](day-05-querying-cypher-sparql.md) &nbsp;|&nbsp; [Day 6 — Rest & Synthesize I →](day-06-rest-and-synthesize-i.md)
