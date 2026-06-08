# Graph Engine

This document details the design, schema, and traversal patterns of the Causal Graph Engine. The Graph Engine maps semantic relationships between developers, commits, slack discussions, tickets, and code symbols.

---

## 1. Node Schema

The Causal Graph represents engineering entities as distinct nodes. The primary node types are:

```text
  [User]          -> An engineer (Github username, Slack ID, email).
  [Commit]        -> A Git commit (hash, author, date, message).
  [PullRequest]   -> A GitHub PR (number, title, author, state, diff).
  [SlackChannel]  -> A public Slack channel (id, name).
  [SlackMessage]  -> A specific Slack post/thread (id, text, timestamp).
  [CodeSymbol]    -> A package, class, function, or database schema (name, path, scope).
  [Incident]      -> A production alert or post-mortem (id, description, status).
```

---

## 2. Edge Schema (Relationships)

Edges connect nodes to represent the causal links of development history. Key relationship types include:

* **AUTHORED:** `User` → `Commit` / `PullRequest` / `SlackMessage`
* **DISCUSSED_IN:** `SlackMessage` → `SlackChannel`
* **MODIFIED:** `Commit` → `CodeSymbol`
* **DEPICTS:** `SlackMessage` → `CodeSymbol` / `Incident` (e.g., discussing a function or an outage)
* **RESOLVES:** `Commit` / `PullRequest` → `Incident`
* **REFS:** `SlackMessage` → `Commit` / `PullRequest` (e.g., pasting a commit link in Slack)

---

## 3. Storage Strategy

To ensure standard SQL compatibility and optimal Supabase query performance, the Causal Graph is implemented using relational tables with foreign key constraints.

```sql
create table chronicle.nodes (
    id uuid primary key default gen_random_uuid(),
    node_type text not null, -- 'user', 'commit', 'slack_message', 'symbol'
    external_id text unique not null, -- e.g., 'sha-a3f89e', 'slack-1283921.22'
    properties jsonb default '{}'::jsonb,
    created_at timestamptz default now()
);

create table chronicle.edges (
    id uuid primary key default gen_random_uuid(),
    source_id uuid references chronicle.nodes(id) on delete cascade,
    target_id uuid references chronicle.nodes(id) on delete cascade,
    relation_type text not null, -- 'authored', 'modified', 'refs'
    properties jsonb default '{}'::jsonb,
    created_at timestamptz default now(),
    unique (source_id, target_id, relation_type)
);

create index on chronicle.edges (source_id);
create index on chronicle.edges (target_id);
create index on chronicle.edges (relation_type);
```

---

## 4. Traversal Logic (Causal Path Reconstruction)

The query engine uses breadth-first search (BFS) or depth-limited search (DLS) over the relational edge index to reconstruct how code changes relate to discussions:

```text
    [CodeSymbol]
          ↑ (modified in)
       [Commit]
          ↑ (associated with)
     [PullRequest]
          ↑ (referenced in)
    [SlackMessage]  <-- This thread contains the debate/rationale!
```

By traversing this path (limit: 4 hops), Chronicle retrieves the exact conversation that explains why the `CodeSymbol` exists, even if the git history contains no descriptive commit messages.
