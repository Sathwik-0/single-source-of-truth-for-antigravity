# V1 Scope

This document defines the strict scope boundaries for the First Version (V1) of Chronicle. Anything not listed here is out of scope and deferred to future versions.

---

## 1. Integrations
V1 will connect to exactly two external sources:
* **GitHub:** Ingesting Pull Requests, commits, repository file structures, and code diffs.
* **Slack:** Ingesting messages, threads, and user channels.

*Note: Integrations must be fully functional, supporting secure OAuth, real-time webhooks, incremental data sync, and robust error recovery before they are marked complete.*

---

## 2. Core Engines
V1 must implement four primary backend engines:
* **Timeline Engine:** Collects and orders all events (commits, Slack messages, deployments) chronologically.
* **Memory Engine:** Automatically parses, indexes, and associates keywords, entities, and topics across code and conversation.
* **Evidence Ranking:** A scoring algorithm that evaluates source relevance, recency, and author authority to rank retrieved context.
* **Question Planner:** Deconstructs a user's natural language question into a structured plan of retrieval and synthesis steps.

---

## 3. Search & Interface
* **Evidence-Backed Answers:** The main user interface must display synthesized answers alongside raw, side-by-side evidence panels (e.g., highlighting specific code blocks or displaying the exact Slack chat quote).
* **Keyboard-Driven Navigation:** Complete command palette (`Cmd+K`) functionality to search, navigate, and trigger commands without a mouse.
* **Fast UI:** Sub-100ms response times for core UI rendering.

---

## 4. Excluded (Out of Scope for V1)
To ensure execution focus, the following features are explicitly excluded from V1:
* **Linear / Jira Integration:** Deferred.
* **Notion / Google Drive / Confluence Integration:** Deferred.
* **Microsoft Teams / GitLab Integration:** Deferred.
* **Enterprise Features:** Single Sign-On (SSO), role-based access control (RBAC), multi-region hosting, audit logs, and custom billing models are excluded.
* **Multi-Tenant Spaces:** Only single-workspace support in V1.
