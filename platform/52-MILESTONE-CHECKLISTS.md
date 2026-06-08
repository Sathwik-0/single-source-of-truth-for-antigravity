# Milestone Checklists

This document defines the completion criteria for the five core engineering milestones of the Chronicle application.

---

## Milestone 1: Authentication & Workspace Setup
* **Purpose:** Establish secure user sessions and workspace containment.
* **Requirements:**
  * GitHub & Google OAuth connections.
  * Session token creation, rotation, and deletion.
  * Organization and Workspace creation database schemas.
  * Role-Based Access Control (RBAC) middleware (Owner, Admin, Member, Viewer).
* **Milestone Checklist:**
  * `[ ]` UI: Login screen and workspace selector.
  * `[ ]` API: `/api/auth` session routes.
  * `[ ]` DB: `users`, `organizations`, `user_organizations` tables.
  * `[ ]` Tests: Unit tests for RBAC permissions and session token expiration.
  * `[ ]` Error Handling: Rejects invalid tokens and unauthorized routes.
  * `[ ]` Loading States: Clear skeletal animations during OAuth redirect loops.

---

## Milestone 2: GitHub Integration
* **Purpose:** Index code repositories and capture active development webhooks.
* **Requirements:**
  * Repository discovery and token installation storage.
  * Incremental and scheduled historical backfills (commits, PRs, issues).
  * Webhook listener endpoints (`push`, `pull_request`).
  * Ingestion queue retry backoff handling.
* **Milestone Checklist:**
  * `[ ]` UI: Integrations configuration dashboard.
  * `[ ]` API: `/api/integrations/github` configuration routes and webhook endpoints.
  * `[ ]` DB: `repositories`, `events` (partitioned), and `integrations` tables.
  * `[ ]` Tests: Mock git pushes and verify BullMQ retry flows.

---

## Milestone 3: Slack Integration
* **Purpose:** Capture team conversations and threads.
* **Requirements:**
  * Slack bot installation flow.
  * Public channel message and thread ingestion.
  * User profile mapping.
* **Milestone Checklist:**
  * `[ ]` UI: Slack connect button and channel whitelist selector.
  * `[ ]` API: `/api/integrations/slack` endpoints.
  * `[ ]` DB: Slack channel and message events mapped to `chronicle.events`.
  * `[ ]` Tests: Verify threaded message linking and emoji reaction indexing.

---

## Milestone 4: Timeline & Graph Engines
* **Purpose:** Align events by time and establish causal links.
* **Requirements:**
  * Normalization of GitHub and Slack records into Event Primitives.
  * Causal Graph node-edge relation structures.
  * Graph traversal SQL functions.
* **Milestone Checklist:**
  * `[ ]` UI: Interactive timeline view and split-screen Evidence Panel.
  * `[ ]` API: `/api/timeline/{entityId}`.
  * `[ ]` DB: `graph_nodes`, `graph_edges`, and `timeline_events` schemas.
  * `[ ]` Tests: Traverse graph to trace a code line back to its Slack thread.

---

## Milestone 5: Question Answering (QA Synthesis)
* **Purpose:** Answer natural language developer queries using evidence.
* **Requirements:**
  * Question Planner DAG generation.
  * Context Retrieval and Evidence Ranking.
  * Gemini 2.5 Pro synthesis with citation verification loops.
* **Milestone Checklist:**
  * `[ ]` UI: Search box, citation preview, and Answer Card rendering.
  * `[ ]` API: `/api/questions` streaming response endpoint (SSE).
  * `[ ]` DB: `questions` and `answers` logging.
  * `[ ]` Tests: Hallucination benchmarks (`No evidence = No answer`).
