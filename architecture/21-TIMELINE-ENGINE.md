# Timeline Engine

This document defines the architecture, event schema, and zoom logic for the Chronicle Timeline Engine. The Timeline Engine establishes time as the primary organizing axis for software development events.

---

## 1. Event Primitive Schema

Every ingestion source (GitHub webhooks, Slack messages, deployment logs) yields events that are mapped to a unified **Event Primitive**:

```sql
create table chronicle.events (
    id uuid primary key default gen_random_uuid(),
    event_timestamp timestamptz not null,
    event_type text not null,       -- 'commit', 'pr_approve', 'slack_post', 'deploy', 'alert'
    actor_id uuid not null,         -- references user node
    description text not null,      -- short human-readable summary
    metadata jsonb default '{}',    -- original source payload details (e.g. diff size, message length)
    source_id text not null,        -- external identifier (e.g., commit SHA or Slack timestamp)
    created_at timestamptz default now()
);

create index on chronicle.events (event_timestamp desc);
create index on chronicle.events (event_type);
```

---

## 2. Multi-Dimensional Timeline Alignment

The Timeline Engine acts as a time-series database. It aligns activities across different systems by partitioning them into hourly or daily buckets:

```text
    Time Axis   ──► [10:00 AM] ──► [10:15 AM] ──► [10:30 AM] ──► [10:45 AM]
    Slack       ──► #deploy msg      ──►                  ──► #incident thread
    GitHub      ──►                  ──► Commit a3f89e    ──►
    Telemetry   ──►                  ──►                  ──► Alert: Timeout
```

When an incident query is processed, the engine isolates the relevant window (e.g. `[10:00 AM - 10:45 AM]`), overlaying chat events, code changes, and alerts to show their temporal proximity.

---

## 3. Aggregation & Zooming Logic

To prevent UI performance degradation and message overload when viewing long time ranges (e.g., three months), the Timeline Engine applies **Semantic Time Windowing**:

* **Micro-zoom:** Individual events (e.g., every single Slack comment or Git file modification) are shown in detail.
* **Macro-zoom:** Events are collapsed into logical clusters using temporal proximity and entity clustering:
  * 15 commits to repository `payment-service` within 1 hour are represented as: *"15 commits by Sathwik to payment-service"* (Click to expand).
  * Multiple alerts and resolution posts within a 30-minute block are grouped as: *"Incident PaymentGatewayTimeout (Mitigated)"*.

The zoom level is dynamically calculated in the API based on the viewport parameters requested by the keyboard-driven UI, ensuring response payloads remain small and fast to parse.
