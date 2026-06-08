# Question Planner

This document defines the architecture, processing steps, and execution guidelines of the Question Planner. The Question Planner prevents AI hallucinations by forcing structured analysis before answering.

---

## 1. The Planning Phase (Before AI Answers)

Chronicle never submits a developer's question directly to the synthesis model. Instead, it enters a structured **Planning Phase** where it deconstructs the query to decide exactly how to retrieve the required facts.

```text
                             [ Natural Language Query ]
                                         │
                                         ▼
                            [ Question Planner Engine ]
                                         │
       ┌──────────────────────────┬──────┴───────────────────┬──────────────────────────┐
       ▼                          ▼                          ▼                          ▼
[Identify Intent]        [Determine Evidence]       [Select Engines]           [Query Integrations]
* What is being asked?   * Commits, code, chat?     * Timeline, Graph,         * GitHub API, Slack
* Extract time window.   * Extract keywords.          or Vector memory?          history, or local DB?
       │                          │                          │                          │
       └──────────────────────────┼──────────────────────────┼──────────────────────────┘
                                  ▼
                        [ Execution Plan DAG ]
                                  │
                                  ▼
                         [ Context Retrieval ]
```

---

## 2. Planning Decoupling

The planner evaluates four critical questions:

### A. What is being asked?
* Classifies the query type (e.g., *Causal debug*, *Code explanation*, *Decision audit*, *Ownership verification*).
* Identifies implicit temporal markers (e.g., "last deployment", "yesterday", "when we setup auth").

### B. What evidence is needed?
* Determines the target entities: repositories, code files, directories, Slack channels, users, or tickets.
* Extracts query tokens to perform exact matches and vector lookups.

### C. Which engines should run?
* **Timeline Engine:** Runs if the query asks about chronological sequences or recent events.
* **Graph Engine:** Runs if the query requires tracing causality across tools (e.g. connecting a Slack post to a git commit).
* **Memory Engine:** Runs to retrieve high-level consolidated decisions or long-term design concepts.

### D. Which integrations should be queried?
* Decides if the engine must fetch live, real-time data from external APIs (e.g., checking GitHub PR reviews or Slack threads) or rely on cached database indexes.

---

## 3. Prevention of Hallucinations

By decoupling planning from synthesis, Chronicle ensures that the final AI answer is strictly bounded by retrieved evidence. If the Question Planner finds that no evidence exists for the requested time window or entity, it halts execution and triggers a safe fallback response immediately, bypassing the synthesis model completely and avoiding potential hallucinations.
