*[Knowledge Graphs: From Concept to Production](../README.md) · Day 23 of 23 · Arc 4 — Time, Procedure and Reason*

# Day 23 — Capstone II: Give Your Agent a Past, a Playbook and Its Reasons

> **Today's one idea:** Extend your Day 15 system so that its agent can answer, from the graph alone: *"What did we believe about X on date D, which procedure did we follow, why did we decide what we did — and would we decide the same today?"*
> **Reading time:** ~120–150 min (a build day, not a reading day)
> **Prereqs:** [Day 15](day-15-capstone.md) (your working system), [Days 16–22](day-16-temporal-graphs.md)
> **No single primary source.** This capstone draws on the whole Arc 4 shelf in [bibliography.md](../bibliography.md) — keep it open.
> **Before you start:** Without looking at any page, list the four layers you're adding today and the one rule from each that most often goes wrong. Retire-never-rewrite? Route-in-code? Supersede-never-edit? Similar-*and*-applicable? If any is blank, do that day's Exercise 1 cold before starting.

---

## What "done" looks like

You're done when all six hold:

1. **Two clocks.** Every time-sensitive edge in your Day 15 graph has a valid window and a record window. The overlap check from [Day 16, Exercise 3](day-16-temporal-graphs.md) returns no unexpected rows.
2. **One real procedure.** At least one *real* written procedure from your domain — a runbook, policy, playbook or SOP — compiled into a LangGraph procedure graph with at least one rule junction and one human gate, stored in Neo4j with a version and a valid window. Its ambiguities are written down as questions for its owner (Day 18).
3. **A trace for every run.** Every run writes a decision trace that scores **10/10** on [Day 20's](day-20-drill-decision-traces.md) checklist, with an explicit snapshot / pointer / hash choice for each kind of evidence.
4. **Precedent that can be cited.** A `find_precedents` tool that your agent calls before deciding, which provably excludes superseded decisions and decisions made under incompatible procedure versions, and writes `CITED_PRECEDENT` edges.
5. **Conformance.** A conformance check over all runs (Day 18, Exercise 7), checking both edges and guards.
6. **The audit test passes** (below): for one decision affected by a later correction, a procedure change and an overturned precedent, the agent answers all four parts of the question from the graph, with citations.

---

## Choose your domain

Keep the domain from Day 15 if it has a real written procedure; otherwise pick one below. Use **synthetic or anonymised data** — never real customer, patient or claimant data in a learning project.

| Domain | The procedure you compile | The decision you trace | Why precedent matters |
|---|---|---|---|
| **Insurance claims / SIU** | Claim triage playbook: coverage rule → fraud score → fast-track / adjuster / SIU → cited report → sign-off | Pay, refer or deny | Consistent SIU outcomes; overturned denials must not become precedent |
| **IT incident response** | An on-call runbook (alert → diagnose → mitigate → escalate) | Mitigation chosen | "This happened before — and the first fix was wrong" |
| **Lending** | Credit-limit or hardship policy with thresholds and an underwriter gate | Approve, refer, decline (with reasons) | Fair, consistent treatment; replay under a new policy |
| **Customer support** | Refund, returns or escalation policy | Refund, decline, escalate | Exceptions must stay exceptions |
| **Healthcare admin** (non-diagnostic) | Repeat-prescription or referral-routing SOP | Route and approval | Corrections to records are routine; fairness depends on `known_at` |
| **Trust & safety** | Moderation guideline with regional rules and appeals | Remove, restrict, reinstate | Appeals supersede; guidelines are versioned by region |

---

## Build plan (work through these phases)

### Phase 1 — Make the Day 15 graph bitemporal (20 min)

Your Day 15 edges carry provenance (`source`, `confidence`, `extracted_at` — Day 13's pattern) but no time windows. Backfill them honestly:

```cypher
// 1. Record time: when the graph first believed the edge. extracted_at is the best evidence you have.
MATCH ()-[r]->()
WHERE r.recorded_at IS NULL
SET r.recorded_at = coalesce(r.extracted_at, datetime());

// 2. Valid time: you usually DON'T know when the fact became true. Don't invent it.
//    Use an explicit sentinel and a flag, so queries still work and nobody mistakes it for a real date.
MATCH ()-[r]->()
WHERE r.valid_from IS NULL
SET r.valid_from = datetime('0001-01-01T00:00:00Z'), r.valid_from_unknown = true;
```

That second step is the Open World Assumption from Day 1 in practice: "we don't know when it started" is different from "it started at the beginning of time". The flag keeps the difference visible. Then:

- Replace your Day 8/15 writer's `MERGE`-and-overwrite with Day 16's `believe()` pattern — retire, then write — for every relationship type that can change.
- Replace Day 14's `expire_unseen_facts` deletion with `last_confirmed_at` (Day 16, Exercise 4).
- Index `valid_from` on the relationship types you'll filter most.
- Run the overlap check, filtered to relationship types your ontology says are one-at-a-time.

### Phase 2 — Compile one real procedure (30 min)

Take a real written procedure from your domain and run [Day 18's](day-18-drill-sop-to-procedure-graph.md) six-step conversion. Produce four artefacts and keep them in your project:

1. **The step table** — step · type · inputs · outputs (Day 18, Exercise 1).
2. **The routing functions** — complete and exclusive, with the boundary convention written in a comment (Exercise 2).
3. **The owner questions** — every ambiguity, missing branch and implicit step you found, written as a question with your interim assumption (Exercises 3, 6).
4. **The LangGraph build** — with a durable checkpointer, side effects in their own nodes, and human gates as `interrupt()` (Exercise 4).

Store the procedure in Neo4j as data, with Day 16's clocks:

```cypher
CREATE (p:Procedure {name: $name, version: 1,
                     valid_from: datetime($effective_from), recorded_at: datetime()})
WITH p
UNWIND $steps AS st                                   // [{name, type}, ...] from your step table
CREATE (p)-[:HAS_STEP]->(:Step {name: st.name, type: st.type})
```

Then **change it once**: write a v2 with one changed threshold, effective from a date in the middle of your synthetic data. You'll need it for the audit test.

### Phase 3 — Write a trace from every run (20 min)

Adapt [Day 19's](day-19-context-graphs.md) trace writer so it runs as the last step of every LangGraph run (or as a hook after the graph finishes). Before writing code, fill in this table for your domain — it's Day 20's Exercise 2, applied:

| Evidence kind in my domain | Snapshot / pointer + `known_at` / hash only | Why |
|---|---|---|
| … | … | … |

Then score one real trace against the ten-field checklist. Common gaps: the rule *inputs* (needed for replay), the *agent version*, and *two* timestamps.

### Phase 4 — Precedent retrieval as an agent tool (20 min)

1. Write a **decision card** for every decision: type, outcome, rules fired, evidence kinds, key amounts, procedure version — 2–4 lines of structured text.
2. Embed the cards with any sentence-embedding model and create the vector index (Day 21).
3. Wrap the Day 21 precedent query as a tool and register it with your Day 15 agent the same way you registered your graph tool:

```python
from datetime import datetime, timezone
from neo4j import Driver

def find_precedents(driver: Driver, embed, case: dict, tenant: str,
                    as_of: datetime | None = None) -> list[dict]:
    """Return up to 5 citable precedents: similar, standing as of `as_of`, same tenant,
    decided under compatible procedure versions. `embed` maps text -> list[float]."""
    as_of = as_of or datetime.now(timezone.utc)
    with driver.session() as s:
        rows = s.run(PRECEDENT_QUERY,                # Day 21's query, plus: AND d.tenant = $tenant
                     card_vec=embed(case["card"]), type=case["type"], as_of=as_of,
                     procedure=case["procedure"], compatible_versions=case["compatible_versions"],
                     fired_now=case["fired_rules"], context_ids=case["context_ids"], tenant=tenant)
        return [r.data() for r in rows]
```

4. In the trace writer, add one `CITED_PRECEDENT` edge per precedent the agent actually used.
5. Plant three decisions that **must not** come back: one superseded, one under an incompatible procedure version, one from another tenant. Check that none of them does.

### Phase 5 — The audit pack and the replay (20 min)

Two functions answer the audit question. The first reads the trace as it is:

```python
AUDIT = """
MATCH (d:Decision {id: $decision_id})
OPTIONAL MATCH (d)-[:FOLLOWED]->(p:Procedure)
OPTIONAL MATCH (d)-[e:EVALUATED]->(r:Rule)
OPTIONAL MATCH (d)-[:USED]->(ev:Evidence)
OPTIONAL MATCH (d)-[:APPROVED_BY]->(h)
OPTIONAL MATCH (d)-[:CITED_PRECEDENT]->(pre:Decision)
OPTIONAL MATCH (later:Decision)-[:SUPERSEDES]->(d)
OPTIONAL MATCH (pre)<-[:SUPERSEDES]-(fell:Decision)
RETURN d {.*} AS decision,
       p {.name, .version} AS procedure,
       collect(DISTINCT r {.id, .version, fired: e.fired, inputs: e.inputs}) AS rules,
       collect(DISTINCT ev {.kind, .sha256}) AS evidence,
       collect(DISTINCT h.id) AS approvers,
       collect(DISTINCT pre.id) AS precedents_cited,
       collect(DISTINCT pre.id + ' superseded by ' + fell.id) AS precedents_since_overturned,
       collect(DISTINCT later.id) AS superseded_by
"""
```

The second answers *"would we decide the same today?"* by **replaying the routing functions only** — no tools, no side effects — against today's procedure and today's knowledge:

```python
from collections.abc import Callable

Route = Callable[[dict], str]

def replay(start: str, routes: dict[str, Route | str], state: dict, max_steps: int = 50) -> list[str]:
    """Walk a procedure's routing functions over a fixed state. `routes[step]` is either a routing
    function (a junction) or the name of the next step. Steps that would call tools or humans are
    not executed: their recorded outputs must already be in `state`."""
    path, step = [], start
    while step != "END" and len(path) < max_steps:
        path.append(step)
        nxt = routes[step]
        step = nxt(state) if callable(nxt) else nxt
    return path
```

Build `state_then` from the trace's stored rule inputs and snapshots, and `state_now` by re-reading the pointer evidence with `known_at = now` (Day 16). Replay `state_now` through the *current* procedure version's `routes`. If the paths differ, report which input or which rule version made the difference — that is the answer to "would we decide the same today, and if not, why not?"

### Phase 6 — Health report additions (10 min)

Add these to your Day 14/15 health report:

```text
overlapping_current_windows:        0 (or documented as allowed by the ontology)
runs_without_trace:                  0
traces_missing_procedure_version:    0
traces_missing_rule_inputs:          0
conformance_violations (edges/guards): listed with run IDs, each triaged "graph wrong" or "people wrong"
decisions_citing_overturned_precedent: listed for review
open_owner_questions:                listed, with interim assumptions
```

---

## The audit test

Set up one decision **D** in your synthetic data so that, *after* D was made:

- a piece of evidence it used was **corrected** (Day 16),
- the procedure moved to **v2** with a changed threshold (Day 17),
- one precedent D cited was **superseded** (Days 19, 21).

Then ask your agent, using only its tools:

> *"On the date of decision D: what did we believe about [the subject], which procedure did we follow, and why did we decide what we did? Would we decide the same today — and if not, what changed?"*

A passing answer contains every item below, each traceable to a node or edge:

| Part | Must include | Comes from |
|---|---|---|
| What we believed | The evidence as known on D's date — including the value that was later corrected | Day 16 (`known_at` = D's date) |
| Which procedure | The version D followed and the path it took | Day 17 |
| Why | The rules evaluated, with their inputs; approvers; exceptions, if any | Days 19–20 |
| Precedent | The precedents D cited — and that one has since been overturned, so D needs review | Day 21 |
| Today | The replayed path under v2 with today's knowledge, and which input or rule version changed the outcome (or that nothing did) | Days 16, 17, 20 |

**Fail conditions:** any part answered from text the LLM made up rather than from the graph; the corrected value shown as what was known on D's date; the superseded precedent presented as still valid; "would we decide the same today?" answered by asking the LLM rather than by the replay.

---

## Grading yourself

| Criterion | Pass | Strong |
|---|---|---|
| Two clocks | All time-sensitive edges bitemporal; overlap check clean | Unknown valid starts flagged, not invented; `last_confirmed_at` replaces deletion |
| Procedure | One real SOP compiled with ≥ 1 rule junction and ≥ 1 human gate | Owner questions answered by a real owner; v2 change made from a real memo |
| Traces | 10/10 on the checklist for every run | Evidence choices table justified; PROV- or FHIR-aligned naming |
| Precedent | Planted bad precedents never returned | Weights tuned on ≥ 20 reviewer-labelled pairs; tenant isolation tested |
| Conformance | Edge + guard checks over all runs | Each violation triaged, with one procedure change proposed (Day 21, Exercise 3) |
| Audit test | All five parts, graph-sourced, with citations | A colleague who didn't build it can run it and understand the answer |

---

## Suggested readings for today

**Required:** Re-read **"Where it breaks / what it is not"** on the day whose phase gave you the most trouble today. On a second reading after a real build, those sections tend to read very differently.

**If you want the deep version:**
- Rasmussen et al., "Zep: A Temporal Knowledge Graph Architecture for Agent Memory," arXiv:2501.13956, 2025 — now read the parts on how an LLM *detects* which old edge a new fact contradicts. Your Phase 1 writer assumes you already know; in production, you often don't.
- van der Aalst, *Process Mining: Data Science in Action*, 2nd ed., Springer, 2016 — process *discovery*: learning the procedure from event logs rather than from a document. Once your runs are in the graph, you can compare the procedure you wrote with the one people actually follow.
- Garg & Gupta, "AI's trillion-dollar opportunity: Context graphs," Foundation Capital, 2025, and Tomicevic, InfoWorld, 2026 — read them together, now that you've built it, and decide for yourself which one is right about what decision traces can and can't do.

---

← [Day 22 — Rest & Synthesize III](day-22-rest-and-synthesize-iii.md) &nbsp;|&nbsp; **Back to course overview:** [README](../README.md)
