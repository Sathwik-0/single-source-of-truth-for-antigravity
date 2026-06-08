# Developer Bootstrap Guide

This document defines the transition protocol between the specification repository and the actual code development repository.

---

## 1. Bridging the Silos

```text
   Constitutional Repository
   (single-source-of-truth-for-antigravity)
   * Contains documents 00-60
   * Defines the rules, schemas, prompts, and metrics
                       │
                       ▼  (Read & Inherit Specs)
   [ Implementation Builder (AI / Human) ]
                       │
                       ▼  (Code Execution)
   Chronicle Code Repository
   (chronicle)
   * The actual Next.js, Fastify, and database codebase
   * Executed under the rules of the Constitution
```

---

## 2. Builder Responsibilities

* **Read 00–60 First:** The builder must read, parse, and commit to memory all 60 specifications before generating directories or files in the code repository.
* **Inspect Supabase Memory:** Read the active configuration keys (principles, decisions, engine configurations) in the Supabase workspace.
* **Create a Separate `chronicle` Repository:** All actual code files must live in a separate repository (e.g. `chronicle`).
* **Build Incrementally:** Follow the strict build order. Never attempt to code multiple modules in parallel.
* **Never Modify the Constitution Repository:** The constitution repository is the read-only Single Source of Truth (SSOT). Code changes are never committed here.

---

## 3. Incremental Build Order

The actual codebase must be constructed in this exact sequence:

1. **Authentication:** Set up GitHub and Google OAuth, workspaces, and session logic.
2. **Database:** Provision schema, tables, indexes, and RLS policies in Supabase.
3. **GitHub Integration:** Implement OAuth connector, webhook receiver, and data backfill sync.
4. **Slack Integration:** Implement bot workspace installer and thread channel listener.
5. **Timeline Engine:** Build event normalization parser and time bucketing queries.
6. **Graph Engine:** Implement causal graph nodes, edges, and recursive traversal SQL.
7. **Memory Engine:** Integrate entity extraction via Gemini Flash and pgvector embeddings.
8. **Question Planner:** Implement query parser and DAG executor.
9. **Evidence Ranking:** Code the 5-factor relevance/recency/authority scoring model.
10. **Answer Engine:** Implement Gemini 2.5 Pro synthesis and post-generation citation check.
11. **Frontend:** Build keyboard-driven dashboard UI, evidence panels, and Cmd+K command palettes.
12. **Billing:** Connect Stripe or value-based organization quota rules.
13. **Observability:** Integrate OpenTelemetry spans, Fastify JSON logging, and alerting triggers.
