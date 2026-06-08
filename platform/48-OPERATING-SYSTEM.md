# Operating System

This document defines the operating rhythms, decision frameworks, and product development rules for the Chronicle team.

---

## 1. Weekly Rhythm

The company maintains operational alignment through three focused weekly sessions:

### Monday: Product Review
* **Goal:** Align on upcoming feature specs and review UX flows.
* **Checks:** Ensure every planned feature template maps to the core schema (UI → API → Service → Database).
* **Veto:** Any proposed feature that relies on mock interfaces or "coming soon" placeholders is immediately rejected.

### Wednesday: Engineering Review
* **Goal:** Review system performance and query accuracy.
* **Checks:** Audit open-telemetry spans, database partitioning health, and API latency metrics.
* **Review:** Inspect all failed hallucination tests or user-reported answer inaccuracies.

### Friday: Metrics Review
* **Goal:** Review user onboarding, activation, and retention numbers.
* **Checks:** Monitor Time to Understanding (TTU) tracking, citation click-through rates, and daily active user trends.

---

## 2. Decision Framework

Decisions (technical or product) must follow a strict, data-first path. We reject opinions or assumptions that are not backed by evidence:

```text
  Raw Evidence (DB performance, telemetry, user interviews)
       │
       ▼
  Temporal Timeline (Isolating when the problem occurred)
       │
       ▼
  Logical Reasoning (Deconstruct alternative paths and trade-offs)
       │
       ▼
  Approved Decision (Committed to DECISION-LOG.md and database)
```

---

## 3. Product Rule

> **"Every feature must improve understanding, or it does not ship."**

Chronicle exists to resolve context fragmentation and reduce Time to Understanding. If a proposed feature does not directly contribute to:
* Making system context clearer,
* Speeding up query planning or retrieval, or
* Elevating evidence accuracy and trust,

it is bloat. The feature is rejected, and resources are diverted to optimizing core engine latency and fact validation.
