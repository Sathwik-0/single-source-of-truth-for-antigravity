# Timeline Engine

This document outlines the design, event model, and chronological alignment processes of Chronicle's Timeline Engine. The Timeline Engine turns every activity across tools into a standardized event, allowing developers to reconstruct code history.

---

## 1. The Unified Event Primitive

The Timeline Engine treats every piece of data as a temporal event. The core primitive maps events to a common format:

```sql
create table chronicle.timeline_events (
    id uuid default gen_random_uuid(),
    event_timestamp timestamptz not null,
    event_type text not null,       -- 'commit', 'pr', 'issue', 'slack_message', 'incident', 'deploy'
    organization_id uuid not null,
    actor_id uuid references chronicle.users(id),
    title text not null,            -- e.g., "PR #14 Approved", "Alert: PaymentTimeout"
    body text,                      -- raw text or description
    source_url text,                -- link to the source tool
    metadata jsonb default '{}',    -- raw tool payloads (diff sizes, tags, statuses)
    primary key (id, event_timestamp)
) partition by range (event_timestamp);
```

---

## 2. Event Normalization

As webhooks trigger, disparate data payloads are immediately transformed into event primitives:

* **Commit Event:** Maps a Git commit (SHA, timestamp, file diffs, author details) to a `commit` event.
* **PR Event:** Maps a Pull Request lifecycle event (opened, reviewed, approved, merged) to a `pr` event.
* **Issue Event:** Maps task changes (created, moved, closed) in GitHub Issues, Linear, or Jira to an `issue` event.
* **Slack Message Event:** Maps conversations, replies, and reactions to a `slack_message` event.
* **Incident Event:** Maps alerts from Sentry, Datadog, or manual logs to an `incident` event.
* **Deployment Event:** Maps CI/CD run milestones to a `deploy` event.

---

## 3. Chronological Reconstruction Flow

When a developer asks *"What happened during the database outage last Tuesday?"*, the Timeline Engine runs a chronological query over the target time window:

```text
  [Tuesday 10:00 AM] ──► Event: deploy          (Build #204 deployed)
  [Tuesday 10:05 AM] ──► Event: incident        (Alert: DB Connection Timeout spikes)
  [Tuesday 10:06 AM] ──► Event: slack_message   (@sathwik: "Did we change DB credentials?")
  [Tuesday 10:10 AM] ──► Event: slack_message   (@manager: "Reverting build #204")
  [Tuesday 10:15 AM] ──► Event: deploy          (Revert deploy successful)
  [Tuesday 10:18 AM] ──► Event: incident        (DB Connection Timeout resolved)
```

By ordering these events by time, Chronicle reconstructs the timeline of an incident or decision. The UI renders this as a dense, keyboard-navigable timeline, allowing developers to step forward and backward through history.
