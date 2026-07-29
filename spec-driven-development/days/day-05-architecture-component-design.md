*[Spec-Driven Development](../README.md) · Day 5 of 8*

# Day 5 — Architecture & Component Design

> **Today's one idea:** For any non-trivial feature, a one-page technical design sits *between* the spec and the plan — components, contracts, boundaries, and key decisions — closing the architectural degrees of freedom the spec leaves open, so the agent executes *your* structure instead of quietly inventing its own.
> **Reading time:** ~35 min · **Prereqs:** [Day 1](day-01-spec-agent-contract.md), [Day 2](day-02-anatomy-of-executable-spec.md), [Day 4](day-04-writing-feature-specs.md)
> **Primary source for today:** John Ousterhout, *A Philosophy of Software Design*, 2nd ed., Yaknyam Press, 2021 — Chapter 4 ("Modules Should Be Deep") and Chapter 5 ("Information Hiding").

---

## The hook

You wrote a clean Day 4 feature spec: *"When a task is assigned to a user, notify them."* Appetite set, no-gos listed, three testable acceptance criteria. You hand it to the agent and say **"build it."**

Two minutes later it hands back a working feature — plus a brand-new **WebSocket server**, a **Redis pub/sub channel**, and a **background worker process**. It runs. Every acceptance criterion passes.

It also just tripled your infrastructure. You had assumed the notification would piggyback on the 30-second poll your app already does. You never said so. The spec told the agent *what the user sees*. It said nothing about *the shape of the solution* — so the agent picked a shape. A reasonable one. An expensive one, and not yours.

This is the same failure you met on [Day 1](day-01-spec-agent-contract.md) — ambiguity resolved invisibly, by the agent, and discovered too late. Except this time the ambiguity isn't in the *product* requirements. It's in the **architecture**. And that is the most expensive place to discover it.

---

## Building the intuition

By Day 4 you have a feature spec. By tomorrow you'll run it through the agent. It is tempting to go straight from one to the other. Don't — not for anything with real structure. There is a document that belongs in between.

Think of three documents, each answering a different question:

| Document | Day | The question it answers |
|---|---|---|
| **Feature spec** | Day 4 | *What* and *why* — the user-visible behaviour, the no-gos, the acceptance criteria |
| **Technical design** | **Today** | *What shape* — which components exist, who owns what, how data crosses the boundaries |
| **Plan** | Day 6 | *What steps* — the ordered, file-by-file changes the agent makes |

Here is the analogy that makes it click. You are building a house. The **brief** (the spec) says "three bedrooms, this budget, and keep the big oak tree" — that last one is a no-go. The **architectural drawing** (the design) says where the load-bearing walls go, where the plumbing runs, which room is the kitchen. The **construction schedule** (the plan) says "week 1 foundation, week 2 framing."

Hand a builder only the brief and they *will* make the structural calls themselves — where the walls go, where the water runs. Maybe fine. Maybe they put the kitchen where you wanted the study, and moving it later means tearing out plumbing. The drawing is where you make the decisions that are **expensive to reverse**, on purpose, before anyone pours concrete.

For a coding agent the expensive-to-reverse decisions are the same handful every time:

- **Data model & contracts** — the shapes that cross each boundary
- **Component boundaries** — what is a separate module vs. a function
- **Where state lives** — client, server, cache, database
- **API & event shapes** — endpoints, payloads, messages
- **Integration points** — what this feature touches, and how

The feature spec leaves every one of these open. If you skip the design, the agent closes them — silently, while it plans — and you find out in code review. The design is you closing them first.

