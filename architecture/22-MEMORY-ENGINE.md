# Memory Engine

This document defines how memory is organized, scored, retained, decayed, and recalled within Chronicle. The Memory Engine manages four distinct tiers of memory to ensure that developers get context-appropriate answers.

---

## 1. Memory Tiers

```text
  ┌─────────────────────────────────────────────────────────────────────────┐
  │                           Chronicle Memory                              │
  └─────────────────────────────────────────────────────────────────────────┘
       │
       ├─► [Conversation Memory] ──► Active chat history and user context.
       │                             (Expires when session closes)
       │
       ├─► [Short-Term Memory]   ──► Temporary context of recent events (deploys,
       │                             commits, active incidents in the last 48h).
       │
       ├─► [Workspace Memory]    ──► Current codebase structure, active specs,
       │                             and organization configuration rules.
       │
       └─► [Long-Term Memory]    ──► Synthesized historical decisions, old post-
                                     mortems, and core architectural changes.
```

* **Conversation Memory:** Holds active session state, current filters, and immediate context of the current developer conversation.
* **Short-Term Memory:** Indexes recent activity logs, webhook payloads, and active discussion threads. Key focus is high-resolution chronological detail.
* **Workspace Memory:** Indexes repository directories, code symbol structures, database configurations, and active integrations.
* **Long-Term Memory:** Retains high-level synthesized summaries of completed incidents, historical decisions, architecture diagrams, and system evolution.

---

## 2. Memory Scoring, Decay, and Retention

To prevent database bloat and maintain relevant vector search, every memory node is assigned an **Importance Score** ($I$) from 1 to 10 (e.g., a simple commit is $I = 2$, a post-mortem is $I = 9$).

### Temporal Decay
The relevance of short-term memories decays over time using an exponential decay function:

$$Relevance(t) = I \times e^{-\lambda \cdot t}$$

Where:
* $I$ = Initial Importance score.
* $\lambda$ = Decay rate (higher for informal conversations, lower for repository commits).
* $t$ = Time elapsed since creation.

### Retention Rules
* If $Relevance(t)$ drops below a threshold (e.g. `0.20`), the memory node is scheduled for pruning or consolidation.
* High-importance memories (e.g., decisions, post-mortems) do not decay completely and are automatically archived into **Long-Term Memory**.

---

## 3. Recall Pipeline

When a developer submits a query, the Memory Engine retrieves context through a hybrid recall process:

1. **Active Session Recall:** Pulls active conversation memories to understand pronouns and context (e.g. *"What did sathwik say about **it**?"*).
2. **Semantic Recall:** Performs vector cosine similarity matching across the `chronicle.embeddings` table (using the 3072d embedding of the query).
3. **Temporal Recall:** Pulls un-decayed short-term memories within the temporal window determined by the Question Planner.
4. **Graph Recall:** Traverses the Causal Graph to fetch connected long-term memories related to the matched entities.
