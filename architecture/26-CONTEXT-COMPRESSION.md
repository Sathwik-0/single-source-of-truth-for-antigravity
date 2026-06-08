# Context Compression

This document details the strategies and algorithms used to compress raw codebase and conversational data into high-density context packages, maximizing retrieval accuracy and minimizing model latency.

---

## 1. Abstract Syntax Tree (AST) Truncation

When injecting code files into the LLM context, raw file text is often bloated with implementation details that are irrelevant to structural queries. 

The Context Compression Engine applies AST-based pruning:
* **Signature-Only Parsing:** If a file is retrieved as secondary context, the engine parses the code and strips function bodies, keeping only class declarations, import statements, and function/method signatures.
* **Relevant block extraction:** If a specific function is matched semantically, the engine extracts *only* that function block and its immediate caller hierarchy, discarding the rest of the file.

```text
    Raw Code File (1,200 lines)
                 ↓
    [AST Parser] (TypeScript/Python/Go AST)
                 ↓
    Compressed Interface Map (60 lines)
    - imports
    - class definitions
    - relevant method body (e.g. `validateSession`)
    - all other method bodies replaced with: `// [body truncated for context density]`
```

---

## 2. Conversation De-noising

Slack logs contain conversational headers, greetings, emojis, and typos that waste token space. The engine cleans chat strings before RAG injection:
* **Banter Removal:** Strips lines that match common conversational filler (e.g., "Good morning team," "checking on this now").
* **Thread Consolidation:** Merges consecutive messages from the same user within a 2-minute window into a single text block, removing redundant timestamps and usernames.
* **Reaction Mapping:** Translates reactions (e.g. thumbs up, fire, checkmark) into explicit semantic markers (e.g., `[Approved by @user]`), preserving context while saving token space.

---

## 3. Incremental RAG Pipeline

Instead of dumping all retrieved events into a single LLM prompt, Chronicle implements a multi-stage refinement loop:
* **Stage 1 (Local Synthesis):** Gemini 2.5 Flash compiles and summarizes individual Slack threads and code diffs into localized summary nodes in parallel.
* **Stage 2 (Global Synthesis):** Gemini 2.5 Pro reads the consolidated summary nodes and the primary source code to draft the final response.

This hierarchical approach reduces context token payload by up to **80%**, while retaining 100% of critical decision events.
