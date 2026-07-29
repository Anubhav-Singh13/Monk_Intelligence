# Spec-Driven Development with Coding Agents

> **The promise:** By the end of this course, you will have a repeatable, tool-agnostic workflow — project constitution, feature spec, and plan-implement-verify loop — that takes any feature idea to deployed code with a coding agent doing the implementation.

---

## Who this course is for

This course is for product managers and technical leads who have some coding background and are already using (or about to use) AI coding agents like Claude Code, Cursor, or GitHub Copilot Workspace. You don't need to be a software engineer — but you should be comfortable reading code, writing a ticket, and understanding what "it deployed successfully" means.

**Depth level: L1 — Practitioner.** You will learn to write specs that agents execute well, direct the agent through a structured loop, and wire the output into a deployment pipeline. You will not build an agent from scratch. If you want to build agents, this course gives you the vocabulary but not the implementation depth.

**Who should not take it:** If you have zero coding background, Day 6 (the pipeline) will require extra time with the linked resources. If you are a senior engineer who already uses spec-driven workflows daily, Days 1–3 will be review — start at Day 4.

---

## The arc at a glance

On Day 1 you arrive with a vague sense that AI coding tools are powerful but unpredictable. By Day 3 you understand that the unpredictability lives entirely in your input — and you have a concrete document (the project constitution) that eliminates most of it. By Day 5 you can turn a feature spec into a one-page technical design that fixes the architecture before the agent starts. By Day 6 you have a named loop (plan → implement → verify) you can run with any agent on any feature. By Day 8 you have shipped something real using a workflow that is repeatable, tool-agnostic, and entirely driven by documents you control.

This course is about one transfer of power: from the agent to the spec author. "Vibe coding" puts the agent in charge — you describe a mood and hope. Spec-driven development puts you in charge — you write a contract and the agent executes it. A PM already knows how to write requirements. This course shows that a well-structured spec *is* the program. The project constitution is your project charter; the feature spec is your ticket; the technical design is your blueprint; the plan-implement-verify loop is your sprint. The pipeline on Day 7 is just those things automated.

---

## How to use this course

- **Rhythm:** One page per day, 30–45 minutes of focused reading. Do not skip ahead — each day assumes the vocabulary of the days before it.
- **Exercises:** The "Try it yourself" section on each day is not optional. The exercises are short (5–10 min) and are the only way to know whether an idea clicked or just felt like it did.
- **Bibliography:** [`bibliography.md`](bibliography.md) has the full annotated shelf. When a day says "primary source," open it if you have 15 extra minutes. Optional dives are for when a specific idea won't let you go.
- **Rest and synthesize:** This 8-day course is already compressed — no formal rest days. On Day 7, before reading new material, spend 5 minutes re-reading your Day 3 constitution, Day 4 spec, and Day 5 design. That review is the rest.
- **If life gets in the way:** Missing one day means re-reading the previous day's "Connect it back" section (2 min) before continuing. Do not restart from Day 1.

---

## The learning path

| Day | Title | One idea | Core source |
|-----|-------|----------|-------------|
| [1](days/day-01-spec-agent-contract.md) | The Spec-Agent Contract | Ambiguous input produces ambiguous output — every time | SWE-bench paper, arXiv:2310.06770 |
| [2](days/day-02-anatomy-of-executable-spec.md) | Anatomy of an Executable Spec | Every actionable spec has four layers: context, goal, constraints, acceptance criteria | *Shape Up*, Ch. 3 |
| [3](days/day-03-project-constitution.md) | The Project Constitution | One persistent document — mission, stack, conventions — that all feature specs inherit from | DeepLearning.AI course, Lesson 3 |
| [4](days/day-04-writing-feature-specs.md) | Writing Feature Specs | A feature spec is a shaped pitch + explicit no-gos + testable acceptance criteria | *Shape Up*, Ch. 5 |
| [5](days/day-05-architecture-component-design.md) | Architecture & Component Design | A one-page technical design — components, contracts, boundaries, decisions — sits between the spec and the plan and fixes the architecture up front | *A Philosophy of Software Design*, Ch. 4–5 |
| [6](days/day-06-plan-implement-verify.md) | Plan → Implement → Verify | The core agent loop: plan first (from the design), implement second, verify against acceptance criteria third | DeepLearning.AI course, Lessons 4–5 |
| [7](days/day-07-full-pipeline.md) | The Full Pipeline | Spec → design → agent → CI → deploy is a single template you fill in once and reuse forever | *Continuous Delivery*, Ch. 5 |
| [8](days/day-08-capstone.md) | Capstone: Ship a Feature | Write a constitution + spec + design, run the loop, push through CI, deploy something real | All prior days |

---

## The Course Shelf — top 6

1. **Shape Up** — Ryan Singer, Basecamp, 2019. Free at basecamp.com/shapeup. *The best existing format for product specs that agents can act on.*
2. **Spec-Driven Development with Coding Agents** — Paul Everitt, DeepLearning.AI. learn.deeplearning.ai/courses/spec-driven-development-with-coding-agents. *The direct practical model for the project constitution and plan-implement-verify loop.*
3. **A Philosophy of Software Design** — John Ousterhout, 2nd ed., 2021. *The design-quality intuition — deep modules, information hiding — behind Day 5's component boundaries.*
4. **Continuous Delivery** — Jez Humble & David Farley, Addison-Wesley, 2010. *The pipeline skeleton for Day 7.*
5. **SWE-bench** — Jimenez et al., NeurIPS 2024, arXiv:2310.06770. *Empirical proof that spec quality predicts agent output quality.*
6. **Claude Code Docs** — docs.anthropic.com/claude-code. *Practical reference for CLAUDE.md and pipeline wiring in Days 4–7.*

Full annotated shelf: [`bibliography.md`](bibliography.md)

---

## The capstone

On Day 8, you will pick a real feature from a project you own or care about, write a project constitution, a feature spec, and (for non-trivial features) a one-page technical design using the templates from Days 3–5, run the plan-implement-verify loop from Day 6, and push the output through a minimal pipeline to a deployed (or deployable) state. "Done" means: the agent's output passes your acceptance criteria, a CI check runs green, and you could hand the spec + pipeline config to a colleague and they could reproduce the result without asking you a single question.

---

## Glossary

[`glossary.md`](glossary.md)

---

## Meta

- **Depth level:** L1 — Practitioner
- **Estimated total hours:** ~5.5 hours (8 × 35 min + capstone ~45 min)
- **Primary external reference:** learn.deeplearning.ai/courses/spec-driven-development-with-coding-agents
- **Last updated:** July 2026
- **Feedback / corrections:** Edit the Markdown directly or open an issue in the host repository
