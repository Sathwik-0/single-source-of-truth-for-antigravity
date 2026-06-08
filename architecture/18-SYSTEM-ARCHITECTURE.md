# System Architecture

This document defines the global architecture, data flow, and runtime components of Chronicle. It explains exactly what happens from the moment a user asks a question to the moment an answer is returned.

---

## 1. The Request Lifecycle (User Asks Question)

When a developer inputs a query into the keyboard-driven Chronicle UI, it flows through the system in this sequence:

```text
       [ User Question ]
               │
               ▼
      [ Question Planner ] (1. Deconstructs intent; decides engines to run/integrations to query)
               │
               ▼
       [ Ingestion/RAG ] (2. Dynamically retrieves raw logs, commits, threads, and text)
               │
               ▼
      [ Timeline Engine ] (3. Aligns events on a temporal, multi-dimensional axis)
               │
               ▼
       [ Graph Engine ] (4. Maps relationships to build a causal understanding of the timeline)
               │
               ▼
      [ Evidence Ranking ] (5. Filters and scores context, presenting only high-confidence facts)
               │
               ▼
      [ Context Compressor ] (6. Compresses the ranked events to the 5 absolute key facts)
               │
               ▼
       [ Gemini / Groq ] (7. Synthesizes the final answer using primary reasoning models)
               │
               ▼
       [ Verified Answer ] (8. Streams results to the UI with side-by-side citations)
```

---

## 2. Core Components

* **Frontend:** Keyboard-first, command-driven interface (Perplexity + Linear + GitHub + Cursor feel) designed for sub-100ms rendering.
* **API Gateway:** Routes incoming search queries, manages real-time socket connections for streaming, and validates user authentication.
* **Question Planner:** The coordinator model (routed via **Groq** for speed) that identifies the query intent and decides which data sources to query.
* **Agent Runtime:** Spawns specialized agents (Research, Timeline, Graph, Evidence, Summarization) to collaborate on multi-step context retrieval.
* **Memory Engine:** Translates raw event history into short-term, long-term, workspace, and conversation memories.
* **Timeline Engine:** Orders all files, logs, commits, messages, and incidents by time to enable historical reconstruction.
* **Graph Engine:** Constructs the Causal Graph, connecting entities (People, Code, Issues) to trace why events happened.
* **Evidence Ranking:** Chronicle’s trust layer, ensuring the AI model only sees highly ranked, scored facts rather than raw database dumps.
* **Supabase Database:** Relational and vector storage, holding the Causal Graph, timelines, logs, and metadata.

---

## 3. Technology Stack

* **Primary Reasoning:** Gemini 2.5 Pro (long-context reasoning, synthesis, deep code analysis).
* **Fast Operations:** Gemini 2.5 Flash (indexing, entity extraction, planning).
* **Router Layer:** Groq (low-latency routing, fast planning, streaming token delivery).
* **Persistence & Vector Search:** Supabase / PostgreSQL (utilizing pgvector for 3072d Gemini Embeddings, RLS for workspace isolation, and table partitioning).
