# Infrastructure Stack Specification

This document defines the technology stack, components, and primary dependency rules for the Chronicle application.

---

## 1. Core Stack Specification

### Frontend Layer
* **Framework:** Next.js (App Router, React server components).
* **Language:** TypeScript.
* **Styling:** Tailwind CSS (utility layout rules) + Vanilla CSS (custom design system tokens).
* **Components:** shadcn/ui (primitives for accessible modals, dropdowns, and buttons).

### Backend Services
* **Runtime:** Node.js (v20+ LTS).
* **Language:** TypeScript.
* **Framework:** Fastify (Chosen for high throughput, low latency routing, and native JSON schema validation).

### Database & Storage
* **Engine:** Supabase PostgreSQL (Managed Postgres).
* **Requirements:**
  * Strict Row-Level Security (RLS) policies.
  * `pgvector` extension (3072-dimensional vector support).
  * Full-Text Search configuration (Postgres `tsvector` and `tsquery` for keyword fallbacks).
* **Storage:** Supabase Storage (S3-compatible bucket storing exports, static document images, and attachments).

### Queue Layer
* **Broker:** Upstash Redis (Serverless Redis instance).
* **Library:** BullMQ (Provides robust job scheduling, retries, and parent-child dependency tracking for ingestion).

### Artificial Intelligence Layer
* **Primary Reasoning:** Gemini 2.5 Pro (long-context processing, multi-source synthesis, and codebase reasoning).
* **Fast Operations / Planner:** Gemini 2.5 Flash (real-time indexing, chat de-noising, and query deconstruction).
* **Model Router:** Groq (low-latency routing and plan DAG generation).

### Telemetry & Monitoring
* **Distributed Tracing:** OpenTelemetry SDK (traces request spans across Fastify, database, and LLM services).
* **Product Analytics:** PostHog (measures user retention, search frequency, and citation CTR).
* **Error Tracking:** Sentry (captures backend exceptions and frontend hydration issues).

---

## 2. Infrastructure Dependency Rule

> **"Every dependency must justify its existence."**

Chronicle rejects bloated npm dependency trees:
* No external packages are allowed for features that can be implemented cleanly with native platform APIs (e.g. using standard `fetch` instead of `axios`).
* External UI libraries must be vetted for bundle size impact before installation.
* Every third-party library introduced must be reviewed, documented, and approved in the architecture checklist.
