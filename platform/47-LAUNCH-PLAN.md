# Launch Plan

This document outlines the four-phase launch strategy for bringing Chronicle to market, detailing execution milestones and success metrics for each phase.

---

## 1. The Launch Phases

```text
  ┌───────────────────┐     ┌───────────────────┐     ┌───────────────────┐     ┌───────────────────┐
  │  Phase 1: Founder │     │ Phase 2: Partners │     │  Phase 3: Beta    │     │  Phase 4: Public  │
  ├───────────────────┤     ├───────────────────┤     ├───────────────────┤     ├───────────────────┤
  │ Internal dogfood. │     │ 5-10 design teams │     │ 50 teams on       │     │ Public waitlist,  │
  │ Verify indexing,  │     │ verify onboarding │     │ platform. Focus   │     │ content loops,    │
  │ RAG, and latency. │     │ and activation.   │     │ on retention.     │     │ open access.      │
  └─────────┬─────────┘     └─────────┬─────────┘     └─────────┬─────────┘     └─────────┬─────────┘
            │                         │                         │                         │
            ▼                         ▼                         ▼                         ▼
      [Zero Outages]            [40% Activation]          [50% Retention]           [Scalable Growth]
```

---

## 2. Phase Detail & Exit Gates

### Phase 1: Founder Testing (Internal Dogfooding)
* **Goal:** Use Chronicle internally to index our own development repos, Slack communication, and post-mortems.
* **Exit Gate:** 
  * P95 query latency remains under 2 seconds.
  * Zero database sync lockups or dropped webhooks.
  * 100% correct answering on core team memory tests.

### Phase 2: Design Partners (5–10 Teams)
* **Goal:** Work closely with a small group of trusted engineering teams to test the onboarding flow and primary integrations.
* **Exit Gate:**
  * User activation rate (Query + Answer + Citation Click) exceeds **40%**.
  * No critical bugs reported on GitHub/Slack OAuth connection flows.

### Phase 3: Private Beta (50 Teams)
* **Goal:** Open access to a waitlist of 50 startups to monitor self-serve onboarding, memory consolidation quality, and retention.
* **Exit Gate:**
  * Weekly Active Retention rate (users querying at least once a week) exceeds **50%** after 4 weeks.
  * Successful compilation of automated daily incident summaries.

### Phase 4: Public Launch
* **Goal:** Public release, waitlist clearance, and content marketing distribution (e.g. blog posts demonstrating Chronicle resolving real-world post-mortems).

---

## 3. Global Launch Success Metrics

To declare the launch a success, the platform must sustain these metrics:
* **Activation:** > 40% of connected organizations complete the activation loop.
* **Retention:** > 50% weekly active query retention.
* **Time To Understanding (TTU):** Average TTU for complex historical bugs is resolved in under 30 seconds.
* **System Latency:** P95 latency for query synthesis remains under 2.5 seconds.
