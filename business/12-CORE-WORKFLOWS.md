# Core Workflows

This document maps the technical execution pipelines for Chronicle's primary system flows.

---

## 1. Repository & Slack Onboarding Workflow
When a team connects their workspace, Chronicle bootstraps the memory index via this pipeline:

```text
  Connect GitHub & Slack
           ↓
  [Ingestion Service] (Pulls commits, PRs, diffs, channels, threads)
           ↓
  [Timeline Engine] (Organizes events on a chronological, multi-dimensional axis)
           ↓
  [Causal Graph Engine] (Maps links: Author ↔ Commit ↔ Slack thread ↔ Code modified)
           ↓
  [Memory Indexer] (Extracts semantic topics, terms, and keywords)
           ↓
  [Ready State] (Workspace is queryable)
```

---

## 2. Incident Analysis Workflow
When an engineer queries Chronicle during an incident, the system processes the request as follows:

```text
  Natural Language Query (e.g., "Why is payment database failing?")
           ↓
  [Question Planner] (Deconstructs query into search, code audit, and log queries)
           ↓
  [Hybrid Retrieval] (Pulls code diffs, Slack outage threads, recent commits)
           ↓
  [Evidence Ranking] (Scores evidence: recency, author weight, semantic relevance)
           ↓
  [Timeline Synthesis] (Assembles an interactive event-cause timeline)
           ↓
  [Response Generation] (Presents answer with side-by-side clickable citations)
```

---

## 3. Knowledge Search Workflow
When looking up legacy decisions or architectural choices, Chronicle uses this pipeline:

```text
  User Query (e.g., "Why did we swap JWT for sessions?")
           ↓
  [Memory Lookup] (Locates entity index for "JWT" and "sessions")
           ↓
  [Graph Traversal] (Follows causal links to find the original RFC PR and Slack reviews)
           ↓
  [Evidence Extraction] (Selects the specific debate thread and commit diffs)
           ↓
  [Synthesis Loop] (Gemini 2.5 Pro processes context to draft the response)
           ↓
  [Legible Output] (A decision timeline with original quotes and diffs)
```
