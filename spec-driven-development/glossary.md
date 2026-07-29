# Glossary — Spec-Driven Development with Coding Agents

Terms are listed alphabetically. Each entry has: a plain-English definition, a one-line formal definition, and the day it was first introduced.

---

**Acceptance criteria**
*Plain English:* The 3–5 statements that tell both you and the agent exactly when a feature is done. Each one is either true or false — no gray area.
*Formal:* Testable postconditions expressed in Given/When/Then format that a completed implementation must satisfy.
*Introduced: Day 2*

---

**Agent replaceability**
*Plain English:* Designing your workflow so that swapping one coding agent for another (e.g., Claude Code → Cursor) requires no changes to your specs, designs, or constitution — only to the CLI you type.
*Formal:* The property of a spec-driven workflow where the spec, design, and constitution are the primary artifacts, making the agent an implementation detail.
*Introduced: Day 7*

---

**Appetite**
*Plain English:* How much time and complexity you are willing to accept for a feature. Setting an appetite before writing a spec prevents the agent from building a skyscraper when you needed a shed.
*Formal:* A time-box or complexity budget declared in a feature spec that scopes the acceptable solution space (term from Shape Up by Ryan Singer).
*Introduced: Day 4*

---

**Architecture Decision Record (ADR)**
*Plain English:* A one- or two-sentence record of a design choice: what you decided, why, and what you considered and rejected. It keeps a design's reasoning from evaporating.
*Formal:* A short document capturing a single architectural decision, its context, and its consequences (format from Michael Nygard, 2011).
*Introduced: Day 5*

---

**Boundary**
*Plain English:* The line around a component saying what it owns and what it must not touch. Good boundaries hide one decision inside one component so the rest of the system doesn't depend on it.
*Formal:* The interface separating a module's hidden implementation (its "secret") from its consumers; the unit of information hiding (Parnas, 1972).
*Introduced: Day 5*

---

**CLAUDE.md**
*Plain English:* A file you put in the root of your project that Claude Code reads at the start of every session. It tells the agent what the project is, what stack it uses, and what conventions to follow — i.e., it is the project constitution for Claude Code specifically.
*Formal:* A Markdown file in the project root that Claude Code loads as persistent context; Anthropic's implementation of the project constitution pattern.
*Introduced: Day 3*

---

**Component**
*Plain English:* One of the pieces you break a feature into, each with a single clear responsibility — e.g., a NotificationService that owns creating and storing notifications.
*Formal:* A cohesive unit of implementation with one responsibility, exposing a narrow interface and hiding its internals; a "deep module" in Ousterhout's terms.
*Introduced: Day 5*

---

**Constraints**
*Plain English:* The "don't do this" layer of a spec. Every constraint is a rabbit hole the agent won't fall into.
*Formal:* Negative requirements that bound the solution space — what the implementation must not do, must not change, or must stay consistent with.
*Introduced: Day 2*

---

**Context**
*Plain English:* Everything the agent needs to know about the world to make this specific change — the relevant file, the existing pattern, the tech stack.
*Formal:* The background information layer of a spec that provides the agent with the situational knowledge required to interpret the goal correctly.
*Introduced: Day 2*

---

**Contract**
*Plain English:* The agreed shape of what passes between two components — the function signature, API endpoint, or event payload. The part of a design most expensive to change later, so you decide it deliberately.
*Formal:* The interface between components: the types, signatures, and message shapes exchanged across a boundary.
*Introduced: Day 5*

---

**Deployment pipeline**
*Plain English:* The automated chain that takes code from your editor to production: commit → tests → CI → staging → deploy. If any gate fails, nothing moves forward.
*Formal:* A sequence of automated stages (build, test, acceptance, deploy) through which every code change must pass before reaching production (from Humble & Farley, *Continuous Delivery*).
*Introduced: Day 7*

---

