# Authentication & Permissions

This document defines the security models, role structures, permission matrices, and integration scopes required to protect Chronicle's workspaces and prevent cross-tenant data leaks.

---

## 1. Authentication Strategy

Chronicle V1 relies exclusively on OAuth federated identity. To minimize surface vulnerability, local password storage is rejected:

* **Primary Providers:** GitHub OAuth & Google OAuth (Managed via Supabase Auth / NextAuth).
* **Workspace Containment Model:**
  ```text
    [Organization]
          └── [Workspace]
                 └── [Users] (Assigned specific roles within workspace scope)
  ```

---

## 2. Role Structure

Users are assigned one of four workspace roles:

* **Owner:** The workspace creator. Has complete authority over billing, workspace deletion, integration configurations, and membership lists.
* **Admin:** Workspace administrator. Can manage integrations, invite/remove users, and view usage and cost metrics.
* **Member:** Standard team developer. Can submit questions, search memory, and inspect timelines.
* **Viewer:** Read-only partner (e.g. guest, auditor, product manager). Can view timelines and answers, but cannot submit new searches or execute query planner tasks.

---

## 3. Permission Matrix

| Operation | Owner | Admin | Member | Viewer |
| :--- | :---: | :---: | :---: | :---: |
| **Question Asking** | ✅ | ✅ | ✅ | ❌ |
| **Timeline Viewing** | ✅ | ✅ | ✅ | ✅ |
| **Graph Viewing** | ✅ | ✅ | ✅ | ✅ |
| **Integration Setup** | ✅ | ✅ | ❌ | ❌ |
| **Workspace Settings**| ✅ | ✅ | ❌ | ❌ |
| **Billing & Deletion**| ✅ | ❌ | ❌ | ❌ |

---

## 4. OAuth Scopes

Integrations must request the minimum scopes required to index necessary event primitives:

### GitHub Scope
* `repo` (Allows cloning private/public repositories to read code structures).
* `read:org` (Retrieves organization user lists and profile mappings).
* `user:email` (Associates commit authors with user profiles).

### Slack Scope
* `channels:history` (Allows indexing messages and threads in public channels).
* `users:read` (Matches Slack user IDs with user profiles).
* `team:read` (Validates workspace ID boundaries).
