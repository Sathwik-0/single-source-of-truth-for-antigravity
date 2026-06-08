# Security Specification

This document defines the encryption standards, secret management protocols, audit logging rules, and access control policies for Chronicle.

---

## 1. Authentication & Authorization

### Authentication
* Chronicle V1 relies entirely on federated OAuth providers: **GitHub OAuth** and **Google OAuth**.
* No passwords, passwords hashes, or salt values are stored within the Chronicle database, eliminating credential theft vectors.

### Authorization (Role-Based Access Control)
Workspaces enforce isolation via role scopes:
* **Owner:** Full account access, billing administration, member provisioning, integration access.
* **Admin:** Manage workspace configurations, install integrations, invite users.
* **Member:** Perform queries, search memory, inspect timelines.
* **Viewer:** Read-only access to existing timeline views and synthesized answers.

---

## 2. Encryption Standards

### Data in Transit
* All network traffic is encrypted using **TLS 1.3** (with TLS 1.2 as a fallback for legacy clients).
* Connections attempting insecure HTTP are redirected to HTTPS.

### Data at Rest
* Database storage disks are encrypted using **AES-256**.
* API access tokens and integration credentials (e.g., GitHub tokens, Slack keys) are encrypted before database insertion using **AES-256 GCM** with a rotating master key.

---

## 3. Secret Management

> **"Secrets are never stored in code."**

* No API keys, database passwords, or private tokens may be hardcoded or checked into the Git repository.
* Secrets are injected at runtime via environment variables configured in:
  * **Vercel Console** (for frontend serverless functions).
  * **Supabase Vault** (for database functions).
  * **GitHub Secrets** (for CI/CD deployment pipelines).

---

## 4. Audit Logging

To ensure security compliance (SOC2 readiness), the database maintains an immutable log of critical actions:

```sql
create table chronicle.audit_logs (
    id uuid primary key default gen_random_uuid(),
    actor_id uuid references chronicle.users(id),
    action_type text not null, -- 'login', 'permission_change', 'integration_install', 'workspace_delete'
    ip_address text,
    user_agent text,
    metadata jsonb default '{}'::jsonb,
    created_at timestamptz default now()
);
```

### Monitored Events
* Successful and failed user logins.
* Workspace role adjustments and member additions.
* Integration authorization and token rotation actions.
* Workspace deletions or billing plan modifications.

---

## 5. Security Reviews

The engineering team must execute a **Quarterly Security Audit**:
* Review and rotate API tokens and credentials.
* Inspect dependency vulnerability logs (Snyk / Dependabot reports).
* Audit Postgres Row-Level Security (RLS) policies to ensure tenant boundary integrity.
