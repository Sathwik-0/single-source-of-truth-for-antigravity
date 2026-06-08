# Sync Engine

This document defines the architecture, queues, and concurrency rules of the Chronicle Ingestion Sync Engine.

---

## 1. Core Principle: No Data Loss

Chronicle is built on the absolute requirement of data integrity. Missing code commits, deleted Slack threads, or dropped webhooks will break the causal graph, leading to incorrect synthesis. Every event must be processed, tracked, and stored reliably.

---

## 2. Sync Modalities

```text
       ┌───────────────────┐     ┌───────────────────┐     ┌───────────────────┐
       │ Initial Backfill  │     │ Incremental Sync  │     │  Scheduled Sync   │
       ├───────────────────┤     ├───────────────────┤     ├───────────────────┤
       │ Runs on connect.  │     │ Triggers via      │     │ Runs hourly.      │
       │ Pulls historical  │     │ webhooks. Queues  │     │ Fallback to catch │
       │ data, indexes DB. │     │ & updates graph.  │     │ missed events.    │
       └───────────────────┘     └───────────────────┘     └───────────────────┘
```

### Initial Backfill
* Triggered immediately when an integration (GitHub or Slack) is connected.
* Fetches the last 30 days of commits, pull requests, and chat logs.
* Runs asynchronously using background workers to prevent blocking user queries.
* Normalizes, ranks, and indexes all retrieved items.

### Incremental Sync
* Webhook triggers when an action occurs (e.g., commit pushed, Slack message posted).
* Event payload is validated, queued immediately, and processed by workers.
* The Causal Graph and vector indexes are updated in real-time.

### Scheduled Sync (Fallback protection)
* A cron worker executes an hourly polling job for all active integrations.
* Compares the latest local event timestamps against the remote GitHub/Slack state to identify and retrieve any missed events (mitigating webhook delivery failures).

---

## 3. Queue Design

To manage traffic spikes and ensure reliability under heavy load, ingestion is structured across three queues:

```text
  Webhook / API Event ──► [ Ingestion Queue ] ──(Success)──► Database / Graph
                                 │
                              (Fail)
                                 ▼
                          [ Retry Queue ]  (1m, 5m, 15m, 1h backoff)
                                 │
                            (Max Retries)
                                 ▼
                      [ Dead Letter Queue ]  (DLQ - Triggers Alert)
```

* **Ingestion Queue:** High-throughput queue (powered by BullMQ/Redis) that receives and serializes incoming webhook events.
* **Retry Queue:** Events that failed due to transient issues (e.g., database lockups or external API limits) are placed here.
* **Dead Letter Queue (DLQ):** Messages that fail after 4 retries are parked here. Any event entering the DLQ triggers a PagerDuty/Slack notification for engineering triage.

---

## 4. Retry Strategy & Idempotency

### Retry Intervals
Failed ingestion tasks are retried using a structured backoff schedule:
1. **First Retry:** 1 minute after failure.
2. **Second Retry:** 5 minutes after failure.
3. **Third Retry:** 15 minutes after failure.
4. **Fourth Retry:** 1 hour after failure.

### Idempotency (Deduplication)
Every incoming event is assigned a unique, deterministic **deduplication key** before processing:
* **GitHub Commit:** `github-commit-{commit_sha}`
* **Slack Message:** `slack-msg-{channel_id}-{message_timestamp}`

The ingestion database uses a unique constraint on this key. If the engine attempts to process a duplicate webhook or historical event, the transaction is discarded silently, ensuring **same event = processed once**.
