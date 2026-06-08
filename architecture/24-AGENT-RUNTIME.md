# Agent Runtime

This document describes the design, roles, and collaboration patterns of the Chronicle Agent Runtime. The Agent Runtime coordinates specialized AI agents to solve complex query tasks.

---

## 1. Agent Inventory

The runtime coordinates five specialized agents, each holding a narrow set of tools and responsibilities:

```text
  ┌───────────────────┐     ┌───────────────────┐     ┌───────────────────┐
  │  Timeline Agent   │     │    Graph Agent    │     │  Research Agent   │
  ├───────────────────┤     ├───────────────────┤     ├───────────────────┤
  │ Indexes, clusters,│     │ Traces causality  │     │ Performs code     │
  │ & sequences events│     │ across entities   │     │ reading and AST   │
  │ chronologically   │     │ and tools         │     │ analysis          │
  └─────────┬─────────┘     └─────────┬─────────┘     └─────────┬─────────┘
            │                         │                         │
            └─────────────────────────┼─────────────────────────┘
                                      ▼
                            ┌───────────────────┐
                            │  Evidence Agent   │
                            ├───────────────────┤
                            │ Filters, scores & │
                            │ validates facts   │
                            └─────────┬─────────┘
                                      │
                                      ▼
                            ┌───────────────────┐
                            │Summarization Agent│
                            ├───────────────────┤
                            │ Synthesizes the   │
                            │ final response    │
                            └───────────────────┘
```

* **Research Agent:** Inspects code repositories, analyzes file structures, and parses code syntax trees to locate specific changes.
* **Timeline Agent:** Gathers temporal events from various integrations, handles sequence clustering, and aligns events chronologically.
* **Graph Agent:** Traverses the Causal Graph, identifying connections between commits, users, Slack channels, and decisions.
* **Evidence Agent:** Evaluates and scores context using the [Evidence Ranking Engine](file:///C:/Users/ADMIN/.gemini/antigravity/scratch/single-source-of-truth-for-antigravity/architecture/25-EVIDENCE-RANKING.md), verifying the validity of citations.
* **Summarization Agent:** Takes the highly compressed, ranked facts and drafts the final answer, ensuring strict grounding.

---

## 2. Collaboration Protocol

When a multi-step query is processed (e.g., *"Why did the deployment fail, and what was discussed in Slack about it?"*), the agents collaborate through a structured pipeline:

1. **Task Delegation:** The Question Planner instantiates the runtime and delegates sub-tasks to the agents.
2. **Parallel Retrieval:**
   * The **Timeline Agent** aggregates all events around the deployment timestamp.
   * The **Research Agent** analyzes the codebase diff of the failed deployment.
   * The **Graph Agent** finds Slack threads linked to the developer who triggered the deployment.
3. **Consolidation:** The agents send their retrieved facts to the **Evidence Agent**, which filters out irrelevant information and assigns authority scores.
4. **Final Synthesis:** The **Summarization Agent** receives the ranked evidence, resolves any conflicts, and streams the grounded answer back to the user.
5. **Session Teardown:** The ephemeral container environments are destroyed, preserving only the session context.