**Feature spec**
*Plain English:* A short document (one page or less) that tells the agent what to build for a single feature — using the shaped pitch + no-gos + acceptance criteria format.
*Formal:* A structured requirement document with appetite, problem statement, solution sketch, explicit no-gos, and testable acceptance criteria that a coding agent can execute without additional context beyond the project constitution.
*Introduced: Day 4*

---

**Given/When/Then**
*Plain English:* A three-part sentence format for writing acceptance criteria. "Given [starting state], when [action], then [expected outcome]." Each one is a mini test case.
*Formal:* A structured scenario format from Behaviour-Driven Development (BDD) used to express acceptance criteria as unambiguous, automatable test cases (from Wynne & Hellesøy, *The Cucumber Book*).
*Introduced: Day 2*

---

**Goal**
*Plain English:* The single outcome you want from the agent — stated as user-visible behavior, not as implementation steps.
*Formal:* The desired postcondition layer of a spec: the user-observable change the implementation must produce, expressed independently of the method.
*Introduced: Day 2*

---

**No-gos**
*Plain English:* An explicit list of things the agent should NOT build, change, or include in this feature. The most underused, most valuable part of a spec.
*Formal:* Explicitly declared out-of-scope items in a feature spec that prevent scope creep and rabbit-hole implementations (term from Shape Up).
*Introduced: Day 4*

---

**Plan-implement-verify loop**
*Plain English:* The three-beat workflow for running a spec through a coding agent: first ask for a plan, then approve and implement, then verify each acceptance criterion.
*Formal:* A structured agent interaction protocol in which the agent produces an implementation plan (from the spec and technical design) before writing code, implements against that plan, and then explicitly checks each acceptance criterion (term from DeepLearning.AI course by Paul Everitt).
*Introduced: Day 6*

---

**Project constitution**
*Plain English:* A single persistent document — mission, tech stack, conventions, roadmap — that you give to any coding agent at the start of any session so it immediately understands the project without you repeating yourself.
*Formal:* A project-level context document that all feature specs inherit from; defines the invariants (stack, conventions, constraints) that apply to every implementation in the codebase (term from DeepLearning.AI course by Paul Everitt).
*Introduced: Day 3*

---

**Shaped pitch**
*Plain English:* A product description that is concrete enough for a builder to act on but vague enough to leave implementation decisions to them. It describes what and why, not how.
*Formal:* A feature description format comprising appetite, problem statement, and solution sketch — concrete enough to remove uncertainty, abstract enough to preserve builder autonomy (term from Shape Up by Ryan Singer).
*Introduced: Day 4*

---

**Spec**
*Plain English:* Any document that tells a coding agent what to build. In this course, a spec always means a structured document with context, goal, constraints, and acceptance criteria.
*Formal:* A structured natural-language contract that defines the input to a coding agent; the primary artifact of spec-driven development.
*Introduced: Day 1*

---

**Spec-driven development**
*Plain English:* A workflow where you write a complete, structured specification before asking a coding agent to write any code — the opposite of vibe coding.
*Formal:* A software development methodology in which structured specifications (project constitution + feature specs) are the primary inputs to coding agents, making the spec the durable artifact and the agent the interchangeable executor.
*Introduced: Day 1*

---

**Technical design**
*Plain English:* A one-page document between the feature spec and the plan. It says what *shape* the solution takes — which components exist, how data crosses between them, and the key decisions — so the agent executes your architecture instead of inventing one.
*Formal:* A lightweight design artifact (components, contracts, boundaries, decisions) that closes the architectural degrees of freedom a feature spec leaves open, produced before the implementation plan.
*Introduced: Day 5*

---

**Vibe coding**
*Plain English:* Asking a coding agent to build something using a loose, conversational description and iterating in real time. Fast to start, expensive to debug.
*Formal:* An informal agent interaction pattern in which requirements are communicated implicitly or incrementally rather than through explicit, structured specifications; maximizes agent degrees of freedom.
*Introduced: Day 1*
