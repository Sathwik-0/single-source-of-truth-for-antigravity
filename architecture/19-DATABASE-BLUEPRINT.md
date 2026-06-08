# Database Blueprint

This document defines the complete SQL schema, relationships, indexes, and constraints for the `chronicle` database schema on Supabase.

---

## 1. Schema Initialization

```sql
create schema if not exists chronicle;
create extension if not exists vector;
```

---

## 2. Table Definitions

### A. documents
Tracks the 50 canonical governance and blueprint files in the repository.
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

### B. decisions
Stores permanent architectural and product decisions.
```sql
create table chronicle.decisions (
    id uuid primary key default gen_random_uuid(),
    title text not null,
    rationale text not null,
    consequences text,
    status text not null default 'approved',
    created_at timestamptz default now()
);
```

### C. principles
Core design and building values.
```sql
create table chronicle.principles (
    id uuid primary key default gen_random_uuid(),
    category text not null,
    principle text not null,
    source_document text not null,
    created_at timestamptz default now()
);
```

### D. engines
Core component systems of the Chronicle stack.
```sql
create table chronicle.engines (
    id uuid primary key default gen_random_uuid(),
    name text unique not null,
    purpose text not null,
    dependencies jsonb default '[]'::jsonb,
    status text not null default 'planned',
    created_at timestamptz default now()
);
```

### E. integrations
External tools and phase specifications.
```sql
create table chronicle.integrations (
    id uuid primary key default gen_random_uuid(),
    name text unique not null,
    provider text not null,
    phase text not null,
    status text not null default 'planned',
    oauth boolean default false,
    webhooks boolean default false,
    notes text
);
```

### F. features
Track specific product capabilities and requirements.
```sql
create table chronicle.features (
    id uuid primary key default gen_random_uuid(),
    name text not null,
    phase text not null,
    status text not null default 'planned',
    owner text,
    dependencies jsonb default '[]'::jsonb
);
```

### G. workflows
Logical execution pipelines.
```sql
create table chronicle.workflows (
    id uuid primary key default gen_random_uuid(),
    name text unique not null,
    trigger text not null,
    description text not null,
    status text not null default 'planned'
);
```

### H. prompts
Stores prompt templates (e.g., Golden Spoon Prompt) used by the LLM layer.
```sql
create table chronicle.prompts (
    id uuid primary key default gen_random_uuid(),
    name text unique not null,
    purpose text not null,
    content text not null
);
```

### I. memories
Synthesized memory nodes extracted from discussions and logs.
```sql
create table chronicle.memories (
    id uuid primary key default gen_random_uuid(),
    category text not null,
    content text not null,
    importance integer default 1,
    source text not null,
    created_at timestamptz default now()
);
```

### J. document_registry
Tracks the completion status of the 50-document blueprint.
```sql
create table chronicle.document_registry (
    number integer primary key,
    filename text not null,
    section text not null,
    completed boolean default false
);
```

### K. embeddings
Stores vector representations for semantic search.
```sql
create table chronicle.embeddings (
    id uuid primary key default gen_random_uuid(),
    source_type text not null, -- e.g., 'slack_message', 'git_commit', 'document'
    source_id uuid not null,   -- foreign key pointing to source table
    content text not null,     -- raw source text
    embedding vector(3072)     -- 3072 dimensions match Gemini Embeddings
);
```

---

## 3. Indexes & Constraints

To ensure sub-100ms querying and fast vector similarity matching:
```sql
-- Indexes for vector similarity search using cosine distance
create index on chronicle.embeddings using hnsw (embedding vector_cosine_ops);

-- Indexing source_type for fast filtering before vector lookup
create index on chronicle.embeddings (source_type);

-- Standard indexes for performance
create index on chronicle.documents (number);
create index on chronicle.memories (category);
create index on chronicle.document_registry (completed);
```

---

## 4. Row Level Security (RLS)

Every table under the `chronicle` schema must enable RLS to ensure workspace isolation.
```sql
alter table chronicle.documents enable row level security;
alter table chronicle.decisions enable row level security;
alter table chronicle.principles enable row level security;
alter table chronicle.embeddings enable row level security;

-- Example policy for workspace containment:
-- create policy workspace_isolation_policy on chronicle.documents
--     for all using (workspace_id = auth.uid());
```
 Aurora and Postgres optimizations like partitioning are deferred until V3.
