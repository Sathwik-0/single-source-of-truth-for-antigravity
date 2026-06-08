# Agent Runtime

This document describes the design, execution loop, sandboxing, and capabilities of the Chronicle Agent Runtime. The Agent Runtime executes complex, multi-step queries that require autonomous code reading or schema analysis.

---

## 1. The Autonomous Agent Loop

For queries that cannot be solved by single-step database retrieval, the system instantiates an ephemeral Agent Loop:

```text
               [ Start Agent Runtime ]
                         │
                         ▼
                     [ Plan ] (Gemini 2.5 Pro sets sub-goals)
                         │
                         ▼
                     [ Action ] (Invokes sandbox tools: Git, DB, Web)
                         │
                         ▼
                   [ Observation ] (Parses execution stdout/errors)
                         │
                         ▼
               { Goal Achieved? } ──[ No ]──► [ Refine Plan ] ──┐
                         │                                      │
                       [ Yes ]                                  │
                         ▼                                      │
             [ Synthesize Response ] ◄──────────────────────────┘
```

---

## 2. Sandbox Security & Isolation

Executing developer-like scripts (e.g., parsing a complex Python configuration or analyzing SQL schemas) requires strict safety constraints:

* **Ephemeral Containers:** All code-execution analysis runs inside isolated, secure, read-only Docker containers with memory limits (max 256MB).
* **Network Isolation:** Sandbox containers have no access to the external internet, preventing data exfiltration.
* **Timeout Limits:** Actions are hard-capped at 5 seconds of execution time to prevent infinite loops or denial-of-service attempts.

---

## 3. Runtime Tool Capabilities

The Agent Runtime is equipped with a restricted set of read-only tools:

* **read_repository_file:** Reads file contents from the checked-out repository.
* **list_code_directory:** Lists the files and directories inside the repository.
* **inspect_database_schema:** Reads table structures, columns, and indexes of the Supabase project database.
* **match_causal_path:** Traces paths in the Causal Graph between code nodes and chat nodes.
