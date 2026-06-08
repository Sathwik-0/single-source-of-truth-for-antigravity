# Success Metrics

How we measure whether Chronicle is achieving its mission. All product improvements must show positive movement in these core telemetry groups.

---

## 1. North Star: Time To Understanding (TTU)
* **Definition:** The duration of time between when an engineer submits a query to Chronicle and when they locate the correct answer (measured by clicking on a cited source, viewing the timeline node, or closing the tab without querying another source).
* **Target:** Under **30 seconds** for complex architectural queries (which typically take hours of manual research).
* **Goal:** Continuously minimize TTU.

---

## 2. Secondary Core Metrics

### A. Activation
* **Metric:** Time to First Answer (TTFA).
* **Definition:** The time between a workspace onboarding connection and the user receiving their first high-value, evidence-backed answer.
* **Target:** Under **5 minutes** from repository connection to first query response.

### B. Retention
* **Metric:** Weekly Active Queries (WAQ) per active user.
* **Definition:** The number of unique context-seeking queries submitted by each developer weekly.
* **Target:** An average of **5+ queries per engineer per week**, signaling that asking Chronicle is an instinctive habit.

### C. Trust Rate
* **Metric:** Citation Click-Through Rate (CTR).
* **Definition:** The percentage of answers where the user clicks a cited commit, file path, or Slack message link to inspect the raw evidence.
* **Target:** **40%+ CTR**, indicating that users actively rely on the evidence panels to verify AI synthesis.

### D. Performance Latency
* **Metric:** P95 Query Latency.
* **Definition:** The time taken for the system to plan, retrieve, rank evidence, and stream the response.
* **Target:** Under **2 seconds** for synthesis and timeline assembly.

### E. Knowledge Coverage
* **Metric:** Index Completeness.
* **Definition:** The ratio of indexed resources (repos, Slack channels) to total active resources within the organization.
* **Target:** **95%+ coverage** of public channels and primary repositories.

### F. Reliability Rate
* **Metric:** Hallucination and Error rate.
* **Definition:** The rate of incorrect/unsupported answers reported by users vs. safe fallback states (*"I cannot find evidence for this"*).
* **Target:** **0% tolerance** for hallucination. Fallback rate must track with actual lack of data, never with system failures.