This is exactly Ousterhout's argument. A good module is **deep**: a simple interface hiding a substantial implementation. The design step is where you decide those interfaces — the *contracts* between components — which is the single highest-leverage decision in the whole feature, because everything else is built against them. And it is [Parnas's information hiding](#suggested-readings-for-today): each component should *own a secret* — a decision the rest of the system can't see and doesn't depend on. The design is where you name who owns which secret.

---

## The formal picture

A technical design for a coding agent has **four parts** — and fits on one page.

### Part 1 — Component breakdown

List the pieces you'll build, each with a **single responsibility**, one line each. A Small feature has two to four.

```markdown
## Components
- AssignController   — handles the assign request, delegates the rest
- NotificationService — decides what a notification is and creates one
- NotificationStore   — persists and queries notifications
- Toaster (UI)        — renders unread notifications on the client
```

### Part 2 — Contracts & data model

The types, signatures, and event shapes that cross the boundaries between those components. **This is the deep interface — the part that is expensive to change later**, so it is the part worth deciding deliberately.

```markdown
## Contracts
- POST /tasks/:id/assign  { assigneeId }         → 200
- NotificationService.create(userId, type, payload) → Notification
- Notification { id, userId, type, read, createdAt }
- GET /notifications?unread=true                 → Notification[]
```

### Part 3 — Boundaries

What each component **owns** and must **not** touch — and where state lives. This is information hiding made explicit.

```markdown
## Boundaries
- NotificationService owns "what counts as a notification + how it's created".
- AssignController must NOT write to NotificationStore directly — go through the service.
- State lives in the DB (notifications table). No client-side notification state.
- Delivery uses the existing 30s poll. No new socket, no worker, no Redis.
```

### Part 4 — Key decisions & trade-offs (a mini-ADR)

Two to four decisions, each in one sentence: **we will X, because Y; we considered Z and rejected it because W.** This is an *Architecture Decision Record* — the format is worth stealing wholesale (see readings).

```markdown
## Decisions
- Persist notifications (not fire-and-forget) — unread state must survive a
  reload; considered in-memory, rejected (lost on refresh).
- Deliver via the existing poll — a WebSocket triples infra for this appetite;
  sockets are a roadmap item, not this feature.
- In-app toast only; email deferred to the roadmap.
```

### The component sketch

Finally, a boxes-and-arrows diagram — the "component" level of [Simon Brown's C4 model](#suggested-readings-for-today). Boxes are components; arrows are calls or data.

```mermaid
flowchart TD
    UI["Assign task<br/>(UI)"] -->|POST /tasks/:id/assign| C["AssignController"]
    C -->|create(userId, ...)| NS["NotificationService"]
    NS -->|insert| DB[("NotificationStore<br/>(DB)")]
    T["Toaster (UI)"] -->|GET /notifications?unread| C
    C -->|query| DB
    classDef svc fill:#128C86,color:#fff,stroke:#0E1B2A;
    classDef ui fill:#34506E,color:#fff,stroke:#0E1B2A;
    class NS svc
    class UI,T ui
```

One page. Roughly fifteen minutes to write. And now the handoff: tomorrow's plan is generated **from this design** — "read the spec *and the design*, then produce a numbered plan that implements the design." The agent stops choosing the architecture and starts executing yours.

---

## Where it breaks / what it is not

**"So I write a design for every feature now?"**
No. For a **Small** appetite feature ([Day 4](day-04-writing-feature-specs.md)) with one obvious component — add a search box, export a CSV — the design collapses into the spec. Skip it. The design earns its keep on **Medium and Large** features: multiple components, a real data model, a new integration point. Rule of thumb: if you can't name the components without thinking, you need the design; if there's only one, you don't.

**"Isn't this just a high-level design document?"**
It is a *one-page* one. If your design runs to forty pages, you are either over-designing or specifying implementation the agent should own. Name the components and the contracts — not the variable names, not the exact library. This is the same discipline as [Day 2](day-02-anatomy-of-executable-spec.md): constrain the *what*, not the *how*.

**"Design and plan sound like the same thing."**
They aren't, and conflating them is the most common mistake. The **design is the shape** — stable, decided once, changes rarely. The **plan is the steps** — ephemeral, regenerated on every run of the loop. File-by-file changes belong in the plan, never in the design. If your design has a step numbered "3," it has leaked into plan territory.

**"Can the agent write the design for me?"**
Use it as a design *partner*, not an author: *"Given this spec, propose two or three designs with trade-offs."* Then **you choose, and you write the chosen one down.** This is the one place a conversation with the agent is genuinely useful — but capture the decision in the document, because (as on [Day 1](day-01-spec-agent-contract.md)) a conversation is not durable and a document is.

---

## Try it yourself

### Exercise 1 — Retrieve the three documents
Close this page. In your own words, write the one question each of these answers: the **feature spec**, the **technical design**, the **plan**. Then list the **four parts** of a one-page technical design. No looking.

<details>
<summary>Answer</summary>

- **Feature spec** → *what & why* (user-visible behaviour, no-gos, acceptance criteria).
- **Technical design** → *what shape* (which components, who owns what, how data crosses boundaries).
- **Plan** → *what steps* (ordered, file-by-file changes).

Four parts of the design: **component breakdown, contracts & data model, boundaries, and key decisions (mini-ADR)** — plus a component sketch.
</details>

### Exercise 2 — Write a design (direct application)
Take a feature you would actually build this month (or reuse the **CSV-export** feature from Day 4 if you scale it up — say, "scheduled CSV exports emailed weekly," which is now Medium, not Small). Write its one-page design: the components with one-line responsibilities, the contracts that cross the boundaries, and **two** decisions in ADR form. Time-box it to 15 minutes. If it takes longer, you are specifying implementation — pull back to shape.

<details>
<summary>Hint</summary>
Start from the boundaries. Ask: "what are the two or three things that must NOT know about each other?" Each of those is a component. The contract is whatever has to pass between them.
</details>

### Exercise 3 — Diagnose a broken design (stretch)
Here is a design fragment for the notification feature. Find the two structural problems.

```markdown
## Components
- AssignController — handles the request, creates the notification row,
  and formats the toast HTML
- NotificationService — calls AssignController.getNotifications()

## Boundaries
- (none listed)
```

<details>
<summary>Answer</summary>

1. **The controller does everything; the service is hollow.** `AssignController` writes the DB row *and* formats UI HTML *and* handles the request — three responsibilities, no boundary. Meanwhile `NotificationService` just calls back into the controller. In Ousterhout's terms the service is a **shallow module** (an interface that adds no value) and the controller is a leaky god-object. The responsibilities are inverted: the *service* should own creation and persistence; the controller should only translate the request and delegate.

2. **No boundaries section, so nothing is hidden.** With "who owns what" left blank, every component can reach into every other — the agent will wire them together however is shortest, and you'll get exactly the tangle you didn't specify against. Information hiding only works if you write down the secret each component keeps.
</details>

---

## Connect it back

Yesterday ([Day 4](day-04-writing-feature-specs.md)) you wrote the feature spec — the *what* and the *why*. Today you added the layer that lives between the spec and the agent: the **technical design**, the *shape*. The spec closes the **product** degrees of freedom; the design closes the **architectural** ones — the data model, the boundaries, the decisions that are ruinous to get wrong and cheap to get right on paper.

Tomorrow ([Day 6](day-06-plan-implement-verify.md)) you finally run it through the agent with the plan-implement-verify loop — and the plan you get back will implement *this design*, step by step, instead of improvising one.

The sharp question to carry into tomorrow: *for the feature you're about to build, which single structural decision would hurt most to get wrong — and is it written down anywhere yet?*

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Ousterhout, *A Philosophy of Software Design*, 2nd ed., Chapter 4 ("Modules Should Be Deep"). The deep-vs-shallow distinction *is* the test for a good component boundary: a deep module hides a lot behind a small interface. Read it and your Part 1 and Part 3 will get sharper immediately.

**If you want the deep version:**

- Parnas, D.L., "On the Criteria To Be Used in Decomposing Systems into Modules," *Communications of the ACM*, 15(12), 1972, pp. 1053–1058. DOI: 10.1145/361598.361623. The founding argument for **information hiding**: decompose a system by the *decisions each module hides*, not by the steps of the process. Fifty years old and still the clearest justification for the "boundaries" part of today's design.
- Nygard, Michael, "Documenting Architecture Decisions," 2011 (cognitect.com/blog/2011/11/15/documenting-architecture-decisions). The origin of the **ADR** format used in Part 4 — context, decision, consequences, in a few sentences. This is the lightweight capture that keeps a design one page.
- Brown, Simon, *The C4 Model for Visualising Software Architecture* (c4model.com). The "Component" level is exactly the granularity of today's sketch — enough structure to be useful, not so much that it rots the moment code changes.

---

← [Day 4 — Writing Feature Specs](day-04-writing-feature-specs.md) &nbsp;|&nbsp; [Day 6 — Plan → Implement → Verify →](day-06-plan-implement-verify.md)
