# Database Blueprint

This document defines the complete database schema, tables, relationships, indexing strategies, partitioning plans, and security controls for Chronicle.

---

## 1. Schema Initialization & Vector Extensions

```sql
create schema if not exists chronicle;
create extension if not exists vector;
```

---

## 2. Table Definitions & Schemas

### A. users
Stores developer profile data across platforms.
```sql
create table chronicle.users (
    id uuid primary key default gen_random_uuid(),
    email text unique not null,
    github_username text unique,
    slack_user_id text unique,
    name text not null,
    avatar_url text,
    created_at timestamptz default now(),
    updated_at timestamptz default now()
);
```

### B. organizations
High-level team containment layer.
```sql
create table chronicle.organizations (
    id uuid primary key default gen_random_uuid(),
    name text unique not null,
    slug text unique not null,
    billing_plan text not null default 'free',
    created_at timestamptz default now(),
    updated_at timestamptz default now()
);
```

### C. repositories
Tracked codebases within organizations.
```sql
create table chronicle.repositories (
    id uuid primary key default gen_random_uuid(),
    organization_id uuid references chronicle.organizations(id) on delete cascade,
    name text not null,
    full_name text unique not null,
    html_url text not null,
    is_private boolean default false,
    default_branch text not null default 'main',
    created_at timestamptz default now()
);
```

### D. events (Partitioned by Time)
The core log of all activities (commits, PRs, Slack posts, deployments, incidents).
```sql
create table chronicle.events (
    id uuid default gen_random_uuid(),
    event_timestamp timestamptz not null,
    event_type text not null, -- 'commit', 'pr', 'slack_message', 'incident', 'deploy'
    organization_id uuid not null,
    actor_id uuid references chronicle.users(id),
    description text not null,
    metadata jsonb default '{}'::jsonb,
    source_id text not null,
    created_at timestamptz default now(),
    primary key (id, event_timestamp)
) partition by range (event_timestamp);
```

### E. timelines
Consolidated chronological views matching user search contexts.
```sql
create table chronicle.timelines (
    id uuid primary key default gen_random_uuid(),
    organization_id uuid references chronicle.organizations(id) on delete cascade,
    name text not null,
    start_time timestamptz not null,
    end_time timestamptz not null,
    event_filters jsonb default '{}'::jsonb,
    created_at timestamptz default now()
);
```

### F. memories
Synthesized knowledge nodes with decay parameters.
```sql
create table chronicle.memories (
    id uuid primary key default gen_random_uuid(),
    organization_id uuid references chronicle.organizations(id) on delete cascade,
    category text not null, -- 'short_term', 'long_term', 'workspace', 'conversation'
    content text not null,
    importance integer not null default 1,
    decay_rate double precision not null default 0.1,
    source text not null,
    created_at timestamptz default now()
);
```

### G. evidence
Ranked, verified facts selected for AI context injection.
```sql
create table chronicle.evidence (
    id uuid primary key default gen_random_uuid(),
    memory_id uuid references chronicle.memories(id) on delete cascade,
    source_type text not null, -- 'commit', 'slack_message', 'document'
    source_id text not null,
    content text not null,
    confidence_score double precision not null,
    authority_score double precision not null,
    recency_score double precision not null,
    created_at timestamptz default now()
);
```

### H. questions
Logs of user queries entered into Chronicle.
```sql
create table chronicle.questions (
    id uuid primary key default gen_random_uuid(),
    user_id uuid references chronicle.users(id),
    organization_id uuid references chronicle.organizations(id) on delete cascade,
    query_text text not null,
    planner_steps jsonb not null default '[]'::jsonb,
    created_at timestamptz default now()
);
```

### I. answers
Synthesized answers matching questions with corresponding evidence mappings.
```sql
create table chronicle.answers (
    id uuid primary key default gen_random_uuid(),
    question_id uuid references chronicle.questions(id) on delete cascade,
    answer_text text not null,
    confidence_score double precision not null,
    evidence_ids uuid[] not null, -- array of chronicle.evidence(id)
    created_at timestamptz default now()
);
```

### J. documents
Tracks the canonical files within the Chronicle system.
```sql
create table chronicle.documents (
    id uuid primary key default gen_random_uuid(),
    number integer not null,
    title text not null,
    path text unique not null,
    section text not null,
    status text not null default 'draft',
    version integer default 1,
    created_at timestamptz default now(),
    updated_at timestamptz default now()
);
```

### K. integrations
Credentials, configurations, and phase states.
```sql
create table chronicle.integrations (
    id uuid primary key default gen_random_uuid(),
    organization_id uuid references chronicle.organizations(id) on delete cascade,
    name text not null,
    provider text not null, -- 'github', 'slack', 'linear', 'notion'
    phase text not null default 'v1',
    status text not null default 'inactive',
    oauth_config jsonb default '{}'::jsonb,
    webhook_secret text,
    created_at timestamptz default now()
);
```

### L. embeddings
Vector store for similarity search.
```sql
create table chronicle.embeddings (
    id uuid primary key default gen_random_uuid(),
    organization_id uuid references chronicle.organizations(id) on delete cascade,
    source_type text not null, -- 'slack_message', 'git_commit', 'document'
    source_id uuid not null,
    content text not null,
    embedding vector(3072) -- 3072d for Gemini Embeddings
);
```

---

## 3. Relationships & Referential Integrity

* **Containment:** `users` map to `organizations` via a many-to-many join table `user_organizations` (not shown for brevity).
* **Ingestion:** `events`, `repositories`, `timelines`, `memories`, `questions`, `integrations`, and `embeddings` are strictly isolated under `organizations.id`.
* **Citations:** `answers` map directly back to their parent `questions` and are bound to specific `evidence` arrays.

---

## 4. Performance, Partitioning & Vector Search

### Table Partitioning
* The `events` table is partitioned dynamically by range on `event_timestamp`. Partition tables are created monthly (e.g., `events_y2026m06` for June 2026) to maintain query performance as millions of events accumulate.

### Indexes & Vector Search
```sql
-- HNSW index for high-performance vector cosine distance
create index on chronicle.embeddings using hnsw (embedding vector_cosine_ops);

-- Partitioned indexes for temporal query efficiency
create index on chronicle.events (event_timestamp desc, event_type);
create index on chronicle.events (organization_id);

-- Speed up relational traversal
create index on chronicle.evidence (memory_id);
create index on chronicle.answers (question_id);
```

---

## 5. Security & Row-Level Security (RLS)

All tables must enable RLS. No query is allowed to bypass the tenant-isolation filter:

```sql
alter table chronicle.events enable row level security;
alter table chronicle.memories enable row level security;
alter table chronicle.embeddings enable row level security;

-- Example Tenant-Isolation Policy
create policy tenant_isolation_policy on chronicle.events
    for all using (organization_id = (select current_setting('request.jwt.claim.org_id', true))::uuid);
```
