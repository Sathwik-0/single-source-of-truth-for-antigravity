# Graph Engine

This document defines the schema, nodes, edges, and causal reasoning mechanisms of Chronicle's Graph Engine. The Graph Engine structures the connections between engineering actions and conversations to enable causal reconstruction of code history.

---

## 1. Graph Node Schema

Chronicle's Causal Graph is composed of eight core Node Types:

```text
  [People]        -> Individual developers (GitHub username, Slack ID, emails).
  [Repositories]  -> Codebase repositories containing files and revision history.
  [Commits]       -> Git revisions (hashes, logs, authorship, file diff boundaries).
  [PullRequests]  -> GitHub pull requests containing reviews, approvals, and threads.
  [Issues]        -> Task tracking records (GitHub issues, Linear/Jira tasks).
  [Incidents]     -> Telemetry alerts, error events, outage periods, and post-mortems.
  [Decisions]     -> Approved architectural choices, RFCs, and decision log nodes.
  [Questions]     -> Logs of developer queries to map user context.
```

---

## 2. Graph Edge Schema (Relationships)

Edges connect nodes to represent logical and semantic causality:

* **AUTHORED:** `People` → `Commits` / `PullRequests` / `SlackMessage` / `Questions` (traces direct creation).
* **REVIEWED:** `People` → `PullRequests` (identifies domain experts and approvals).
* **REFERENCES:** `SlackMessage` / `Decisions` / `Commits` → any node (e.g., a commit message referencing a PR, a Slack link to a code file, or a Decision citing a benchmark).
* **CAUSED:** `Commits` / `Decisions` → `Incidents` (maps what change broke the system).
* **FIXED:** `Commits` / `PullRequests` → `Incidents` / `Issues` (traces mitigation history).
* **DISCUSSED:** `People` / `SlackMessage` → `Decisions` / `Incidents` (maps the debate, rationale, and context surrounding an action).

---

## 3. Relational Storage Schema

To ensure scalability and fast query latency, nodes and edges are stored using indexing patterns in Postgres:

```sql
create table chronicle.graph_nodes (
    id uuid primary key default gen_random_uuid(),
    organization_id uuid not null,
    node_label text not null, -- 'people', 'repositories', 'commits', 'pr', 'issue', 'incident', 'decision'
    external_key text unique not null, -- e.g., 'git-sha-7235e4f', 'slack-msg-1283921'
    properties jsonb default '{}'::jsonb,
    created_at timestamptz default now()
);

create table chronicle.graph_edges (
    id uuid primary key default gen_random_uuid(),
    organization_id uuid not null,
    source_id uuid references chronicle.graph_nodes(id) on delete cascade,
    target_id uuid references chronicle.graph_nodes(id) on delete cascade,
    relation_type text not null, -- 'authored', 'reviewed', 'references', 'caused', 'fixed', 'discussed'
    properties jsonb default '{}'::jsonb,
    created_at timestamptz default now(),
    unique (source_id, target_id, relation_type)
);

create index on chronicle.graph_edges (source_id);
create index on chronicle.graph_edges (target_id);
create index on chronicle.graph_edges (relation_type);
```

---

## 4. Causal Path Reconstruction (Traversal)

The Graph Engine reconstructs *causality* by executing path-traversal queries. When an incident is analyzed, the engine runs recursive queries (max 4 depth levels) to bridge telemetry changes with human intent:

```text
    [Incident: PaymentGatewayTimeout]
                   ▲
                   │ (CAUSED by)
               [Commit: a3f89e (timeout config)]
                   ▲
                   │ (AUTHORED by)
               [People: @sathwik]
                   ▲
                   │ (DISCUSSED in)
          [SlackMessage: thread discussing DB congestion]
```

This causal trail is processed by the [Evidence Ranking Engine](file:///C:/Users/ADMIN/.gemini/antigravity/scratch/single-source-of-truth-for-antigravity/architecture/25-EVIDENCE-RANKING.md) to generate the structural citations for the final answer.
