# Evidence Ranking

This document defines the architecture and ranking factors of the Evidence Ranking Engine. As Chronicle's trust layer, this component ensures that AI models process only verified, high-scoring evidence rather than raw database dumps.

---

## 1. Raw Data Isolation (AI Sees Ranked Evidence First)

In traditional RAG systems, raw search results are dumped directly into the model context, leading to noise, token bloat, and hallucinations. 

Chronicle enforces **Raw Data Isolation**:

```text
       Raw Context Sources (Commits, logs, Slack messages)
                               │
                               ▼
     [Evidence Ranking Engine] (Trust Layer)
       * Scores every item across 5 ranking factors.
       * Discards low-scoring items.
       * Formats results into structured, cited facts.
                               │
                               ▼
               [Ranked & Structured Evidence]
                               │
                               ▼
                  [AI Synthesis Model]
```

The AI model never accesses the raw database or search indices directly. It only operates on the output of the Evidence Ranking Engine.

---

## 2. The Five Ranking Factors

Every retrieved fact is scored from `0.00` to `1.00` based on five factors:

### A. Recency
* Measures the temporal relevance of the event.
* Recent commits or active incident logs are prioritized for operational queries, while older records decay exponentially.

### B. Authority
* Evaluates source trust and domain ownership.
* Code changes authored by the primary code owner carry higher authority.
* Direct documentation files (specs, RFCs) have higher authority than casual Slack threads.

### C. Relevance
* Semantic similarity score calculated using cosine distance of the 3072d Gemini Embeddings against the user query.

### D. Frequency
* Measures how often an entity or issue is mentioned across systems.
* If a code symbol is mentioned multiple times across different Slack threads and git commits within the target time window, its ranking is boosted.

### E. Confidence
* Reflects the reliability of the source tool integration.
* A cryptographic Git commit has a confidence of `1.00`, whereas an unverified third-party log might have a lower confidence score.

---

## 3. The Trust Threshold

The final score is compiled, and any source node scoring below a threshold of `0.50` is pruned. The top-ranked items are formatted with citation anchors (e.g. `[Source-1]`) and passed to the [Context Compression Engine](file:///C:/Users/ADMIN/.gemini/antigravity/scratch/single-source-of-truth-for-antigravity/architecture/26-CONTEXT-COMPRESSION.md).
