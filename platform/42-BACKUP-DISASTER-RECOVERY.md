# Backup & Disaster Recovery

This document defines the automated backup processes, retention policies, recovery targets, and restoration procedures for Chronicle's data assets.

---

## 1. Backup Philosophy

> **"Memory is the product. Losing data is unacceptable."**

Chronicle exists to preserve corporate memory. A database crash, server failure, or cloud outage must never result in permanent knowledge loss. If we lose our users' memory, we fail our product promise. Our backup infrastructure is designed with redundancy, isolation, and automation.

---

## 2. Backup Schedules & Retention

### Database Backups (Supabase PostgreSQL)
* **Frequency:** Automated daily snapshots.
* **Point-in-Time Recovery (PITR):** Write-Ahead Logging (WAL) is archived continuously, enabling restoration to any millisecond within the last 7 days.
* **Encryption:** Backup files are encrypted using AES-256 before upload.
* **Retention Schedule:**
  * Daily backups retained for **7 days**.
  * Weekly backups retained for **30 days**.
  * Monthly backups retained for **90 days**.

### File & Asset Storage Backups
* **Scope:** Supabase Storage buckets containing static documentation exports, project assets, and user attachments.
* **Method:** Replicated to a secondary, physically isolated cloud storage bucket (AWS S3) in a different geographic region daily.

---

## 3. Disaster Recovery (DR) Targets

Our infrastructure configuration is bounded by two targets:

* **Recovery Point Objective (RPO) < 1 Hour:** The maximum acceptable period of data loss during a failure. Daily backups and continuous WAL archiving ensure we never lose more than 1 hour of event history.
* **Recovery Time Objective (RTO) < 4 Hours:** The maximum acceptable downtime before the service is restored to a functional state.

---

## 4. Recovery Procedure

In the event of a catastrophic regional outage, the recovery coordinator must execute this restoration plan:

```text
  Catastrophic Outage Detected
               │
               ▼
  1. Restore Database ──► Provision new Postgres instance; apply latest daily backup.
               │
               ▼
  2. Apply WAL Logs   ──► Replay archived logs to recover data up to the failure point.
               │
               ▼
  3. Restore Storage  ──► Synchronize assets from secondary AWS S3 backup bucket.
               │
               ▼
  4. Verify Tokens    ──► Verify encryption keys and re-validate integration tokens.
               │
               ▼
  5. Integration Sync ──► Trigger incremental sync tasks to catch missed webhook events.
               │
               ▼
  6. Health Check     ──► Execute automated verification tests.
               │
               ▼
  System Back Online
```

### Verification Checks
Before routing traffic back to the restored instance, the coordinator must verify:
* Row-Level Security (RLS) policies are active and isolating tenants.
* The API Gateway can retrieve timeline events.
* Vector similarity search returns expected test matches.
