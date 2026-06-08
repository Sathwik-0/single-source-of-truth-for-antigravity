# Observability

This document defines Chronicle's logging, metrics, tracing, alerting, and dashboard structures. Every component in the system must be fully observable to ensure trust and reliability.

---

## 1. Principles of Observability

We treat observability as a core product feature. If an engineering manager cannot track sync health, or if we cannot trace why a specific answer had a low confidence score, the system fails. Every important system action must leave structured telemetry.

---

## 2. Telemetry Layers

### A. Logging
All services emit structured JSON logs to stderr/stdout. Logs must include:
* `timestamp`, `level` (INFO, WARN, ERROR), `service_name`, and `requestId`.
* **Inbound API Logs:** Logs headers (excluding auth tokens) and response latencies.
* **Sync Job Logs:** Tracks job durations, processed event counts, and webhook payloads.
* **Agent Execution Logs:** Details the plan DAG, sub-agent actions, tool inputs, and observation outputs.

### B. Metrics (System & Product)
* **API Metrics:**
  * P50, P90, P99 API route latency.
  * Ingestion queue throughput and processing lag.
  * Webhook request validation error rates.
* **Product Success Metrics:**
  * **Time to Understanding (TTU):** Track time elapsed from query submission to a user interaction on a citation link or evidence panel.
  * **Trust Rate:** Click-through rate (CTR) on citations.

### C. AI Telemetry Metrics
* **Token Usage:** Track prompt, completion, and total tokens consumed per query.
* **Cost Allocation:** Compute real-time monetary costs (per query, per user, and per workspace).
* **Model Metrics:** Log the average confidence score and the count of evidence sources used per answer.

---

## 3. Distributed Tracing

Chronicle uses OpenTelemetry to trace requests across service boundaries:

```text
  User submits Question (Inject TraceParent Header: trace_id)
           │
           ▼
     [API Gateway] (Start span: api_query)
           │
           ▼
     [Question Planner] (Start span: query_planning)
           │
           ▼
     [Retrieval Engine] (Start span: rag_retrieval)
           │
           ▼
     [Timeline & Graph] (Start spans: timeline_alignment, graph_traversal)
           │
           ▼
     [Evidence Ranking] (Start span: context_scoring)
           │
           ▼
     [LLM Call] (Start span: llm_synthesis)
```

If an answer is incorrect or slow, engineers can trace the entire execution plan using the unique `trace_id` to identify which component failed or introduced latency.

---

## 4. Alerting Thresholds

Alerts are routed to PagerDuty or Slack based on three thresholds:

* **Sync Health Alert:** Triggered if the integration sync failure rate exceeds **5%** or if the BullMQ ingestion backlog exceeds **10,000 events** for more than 10 minutes.
* **Platform Latency Alert:** Triggered if P95 API gateway latency exceeds **3 seconds** over a 5-minute window.
* **AI Quality Alert:** Triggered if the average confidence score of generated answers falls below **0.60** or if the fallback rate (*"No evidence found"*) spikes suddenly, signaling potential indexing degradation.

---

## 5. System Dashboards

Chronicle maintains three primary dashboards:

### Product Dashboard
* Active Users (WAU, MAU).
* Question volume and click-through rates on citations.
* Time to Understanding (TTU) tracking.

### Platform Dashboard
* API route health and throughput.
* Queue lag and retry rates.
* Database query performance and lock durations.

### AI Dashboard
* Real-time API costs (Gemini/Groq billing).
* Average answer confidence distribution.
* Context size and token count per query.
