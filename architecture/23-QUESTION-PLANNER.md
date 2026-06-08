# Question Planner

This document defines the architecture, query deconstruction, and model routing strategy of the Question Planner. The Question Planner translates natural language engineering questions into structured execution plans.

---

## 1. Query Deconstruction

Developers ask questions with implicit context and temporal constraints:
* *"Why did we revert the gateway migration last Friday?"*

The Question Planner deconstructs this query into a Directed Acyclic Graph (DAG) of search and retrieval operations:

```text
                                 [ User Query ]
                                       ↓
                           [ Gemini 2.5 Flash Parser ]
                   ┌───────────────────┼───────────────────┐
                   ▼                   ▼                   ▼
           [ Time Window ]     [ Entity Target ]     [ Action Verb ]
          "Last Friday" (UTC)    "gateway", "migration"  "revert"
                   │                   │                   │
                   └───────────────────┼───────────────────┘
                                       ▼
                             Generated Plan DAG:
                             Step 1: Get git commits matching "gateway" & "migration"
                             Step 2: Get Slack threads in #deploy matching "revert"
                             Step 3: Retrieve code diffs from Step 1 commits
```

---

## 2. Plan Execution & Model Routing

To optimize latency and token expense, tasks are split between models:

* **Router Layer (Groq):** Analyzes the raw query latency requirements and routes the request to either Flash or Pro.
* **Extraction & Planning (Gemini 2.5 Flash):** Extract entities, time ranges, and build the retrieval DAG.
* **Retrieval Execution:** Runs the SQL and vector queries sequentially based on the DAG dependencies.
* **Synthesis & Summary (Gemini 2.5 Pro):** Reads the retrieved codebase files, git diffs, and Slack logs to write the final, citation-rich answer.

---

## 3. Execution Schema

The generated plan is represented as JSON and executed by the backend runtime:

```json
{
  "query": "Why did we revert the gateway migration last Friday?",
  "plan": {
    "steps": [
      {
        "id": "fetch_commits",
        "action": "git_search",
        "params": {
          "term": "gateway migration",
          "start_time": "2026-06-05T00:00:00Z",
          "end_time": "2026-06-05T23:59:59Z"
        }
      },
      {
        "id": "fetch_chat",
        "action": "slack_search",
        "params": {
          "term": "revert gateway",
          "start_time": "2026-06-05T00:00:00Z",
          "end_time": "2026-06-05T23:59:59Z"
        }
      }
    ]
  }
}
```
This structured plan guarantees that we perform precise context gathering before invoking our synthesis model.
