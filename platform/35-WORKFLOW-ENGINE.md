# Workflow Engine

This document defines Chronicle's core execution workflows. It details the step-by-step logic and component interactions for our primary system actions.

---

## 1. Workflow 1: Question Answering (Query to Synthesis)

This pipeline processes a developer query to generate a cited, verified answer:

```text
       User Question
             │
             ▼
     [Question Planner] ──► Deconstructs intent; determines required engines
             │
             ▼
   [Evidence Retrieval] ──► Pulls files, commits, and logs based on plan
             │
             ▼
     [Timeline Engine]  ──► Aligns retrieved events chronologically
             │
             ▼
      [Graph Engine]    ──► Maps relationships to trace causality
             │
             ▼
    [Evidence Ranking]  ──► Scores and filters out low-signal context
             │
             ▼
       [Gemini Pro]     ──► Synthesizes final response strictly using context
             │
             ▼
     [Grounded Answer]  ──► Streams answer with side-by-side citations to UI
```

---

## 2. Workflow 2: Repository Onboarding

This pipeline bootstraps a new GitHub repository connection:

```text
  1. Connect GitHub  ──► Authenticate via OAuth; store encrypted tokens.
  2. Discover Repos  ──► Query GitHub API to list all user repositories.
  3. Backfill Data   ──► Ingestion Queue pulls commits, PRs, and issues.
  4. Create Timeline ──► Order all backfilled entities chronologically.
  5. Create Graph    ──► Populate graph nodes and author/dependency edges.
  6. Create Memory   ──► Run semantic indexer to extract concept embeddings.
```

---

## 3. Workflow 3: Incident Reconstruction

This workflow is triggered during production outages to compile causal histories:

```text
  1. Incident Question ──► User asks: "Why did the database crash yesterday?"
  2. Find Events       ──► Pull telemetry alerts, deploys, and commits.
  3. Build Timeline    ──► Align events inside the outage time window.
  4. Build Causal Chain──► Graph Engine traces links (Alert ↔ Deploy ↔ Commit).
  5. Generate Report   ──► Synthesizes post-mortem report detailing root cause.
```

---

## 4. Workflow 4: Memory Creation

This background pipeline converts fresh ingestion data into long-term memories:

```text
  1. New Evidence ──► Webhook ingests Slack thread or code PR.
  2. Scoring      ──► Evaluate initial Importance Score (I) from 1 to 10.
  3. Memory Engine──► Summarize conversations, strip noise, and extract terms.
  4. Storage      ──► Save summary and 3072d vector embeddings to Supabase.
```

---

## 5. Workflow 5: Evidence Ranking

This workflow filters and formats candidate context nodes before AI ingestion:

```text
  Retrieved Context (Raw nodes)
              │
              ▼
    Evaluate Parameters (Recency, Authority, Relevance, Frequency, Confidence)
              │
              ▼
    Calculate Score ($Score = Relevance + Recency + Author + Frequency + Confidence$)
              │
              ▼
    Apply Threshold (Prune any node scoring < 0.50)
              │
              ▼
    Grounded Set (Generate citation indexes and format for synthesis model)
```
