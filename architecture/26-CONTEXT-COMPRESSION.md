# Context Compression

This document defines the compression pipeline that condenses millions of raw engineering events into a few key facts, protecting model latency and accuracy without losing historical meaning.

---

## 1. The Compression Funnel

Chronicle processes massive amounts of organizational data through a multi-stage funnel:

```text
       [ 10 Million Ingested Events ] (Total database size)
                      │
                      ▼  (Step 1: Question Planner filters by time & entity)
       [ 500 Candidate Events ]
                      │
                      ▼  (Step 2: Evidence Ranking applies trust scoring & pruning)
       [ 50 Ranked Events ]
                      │
                      ▼  (Step 3: Context Compression extracts ASTs & de-noises text)
       [ 5 Key Grounded Facts ]
                      │
                      ▼  (Step 4: AI Synthesis Engine drafts the response)
           [ Synthesized Answer ]
```

---

## 2. Compression Stages

### Stage 1: Retrieval (10M → 500 Events)
* The [Question Planner](file:///C:/Users/ADMIN/.gemini/antigravity/scratch/single-source-of-truth-for-antigravity/architecture/23-QUESTION-PLANNER.md) establishes precise boundaries (e.g., target repositories, specific Slack channels, and a 24-hour time window).
* This discards 99.9% of irrelevant history, yielding a maximum of 500 candidate event nodes.

### Stage 2: Ranking (500 → 50 Events)
* The [Evidence Ranking Engine](file:///C:/Users/ADMIN/.gemini/antigravity/scratch/single-source-of-truth-for-antigravity/architecture/25-EVIDENCE-RANKING.md) evaluates the 500 candidates across the 5 ranking factors (Recency, Authority, Relevance, Frequency, Confidence).
* Nodes scoring below `0.50` are pruned, leaving the top 50 highest-signal events.

### Stage 3: Extraction (50 → 5 Key Facts)
* The Context Compression Engine processes the top 50 events:
  * **AST Truncation:** Code files are stripped of irrelevant method bodies, keeping only class structures and the specific lines changed.
  * **Slack De-noising:** Conversation threads are stripped of greetings, filler words, and formatting bloat.
* The engine extracts the **5 key facts** (e.g., specific code diff, exact deployment failure alert, key developer discussion quote) and presents them to the synthesis model.

---

## 3. Preserving Semantic Meaning

Unlike standard token truncation (which cuts off text arbitrarily), Chronicle's compression is semantic. By utilizing AST parsers and thread summarizers, the system guarantees that structural dependencies and the original developer intents are preserved, ensuring highly accurate answers.
