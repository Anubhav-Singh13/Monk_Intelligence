# Monk Intelligence Wiki

> *A library of structured, concept-first courses for building durable expertise in AI, decision-making, and the engineering workflows that surround both.*

---

## What this is

Each course in this library is built on the same principle: **understanding compounds, information decays**. The goal is not to teach the current version of a tool — it is to build the mental models that make every future version obvious.

Courses are designed to be read in order, one page per day, 30–45 minutes of focused work. There are no videos, no quizzes, and no certificates. There are first principles, worked examples, and exercises designed so that understanding either clicks or visibly doesn't — and you know which.

---

## Course Catalogue

### 🤖 Agentic Learning Path

*The conceptual foundation for everything else in this library.*

A technology-agnostic, concept-first progression through the building blocks of modern AI systems — from how LLMs work to multi-agent architectures, RAG, evaluation, and production deployment. Designed to be durable: the ideas here remain true as tools change.

**Format:** 5 modules · Reference depth  
**Best for:** Anyone building or directing AI systems who wants the *why* before the *how*

**→ [Start here](<Agentic_learning path/README.md>)**

---

### 🧩 Agentic Design Patterns

*33 days from "I've built agents" to "I can name, implement, and stress-test every pattern in the field."*

A vocabulary-first, builder-depth course covering every major agentic design pattern — from Chain-of-Thought and ReAct through Tree of Thoughts, self-improvement loops (Self-Refine, STaR, ExpeL), skill libraries, four memory stores, and the full multi-agent stack (Orchestrator-Worker, Critic-Actor, Peer Debate, Mixture of Agents, Guardrails). Exercises are non-optional: the pattern doesn't become yours until you've debugged it at least once.

**Format:** 33 days · 7 modules · ~22 hours  
**Depth:** L2 — Builder  
**Best for:** Engineers who have called an LLM API and built something with LangChain, AutoGen, or a similar framework, and want a precise shared vocabulary — the kind where "this system uses ReAct with an ExpeL-style skill library and a Critic-Actor multi-agent layer" carries exact meaning

**→ [Start Day 1](agentic-design-patterns/README.md)**

---

### 💻 Claude Code Academy

*From first run to production-grade workflows — a practitioner's field guide.*

Ten focused lessons covering the full Claude Code workflow: navigating codebases, writing effective prompts, debugging, testing, refactoring, CLAUDE.md configuration, context management, and two capstone projects. Each lesson is a self-contained reference page you will return to after the first read.

**Format:** 10 lessons · 4 phases  
**Best for:** Engineers and tech leads already using (or about to use) Claude Code on real projects

**→ [Enter the academy](<Claude Code/README.md>)**

---

### 📖 Claude Code 101

*14 days from "I have Claude Code installed" to "I have Claude Code deployed."*

A structured course that builds the four configuration layers of Claude Code from scratch: project memory, behavior gates, workflows, and orchestration. The second half of the course deploys each layer on a real shared codebase (TaskFlow) so every concept has immediate, verifiable evidence.

**Format:** 14 days · 5 modules  
**Best for:** Engineers who have used Claude Code casually and want to use it professionally

**→ [Start Day 0](claude-code-101/README.md)**

---

### 🏗️ Grokking System Design

*21 days from "I can build things" to "I can design and justify them."*

A practitioner's course for .NET developers working in the Azure ecosystem who can implement features but have never been taught to design systems. Covers the four-step design methodology, trade-off analysis, C4 diagrams, storage and compute building blocks (relational, NoSQL, caching, blob, messaging), distributed systems constraints (CAP, sagas, observability, resilience), three full worked system designs, and a self-directed capstone from a one-line brief.

**Format:** 21 days · 5 modules  
**Best for:** .NET/Azure engineers who want to produce defensible High-Level and Low-Level Designs with explicit trade-off reasoning — and pass a system design interview along the way

**→ [Start Day 1](grokking-system-design/README.md)**

---

### 🎯 Decision Science & Game Theory

*65 days from intuition to rigorous strategic reasoning.*

A graduate-level course compressed for practitioners — covering decision theory, Bayesian reasoning, cognitive biases, game theory, mechanism design, reinforcement learning, and personal decision-making. Designed for product managers, engineers, and operators who make consequential decisions under uncertainty and want to do it better.

**Format:** 65 days · 5 modules  
**Best for:** Anyone who makes decisions for a living and wants a formal framework to audit their intuitions against

**→ [Start Day 1](decision-science-game-theory/README.md)**

---

### 📋 Spec-Driven Development

*7 days from "vibe coding" to a repeatable, tool-agnostic delivery workflow.*

A short, dense course on writing specs that AI coding agents can execute reliably. Covers the spec-agent contract, the four-layer anatomy of an executable spec, the project constitution, feature spec templates, the plan-implement-verify loop, and a full CI/deploy pipeline. Everything is tool-agnostic: the workflow works with Claude Code, Cursor, or any future agent.

**Format:** 7 days · Single arc  
**Best for:** Product managers and technical leads who direct AI coding agents and want predictable output

**→ [Start Day 1](spec-driven-development/README.md)**

---

### 🕸️ Knowledge Graphs: From Concept to Production

*15 days from "what is a triple" to a live KG an AI agent queries as structured memory.*

A concept-to-builder course for practitioners with an AI/LLM background who want knowledge graphs in their production toolkit. Covers triples, property graphs, ontologies, Cypher querying, entity extraction, ingestion pipelines, GraphRAG, and KG embeddings — culminating in a working domain-specific knowledge graph wired into a real agent.

**Format:** 15 days · Single arc (L1 + L2)  
**Best for:** Engineers and AI practitioners who already work with RAG or agents and want structured, queryable, multi-hop memory on top of their existing stack

**→ [Start Day 1](knowledge-graphs/README.md)**

---

### 🍵 Japanese Philosophy for Better Living

*21 days from scattered fragments to a coherent, lived philosophy.*

A concept-first journey through twenty-one ideas that form a distinctly Japanese relationship with reality: wabi-sabi, ikigai, ma, kaizen, kintsugi, yūgen, and more. Three modules — Perception, Practice, Integration — build from seeing differently, to acting differently, to sustaining what you've built when life pushes back. No background in philosophy or Japanese culture required; curiosity and thirty minutes a day is enough.

**Format:** 21 days · 3 modules  
**Best for:** Anyone who has encountered these ideas in fragments — on a podcast, in a design article, in a business book — and suspects there is something deeper and more coherent underneath

**→ [Start Day 1](japanese-philosophy/README.md)**

---

## How to navigate

Each course has its own **README** at the folder root — start there for the full day-by-day table of contents. Every lesson page has a breadcrumb at the top linking back to its course and module. Open a course folder, read the README, and begin at Day 1.

If you are new to this library, the recommended sequence is:

1. **[Agentic Learning Path](<Agentic_learning path/README.md>)** — build the conceptual foundation
2. **[Claude Code 101](claude-code-101/README.md)** or **[Claude Code Academy](<Claude Code/README.md>)** — get operational with the primary tool
3. **[Spec-Driven Development](spec-driven-development/README.md)** — make your prompts and specs reliable
4. **[Knowledge Graphs](knowledge-graphs/README.md)** — add structured, multi-hop memory to your agent stack
5. **[Decision Science & Game Theory](decision-science-game-theory/README.md)** — sharpen the reasoning behind every product and engineering decision

**Parallel track (standalone):** **[Grokking System Design](grokking-system-design/README.md)** — for .NET/Azure engineers who design the production systems that AI runs on; pairs with any step above

---

*Built and maintained with Claude Code.*
