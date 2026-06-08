# API Contracts

This document defines the strict API endpoint contracts, request/response models, and standard error envelopes for the Chronicle backend API.

---

## 1. Questions API
* **Endpoint:** `POST /api/questions`
* **Purpose:** Submits a natural language query to the Question Planner to trigger retrieval, ranking, and response synthesis.

### Request Body
```json
{
  "question": "Why was Redis introduced?"
}
```

### Response (200 OK)
```json
{
  "answer": "Redis was introduced in Commit [Commit-1] to handle session persistence and caching. According to PR discussion [PR-2], the team chose Redis over Postgres to mitigate high read-write latency under heavy API load.",
  "confidence": 0.92,
  "sources": [
    {
      "id": "e7b9231f-0e12-4cfb-8199-c02058ff5310",
      "citation_key": "Commit-1",
      "type": "commit",
      "title": "Introduce Redis session store",
      "url": "https://github.com/org/repo/commit/a3f89e",
      "author": "Sathwik"
    },
    {
      "id": "f8c8231f-0e12-4cfb-8199-c02058ff5311",
      "citation_key": "PR-2",
      "type": "pull_request",
      "title": "Implement Redis Caching Layer",
      "url": "https://github.com/org/repo/pull/14",
      "author": "Sathwik"
    }
  ]
}
```

---

## 2. Timeline API
* **Endpoint:** `GET /api/timeline/{entityId}`
* **Purpose:** Returns a chronological reconstruction of events related to a specific entity (e.g., repository or microservice).

### Response (200 OK)
```json
{
  "entityId": "qxnicmicqammdrrxidak",
  "timeline": [
    {
      "timestamp": "2026-06-08T12:00:00Z",
      "event_type": "deploy",
      "title": "Version 1.2.0 Deployed",
      "description": "Production deploy completed successfully",
      "author": "GitHub CI"
    },
    {
      "timestamp": "2026-06-08T12:05:00Z",
      "event_type": "incident",
      "title": "Alert: Database Congestion",
      "description": "Database connection pool exhausted",
      "author": "Telemetry"
    }
  ]
}
```

---

## 3. Memory Search API
* **Endpoint:** `GET /api/memory/search`
* **Purpose:** Performs a semantic search across memories and embeddings.
* **Query Parameter:** `?q=redis`

### Response (200 OK)
```json
{
  "query": "redis",
  "results": [
    {
      "memory_id": "b9f8231f-0e12-4cfb-8199-c02058ff5314",
      "category": "long_term",
      "content": "Redis is used for caching session state to reduce Postgres write amplification.",
      "importance": 8,
      "score": 0.89
    }
  ]
}
```

---

## 4. Evidence API
* **Endpoint:** `GET /api/evidence/{id}`
* **Purpose:** Retrieves complete metadata, relationship mapping, and timeline details for a specific evidence citation.

### Response (200 OK)
```json
{
  "evidence_id": "e7b9231f-0e12-4cfb-8199-c02058ff5310",
  "source_type": "commit",
  "source_id": "a3f89e",
  "content": "commit a3f89e\nAuthor: Sathwik\nDate: Sun Jun 7\n\nIntroduce Redis session store",
  "confidence_score": 1.0,
  "relationships": [
    {
      "type": "CAUSED",
      "target": "Incident: Database Pool Exhaustion"
    }
  ],
  "timeline": {
    "relative_order": 1,
    "timestamp": "2026-06-07T16:09:41Z"
  }
}
```

---

## 5. Integration Connection APIs
* **Endpoints:** 
  * `POST /api/integrations/github/connect`
  * `POST /api/integrations/slack/connect`

### Request Body
```json
{
  "code": "auth_code_from_oauth_callback"
}
```

### Response (200 OK)
```json
{
  "status": "connected",
  "integration_id": "c7b9231f-0e12-4cfb-8199-c02058ff5300",
  "provider": "github",
  "scopes": ["repo", "read:org", "user:email"]
}
```

---

## 6. Standard Error Format
Every failing API response must return this envelope:

### Response Body (e.g. 400 Bad Request)
```json
{
  "code": "INVALID_QUERY_PARAMETER",
  "message": "The query parameter 'q' is required for memory search.",
  "requestId": "req_f2e91238f88a8c"
}
```
All errors must include a unique, traceable `requestId`.
