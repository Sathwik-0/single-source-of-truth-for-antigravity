# Evidence Ranking

This document outlines the scoring model, parameters, and ranking algorithm used to filter retrieved context before presenting it to the synthesis model.

---

## 1. The Scoring Model

To prevent context window bloat and eliminate irrelevant search results, retrieved resources (commits, Slack posts, documentation) are scored using a multi-factor formula:

$$Score = (Relevance \times w_1) + (Recency \times w_2) + (AuthorWeight \times w_3) + (GraphConnectivity \times w_4)$$

---

## 2. Score Components

* **Relevance ($w_1 = 0.40$):** Semantic similarity score calculated by cosine distance from the 3072d vector lookup.
* **Recency ($w_2 = 0.25$):** Exponential decay function based on the time elapsed since the event occurred:
  $$Decay = e^{-\lambda \cdot t}$$
  *Recent changes are weighted higher, as they are more likely to contain the cause of bugs or current design intents.*
* **Author Weight ($w_3 = 0.15$):** Evaluates user domain authority:
  * If the query is about repository `A`, commits written by the primary maintainer of `A` are prioritized.
  * If a Slack user actively participated in an incident thread, their posts carry more weight for that incident context.
* **Graph Connectivity ($w_4 = 0.20$):** High connectivity nodes in the Causal Graph (e.g., a Slack message linked to both a resolved PR and a closed ticket) receive an authority boost.

---

## 3. Thresholding & Pruning

* **Minimum Score:** Any retrieved resource with a final score below `0.45` is discarded immediately.
* **Context Budgeting:** The top-ranked items are filled into the context window until the token budget (e.g., 50,000 tokens for optimal latency) is exhausted.
* **Citation Guarantee:** Every piece of evidence selected for LLM synthesis is assigned a unique, incremental citation ID (e.g., `[Evidence-1]`) that must map directly to its database entry.
