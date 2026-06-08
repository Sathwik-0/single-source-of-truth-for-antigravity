# The Golden Spoon Prompt

This document is the master instruction set and system prompt for Chronicle's implementation. Any AI agent (Antigravity, Codex), system builder, or developer must read this file before writing a single line of code.

---

## 1. Product Identity

```text
  ┌─────────────────────────────────────────────────────────────────────────┐
  │                            Chronicle is:                                │
  │                 An Engineering Knowledge Memory System                  │
  └─────────────────────────────────────────────────────────────────────────┘
       │
       ├─► [NOT a Chatbot]   ──► We reject floating bubbles and general wrappers.
       │                         Chronicle is a keyboard-driven search engine.
       │
       ├─► [NOT a Wiki]      ──► We do not require developers to write manuals.
       │                         We reconstruct understanding from active work.
       │
       └─► [NOT a Dashboard] ──► We reject decorative, slow, vanity metrics.
                                 Every UI element serves to provide context.
```

Chronicle exists to reduce **Time to Understanding (TTU)**. It turns code repositories, commit histories, PR reviews, Slack channels, and telemetry logs into a unified, queryable causal timeline, allowing developers to reconstruct *why* systems evolved the way they did.

---

## 2. Core Operational Rules

### Rule 1: Evidence Before AI
The synthesis model is never allowed to answer queries without direct, retrieved facts from the database.
* Every statement in an answer must map to a unique citation key (e.g. `[Evidence-1]`).
* If no factual records exist to support an answer, the model must halt and output the safe fallback statement: *"I cannot find any evidence to answer this question based on current records."*

### Rule 2: Absolute Code Realism
No mocks, placeholders, or dummy variables in production code:
* Every button must trigger its complete workflow (UI → API → Service → Database → Response).
* Every database table must enforce Row-Level Security (RLS) and referential integrity constraints.
* Every API endpoint must return the standardized error envelope containing a `requestId`.

---

## 3. Forbidden & Required Behaviors

### Forbidden Behaviors (Instant Rejections)
* **Fake Buttons:** Buttons that open "Coming Soon" alerts or execute no logic.
* **Placeholder Integrations:** Integrations that are only styled UI cards with no OAuth, webhook handling, or sync loops.
* **Hallucinated Answers:** AI responses that cite fake commits, fake authors, or fake files.
* **Dead Screens:** Error states or empty lists that provide no explanation or next steps.

### Required Behaviors (Mandatory Gates)
* **Working End-to-End Workflows:** Features must be completed fully through all database, service, and interface layers.
* **Cryptographic Ingestion:** Git commits and Slack histories must be verified and deduplicated using unique idempotency keys.
* **Distributed Observability:** Every search query, sync job, and agent loop must emit structured trace spans using OpenTelemetry.

---

## 4. Implementation Build Order (00–49 Execution)

The builder must implement Chronicle by executing these spec layers sequentially:

1. **Constitutional Layer (00–07):** Understand *why* Chronicle exists, its product principles, and V1 boundaries.
2. **Business & Strategy Layer (08–17):** Align user personas (Senior Engineer, Manager, New Hire), growth loops, pricing models, and target launch criteria.
3. **Architecture Layer (18–27):** Configure the master topology, database schemas, Causal Graph structures, Timeline Buckets, Memory Engine decay rates, and context compression AST parsers.
4. **Platform Layer (28–42):** Implement the monorepo layout, API routing payloads, design token variables, OAuth auth flows, BullMQ sync workers, testing pyramid benchmarks, and backup procedures.
5. **Product Growth Layer (43–49):** Establish the 5-minute onboarding flow, activation triggers, and the 5-phase execution roadmap.

---

## 5. Golden Rules for the Builder

* **STOP and Ask:** If a requirement is ambiguous or if database schemas clash, STOP. Ask the founder. Do not invent product behavior.
* **Performance is a Feature:** Keep page rendering times under 100ms. Focus on keyboard navigation (`Cmd+K` command palettes, Vim shortcuts, mouse-free layouts).
* **Memory is the Product:** Protect database integrity. Data loss is unacceptable. Daily automated backups and RPO < 1 hour must be configured and tested regularly.

*Read this, absorb it, and obey the constitution.*
