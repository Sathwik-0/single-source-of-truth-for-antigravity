# Folder Structure & Monorepo Layout

This document defines the monorepo layout for Chronicle. All developers and AI builders must adhere to this folder structure to maintain architectural integrity.

---

## 1. Monorepo Root Layout

```text
chronicle/
│
├── apps/             # User-facing applications and deployments
├── packages/         # Shared libraries, configurations, and logic
├── services/         # Independent backend microservices
├── agents/           # Specialized agent runtime modules
├── integrations/     # API connectors and webhook listeners
├── infrastructure/   # Supabase, Docker, and Terraform configurations
├── docs/             # Technical specifications and blueprints
└── tests/            # E2E and integration test suites
```

---

## 2. Core Directories

### A. apps/
* **`apps/web/`:** The core Chronicle application (Next.js, TypeScript). Includes the keyboard-driven question interface, timeline engine views, and graph visualization.
* **`apps/admin/`:** Internal administration dashboard for monitoring ingestion sync status, LLM costs, and agent runtime health.
* **`apps/docs-site/`:** Public/internal product documentation, specifications, and user guides.

### B. packages/
* **`packages/ui/`:** Shared React component library (Vanilla CSS tokens).
* **`packages/database/`:** Supabase client, migrations, SQL helper libraries, and database seed scripts.
* **`packages/auth/`:** Shared auth logic (NextAuth/Supabase configurations, JWT validation).
* **`packages/ai/`:** LLM wrappers (Gemini, Groq API interfaces) and prompt template management.
* **`packages/graph/`:** Traversal and node-edge logic for the Causal Graph Engine.
* **`packages/timeline/`:** Event normalizers, time buckets, and semantic zoom controllers.
* **`packages/memory/`:** Indexing logic, semantic vector extraction, and memory decay workers.

### C. services/
* **`services/api/`:** The primary REST & Server-Sent Events (SSE) API Gateway (Node.js/Express).
* **`services/ingestion/`:** Asynchronous ingestion workers (consuming GitHub and Slack webhooks).
* **`services/retrieval/`:** The RAG context builder that pulls data from vector and relational stores.
* **`services/ranking/`:** Implementation of the Evidence Ranking scoring algorithm.

### D. agents/
* **`agents/planner-agent/`:** Deconstructs user queries into retrieval DAG steps.
* **`agents/graph-agent/`:** Analyzes path connections in the Causal Graph.
* **`agents/timeline-agent/`:** Gathers and sequences time-series events.
* **`agents/evidence-agent/`:** Evaluates, scores, and prunes context logs.
* **`agents/summary-agent/`:** Synthesizes final responses.

### E. integrations/
* **`integrations/github/`:** Client wrappers for GitHub REST & GraphQL APIs, Webhooks, and OAuth.
* **`integrations/slack/`:** Client wrappers for Slack Web API and Event subscriptions.
* **`integrations/linear/`:** Client wrappers for Linear API.
* **`integrations/jira/`:** Client wrappers for Jira REST API.
* **`integrations/notion/`:** Client wrappers for Notion API.

### F. infrastructure/
* **`infrastructure/supabase/`:** Local Supabase configuration files and remote migrations.
* **`infrastructure/docker/`:** Ephemeral sandbox environments for the Agent Runtime.
* **`infrastructure/terraform/`:** Cloud deployment states (hosting, VPC, databases).
* **`infrastructure/monitoring/`:** OpenTelemetry and dashboard metrics configurations.
