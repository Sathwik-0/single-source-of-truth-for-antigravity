# Integration Architecture

This document defines how external platforms connect to Chronicle and how raw payloads are transformed into structured, verifiable evidence sources.

---

## 1. Integrations as Evidence Sources

Most applications treat integrations as simple data connectors. Chronicle treats integrations as **verifiable evidence sources**. 

Every ingested file, commit, message, or issue must contain cryptographic proof or immutable references back to its origin. If a piece of data cannot be traced back to a specific user, timestamp, and integration event, it cannot be used as evidence for AI synthesis.

---

## 2. GitHub Integration Architecture

### OAuth Flow
```text
  User Initiates Connect
           │
           ▼
   GitHub OAuth Screen (Requires scopes: repo, read:org, user:email)
           │
           ▼
   OAuth Access Token Returned
           │
           ▼
   AES-256 GCM Encrypted Storage (Saved in database schema)
           │
           ▼
   Trigger Initial Sync Job (Backfill task queued)
```

### Data Sources Ingested
* **Codebases:** Repositories, branches, tags, file trees, and code diff boundaries.
* **Collaboration:** Pull Requests, PR reviews, PR comments, Issues, and Issue comments.
* **Metadata:** Commits, commit authors, releases, and repository tags.

### Webhook Events Handled
Chronicle registers organization-level webhooks for real-time sync:
* `push` (Track commit and branch updates)
* `pull_request` (Track PR reviews, comments, and approvals)
* `issues` & `issue_comment` (Track tasks and discussions)
* `release` (Track code deployments and milestones)

---

## 3. Slack Integration Architecture

### OAuth Flow
* **Workspace Install:** Enterprise/Workspace OAuth flow to install the Chronicle Bot.
* **Token Storage:** Encrypted storage of Bot tokens (`xoxb-...`) in `chronicle.integrations`.
* **Channel Discovery:** Automatically scan and list public channels to sync.

### Data Sources Ingested
* **Communication:** Public channels, user messages, threaded replies, and message reactions (e.g. tracking checkmarks or thumbs up).
* **Identity:** Workspace user lists, Slack profiles, and avatars.

---

## 4. Integration Mapping Layer

To ensure that different tools can be compared on the same timelines and graphs, the Mapping Layer converts raw payloads into standardized Chronicle objects:

* **GitHub Commit → Chronicle Event:** Converts commit messages and diff metadata into a timeline event.
* **Slack Message → Chronicle Event:** Normalizes channel posts and threads into temporal conversation events.
* **GitHub PR Review → Chronicle Evidence:** Converts reviews and approvals into verified evidence nodes in the Causal Graph.

---

## 5. Failure Handling & Resilience

* **Rate Limits:** Ingestion queues dynamically back off when receiving `429 Too Many Requests` or `X-RateLimit-Remaining` headers from GitHub/Slack.
* **Expired Tokens:** Trigger automated user alerts and disable the integration state in the database, requesting re-authentication via the UI.
* **Webhook Failures:** Retries webhooks with exponential backoff. If a webhook is permanently missed, the hourly **Scheduled Sync** catches the missing events.
