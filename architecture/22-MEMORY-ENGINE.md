# Memory Engine

This document outlines the architecture, entity extraction pipeline, and consolidation cycles for the Chronicle Memory Engine. The Memory Engine is responsible for translating raw event history into high-level, queryable conceptual understanding.

---

## 1. Entity Extraction Pipeline

When new text data (Slack chat threads, Pull Request review comments, commit descriptions) is ingested, the Memory Engine processes it to extract engineering entities and map dependencies.

```text
       Raw Chat / Code Comment text
                     ↓
      [Gemini 2.5 Flash Parser] (Fast classification & entity extraction)
                     ↓
      Extracted Entities:
      * Code Symbols: class names, functions, API routes (e.g. `PaymentController`)
      * Infrastructure: DB tables, caches, queues (e.g. `Redis`, `users_table`)
      * Business Context: features, customer IDs, projects (e.g. `OAuth Setup`)
                     ↓
      [Alias Resolution] (Resolving acronyms and synonyms to canonical keys)
                     ↓
      Saved to chronicle.memories & chronicle.embeddings
```

---

## 2. Alias Resolution (The Entity Synonym Graph)

Engineers use multiple informal terms to refer to the same service or database.
* Acronyms: `pg`, `postgres`, `payments-db` all point to the Postgres database instance.
* Slack handles: `@sathwik`, `Sathwik-0`, `sathwik` all resolve to a single `User` node.

The Memory Engine maintains an **Alias Mapping Table** that normalizes terms to their canonical nodes in the Causal Graph before performing search operations, preventing vector retrieval fragmentation.

---

## 3. Memory Consolidation Cycle (Background Workers)

Over time, raw event databases become cluttered with conversational noise ("Good morning," "Thanks," emojis). To maintain high signal-to-noise ratios, the Memory Engine runs a **Consolidation Worker** (cron job) every 24 hours:

* **De-noising:** Filters chat chatter that has no technical references or code symbol mentions.
* **Summarization:** Groups multiple Slack messages related to a specific incident thread and runs Gemini 2.5 Flash to write a concise **incident summary memory** (importance: 5, category: 'incidents').
* **Pruning:** Archive older, low-importance vector embeddings to ensure vector search latency remains sub-100ms.
* **Lineage Tracking:** Resolves code changes to updated architecture versions, maintaining a clean history of decisions.
