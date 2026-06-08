# Execution Roadmap

This document defines the chronological master build phases for Chronicle. Development must progress sequentially; no phase may begin until the preceding phase is verified and complete.

---

## The Build Phases

```text
  ┌────────────────────────────────────────────────────────┐
  │                   Chronicle Build                      │
  └────────────────────────────────────────────────────────┘
       │
       ├─► [Phase 1: Infrastructure] ──► Auth, database tables, local setups,
       │                                 GitHub & Slack OAuth connectors.
       │
       ├─► [Phase 2: Core Engines]   ──► Timeline, Causal Graph, Memory,
       │                                 and Question Planner models.
       │
       ├─► [Phase 3: QA & Synthesis] ──► RAG pipelines, Evidence Ranking,
       │                                 and citation generation.
       │
       ├─► [Phase 4: Team Features]  ──► Shared timelines, Slack reports,
       │                                 and collaborative workspaces.
       │
       └─► [Phase 5: Scale & Enterprise] ──► SSO, SOC2 audit logs, global replicas,
                                             and advanced integrations.
```

---

## Phase Detail & Requirements

### Phase 1: Infrastructure
* **Focus:** Establish the system backbone.
* **Deliverables:**
  * Supabase project initialization, schemas, and RLS policies.
  * Node.js/Fastify server setup.
  * GitHub & Slack OAuth integration pipelines (saving encrypted tokens).

### Phase 2: Core Engines
* **Focus:** Build the context processing layers.
* **Deliverables:**
  * Timeline Engine (normalization of raw commit and chat payloads into events).
  * Graph Engine (populating nodes and edges, writing traversal queries).
  * Memory Engine (semantic extraction via Gemini Flash, vector embedding generation).
  * Question Planner (DAG planning and model routing).

### Phase 3: Question Answering (QA & Synthesis)
* **Focus:** Connect search input to cited output.
* **Deliverables:**
  * Evidence Ranking (applying the 5-factor scoring model).
  * Synthesis Engine (routing grounded context to Gemini 2.5 Pro).
  * Grounding Verification (post-generation citation check).
  * Next.js keyboard-navigable search interface.

### Phase 4: Team Features
* **Focus:** Drive internal organic sharing loops.
* **Deliverables:**
  * Shared timeline URLs and iframe previews.
  * Automatic post-mortem report drafts.
  * Slack notifications for resolved incidents.

### Phase 5: Scale & Enterprise
* **Focus:** Enable corporate rollout.
* **Deliverables:**
  * Google/Okta SAML SSO.
  * Audit Log table monitoring.
  * Multi-region read replicas.
  * Notion, Jira, and Linear integrations (V2 roadmap).
