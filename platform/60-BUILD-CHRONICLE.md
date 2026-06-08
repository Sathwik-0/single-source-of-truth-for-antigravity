# The Master Execution Blueprint

This document is the master prompt and operational directive for Chronicle’s implementation. The builder (AI agent or human developer) must read this blueprint before writing a single line of code in the code repository.

---

## 1. Product Identity

* **An Engineering Knowledge Memory System:** Chronicle is built exclusively to reconstruct engineering understanding.
* **NOT a Chatbot:** No floating chat widgets or generic wrappers.
* **NOT a Wiki:** No manual document creation required; we index active work byproducts.
* **NOT a Dashboard:** No useless charts, graphs, or vanity metrics.

---

## 2. Core Operational Constraints

* **Evidence Before AI:** The AI model is never allowed to synthesize answers without cited facts. If no evidence exists, output: *"I cannot find any evidence to answer this question based on current records."*
* **Absolute Code Realism:** Mocks, placeholders, dummy functions, and mock success states are strictly forbidden. Every button, API route, and table integration must be fully functional.

---

## 3. Input Sources

The builder must refer to and utilize two sources of truth to guide implementation:
1. **The Constitution Repository:** This repository (`single-source-of-truth-for-antigravity`), containing documents `00` through `60`.
2. **Supabase Memory:** The `chronicle` configuration tables containing active decisions, principles, engines, prompts, and registries.

*Note: All development must occur inside a separate code repository (e.g. `chronicle`). Never write code files directly inside the constitution repository.*

---

## 4. Incremental Build Directive

The codebase must be built sequentially following the **Milestone Roadmap** (`52-MILESTONE-CHECKLISTS.md`):

1. **Authentication:** NextAuth configuration, GitHub/Google OAuth, session tokens, and workspace RBAC.
2. **Database:** Supabase Postgres schemas, partitions, indexes, pgvector configurations, and RLS policies.
3. **GitHub Integration:** OAuth connector, webhook endpoints, and BullMQ historical backfills.
4. **Slack Integration:** Bot installer and message thread listener.
5. **Timeline Engine:** Normalized event primitives and time-series clustering.
6. **Graph Engine:** Relational graph nodes, edges, and causal path recursive SQL traversal.
7. **Memory Engine:** Gemini Flash entity extraction and pgvector indexing.
8. **Question Planner:** Natural language parser and execution DAG.
9. **Evidence Ranking:** Mathematical implementation of the 5-factor scoring model.
10. **Answer Engine:** Gemini 2.5 Pro synthesis with citation verification.
11. **Frontend UI:** Keyboard-driven web application dashboard, command palette, and split-screen Evidence Panel.
12. **Billing:** Value-based quota systems.
13. **Observability:** OpenTelemetry distributed tracing, Fastify JSON logging, and alert triggers.

---

## 5. Required File Execution Deliverables

Every implemented feature must deliver:
* **UI:** Fully styled and interactive frontend component.
* **API:** Validated Fastify backend endpoint (returning trace-enabled error envelopes if failing).
* **Service:** Structured TypeScript business logic.
* **Database:** Real table modifications, indexes, and RLS policies.
* **Loading & Error States:** Skeletal rendering and recovery views.
* **Telemetry:** JSON log emitters and metrics recorders.
* **Tests:** Unit, integration, contract, or E2E tests validating the feature.

---

## 6. Safe Fallback & Triggers

If requirements are ambiguous, if database constraints clash, or if a test fails:
* **STOP.**
* **Read the specifications (00-60).**
* **Read the Supabase decisions memory.**
* **Ask the founder.**
* **Never invent product behavior.**

---

## 7. The Chronicle Mission

* *Reduce Time to Understanding.*
* *Preserve organizational memory.*
* *Reconstruct decisions.*
* *Earn trust.*

**Understanding is the product.**
