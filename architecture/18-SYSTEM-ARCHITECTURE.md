# System Architecture

This document describes the global architecture, data flow, component boundaries, and runtime stack of Chronicle. 

---

## 1. Global Topology

Chronicle is built as a modular system that bridges asynchronous ingestion pipelines with a real-time, evidence-based query engine.

```text
                  [ External Sources: GitHub & Slack Webhooks ]
                                       ↓
                               [ Ingestion Engine ]
                                       ↓
                               [ Supabase Stack ]
                  ┌────────────────────┴────────────────────┐
                  ▼                                         ▼
            [ Postgres DB ]                         [ pgvector Storage ]
            * Causal Graph                          * Context Embeddings (3072d)
            * Timelines & Events                    * Raw Semantic Text
                  ▲                                         ▲
                  └────────────────────┬────────────────────┘
                                       ▼
                              [ Query Processor ]
                        ┌──────────────┴──────────────┐
                        ▼                             ▼
               [ Question Planner ]          [ Evidence Ranking ]
                        │                             │
                        ▼                             ▼
                 [ Gemini Flash ]               [ Gemini Pro ]
                   (Low-latency)                (Deep Reasoning)
                        └──────────────┬──────────────┘
                                       ▼
                            [ Keyboard-Driven UI ]
                             (Perplexity-like)
```

---

## 2. Ingestion & Event Pipeline
* **Event Ingestion:** Webhooks capture commits, PR activities, code diffs from GitHub, and channel posts, threads, reactions from Slack.
* **Normalization Service:** Normalizes disparate raw payloads (e.g., a GitHub commit JSON and a Slack message JSON) into a standard **Event Primitive** (schema defined in `21-TIMELINE-ENGINE.md`).
* **Graph Builder:** Takes the normalized event, extracts entities, and writes nodes and edges to the relational **Causal Graph** (schema defined in `20-GRAPH-ENGINE.md`).
* **Embedding Service:** Generates a 3072-dimensional vector embedding of the event payload using the Gemini Embedding API and stores it in `chronicle.embeddings`.

---

## 3. Query & Synthesis Pipeline
When a developer enters a natural language question in the UI:
1. **Question Planning:** The [Question Planner](file:///C:/Users/ADMIN/.gemini/antigravity/scratch/single-source-of-truth-for-antigravity/architecture/23-QUESTION-PLANNER.md) parses the query, plans the retrieval steps, and determines which databases, code symbols, or time ranges must be queried.
2. **Context Retrieval:** Queries the relational database (for timelines and causal graphs) and the vector database (for semantic concepts).
3. **Evidence Ranking:** The [Evidence Ranking Engine](file:///C:/Users/ADMIN/.gemini/antigravity/scratch/single-source-of-truth-for-antigravity/architecture/25-EVIDENCE-RANKING.md) scores all retrieved context chunks by recency, authority, and connectivity, pruning low-scoring noise.
4. **Context Compression:** The [Context Compression Engine](file:///C:/Users/ADMIN/.gemini/antigravity/scratch/single-source-of-truth-for-antigravity/architecture/26-CONTEXT-COMPRESSION.md) fits the ranked facts into the LLM context window using code truncation and chat noise filtering.
5. **Synthesis & Citation:** Gemini 2.5 Pro synthesizes the final response, strictly bounding its answer to the cited evidence (enforced by the [Hallucination Prevention Engine](file:///C:/Users/ADMIN/.gemini/antigravity/scratch/single-source-of-truth-for-antigravity/architecture/27-HALLUCINATION-PREVENTION.md)).
6. **Streaming Delivery:** The response is streamed to the UI via Server-Sent Events (SSE) along with side-by-side clickable citations.

---

## 4. Runtime Stack
* **Database & Hosting:** Supabase (Postgres, pgvector, Edge Functions, Auth).
* **AI Engine:** Google Gemini API (Gemini 2.5 Pro for deep reasoning, Gemini 2.5 Flash for indexing/tagging, Groq for low-latency routing).
* **Application Services:** Node.js/TypeScript backend service deployed as serverless functions.
* **Frontend:** Next.js/React with Vanilla CSS, optimized for sub-100ms keyboard-driven navigation.
