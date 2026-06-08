# Deployment Topology

This document outlines the hosting architecture, network pathways, event pipeline structures, and scaling strategies for Chronicle.

---

## 1. Deployment Philosophy

Chronicle V1 operates under a **Simple Operations** philosophy. We reject premature multi-cloud or Kubernetes configurations. The priority is sub-100ms request latency, database consistency, and minimal operational overhead. We use managed serverless and edge infrastructure to maintain execution speed.

---

## 2. Production Topology

The system topology orchestrates web requests, database queries, and background ingestion streams:

```text
  [ Users ] ──► [ Cloudflare Edge ] 
                     │
         ┌───────────┴───────────┐
         ▼                       ▼
  [ Vercel CDN ]         [ API Gateway ]  (Fastify Serverless Functions)
  (Next.js Web)                  │
         │                       ▼
         │               [ Supabase Stack ] ──► [ Supabase Storage ]
         ▼                       │
  [ static assets ]              ▼
                         [ PostgreSQL DB ] (pgvector + RLS)
```

### Routing & Delivery
* **Cloudflare:** DNS routing, DDoS protection, edge caching, and TLS termination.
* **Vercel:** Hosts the Next.js web application, serving static interfaces and client assets from regional edge servers.
* **API Gateway (Fastify):** Processes incoming request routes, manages token sessions, and serves Server-Sent Events (SSE) for query streaming.
* **Supabase / PostgreSQL:** Holds the Causal Graph and relational timelines.

---

## 3. The AI Layer Pipeline

```text
  Question Injected ──► [ Question Planner ] (Groq / Gemini Flash)
                              │
                              ▼
                        [ RAG Fetch ]
                              │
                              ▼
                      [ Evidence Ranking ]
                              │
                              ▼
                        [ Synthesis ] (Gemini 2.5 Pro)
                              │
                              ▼
                        [ Streaming ] ──► User Screen
```

* The Question Planner evaluates and plans the retrieval DAG.
* Structured evidence is retrieved and ranked before being passed to **Gemini 2.5 Pro** for final synthesis.

---

## 4. The Ingestion Event Pipeline

```text
  External Event (GitHub / Slack Webhook)
                     │
                     ▼
           [ Cloudflare Edge ]
                     │
                     ▼
             [ Webhook Service ]
                     │
                     ▼
           [ Upstash Redis Queue ] (BullMQ)
                     │
                     ▼
            [ Sync Ingestion ]
                     │
                     ▼
             [ Timeline Engine ]
                     │
                     ▼
              [ Graph Engine ]
                     │
                     ▼
             [ Memory Engine ] ──► Supabase Storage
```

---

## 5. Scaling Roadmap

* **Phase 1 (V1):** Single-region deployment (AWS `us-east-1` for databases and serverless logic). Simple architecture to establish baseline viability.
* **Phase 2 (V2):** Multi-region reads. Deploy read replicas of the PostgreSQL instance to reduce latency for global developers querying memory.
* **Phase 3 (V3):** Global deployment. Multi-write database clustering, edge database nodes, and distributed queue systems.
