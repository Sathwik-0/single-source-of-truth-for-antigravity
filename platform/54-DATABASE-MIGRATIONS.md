# Database Migrations

This document defines the schema migration order and rules for database updates in the Chronicle project.

---

## 1. Migration Sequence

Migrations are executed in numerical sequence. No migration file may modify a table structure defined in an earlier file. If schema updates are required, they must be implemented as a new, incremental migration.

```text
  supabase/migrations/
  ├── 001_users.sql
  ├── 002_organizations.sql
  ├── 003_repositories.sql
  ├── 004_events.sql
  ├── 005_evidence.sql
  ├── 006_timelines.sql
  ├── 007_memories.sql
  ├── 008_questions.sql
  ├── 009_answers.sql
  └── 010_integrations.sql
```

---

## 2. Migration Contents & Targets

* **`001_users.sql`:** Creates the base `chronicle.users` table, unique indexes on `email`, `github_username`, `slack_user_id`, and triggers for updating `updated_at`.
* **`002_organizations.sql`:** Creates the `chronicle.organizations` table and the `user_organizations` join table for team membership and RBAC tracking.
* **`003_repositories.sql`:** Defines code repositories, indexing on `organization_id` and unique repository names.
* **`004_events.sql`:** Sets up the partitioned `events` table (partitioned on `event_timestamp`), including trigger scripts to auto-create monthly partition tables.
* **`005_evidence.sql`:** Establishes context scoring fields, authority weights, and validation references.
* **`006_timelines.sql`:** Creates views and tables to query chronological indices by time filter.
* **`007_memories.sql`:** Schema for short-term and long-term memory categories, including decay-rate coefficients.
* **`008_questions.sql`:** Logs user search activities, planner configurations, and trace tokens.
* **`009_answers.sql`:** Captures synthesized output, citations, and relation mappings to questions.
* **`010_integrations.sql`:** Stores integration credentials, OAuth webhook configs, and maps vector embeddings.

---

## 3. Migration Rules

* **Never Edit Old Migrations:** Once a migration has been committed and pushed to `develop` or `main`, it is immutable. Changing old migration files is strictly forbidden.
* **Rollback-Testing Required:** Every migration SQL file must be accompanied by a validation test showing it can be rolled back cleanly (e.g. `supabase/migrations/001_users_rollback.sql` or verifying `up.sql` and `down.sql` scripts locally).
* **Idempotent Statements:** Use clauses like `create table if not exists` and `alter table add column if not exists` to ensure scripts execute safely on all deployment targets.
* **Enable RLS on Create:** Every table creation script must explicitly execute:
  ```sql
  alter table chronicle.{table_name} enable row level security;
  ```
