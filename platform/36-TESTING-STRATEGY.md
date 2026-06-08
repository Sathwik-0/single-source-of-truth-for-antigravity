# Testing Strategy

This document defines the testing pyramid, coverage expectations, and automated validation gates for Chronicle. We enforce strict testing standards to maintain system trust.

---

## 1. The Testing Pyramid

Chronicle implements a comprehensive, six-tiered testing strategy:

```text
               / \
              /   \
             /     \
            /  HAL  \  <-- Hallucination Benchmarks (Accuracy verification)
           /---------─\
          /   AGENT   \  <-- Individual Agent Runs (Planner, Graph, etc.)
         /-------------\
        /     E2E       \  <-- Full query-to-answer UI workflows
       /-----------------\
      /    CONTRACT       \  <-- API request/response schema validation
     /---------------------\
    /     INTEGRATION       \  <-- Ingestion sync (GitHub/Slack OAuth)
   /------------------------─\
  /           UNIT            \  <-- Math formulas, scores, logic (90% target)
 /_____________________________\
```

---

## 2. Testing Levels & Specifications

### A. Unit Tests (Coverage Target: 90%+)
* **Scope:** Mathematical algorithms, parsing rules, and logical helper files.
* **Critical Modules:**
  * **Evidence Ranking:** Verify scoring distributions, recency exponential decay math, and author weighting calculations.
  * **Question Planner:** Verify query parameter extraction and DAG generation logic.
  * **Memory Engine:** Test alias resolution mapping, decay calculations, and retention filters.
  * **Timeline Engine:** Test event normalization and clustering.

### B. Integration Tests
* **Scope:** Real-world API interactions and sync jobs.
* **Verification Targets:**
  * GitHub Sync: Simulate repository connection and verify mock commit backfills.
  * Slack Sync: Mock Slack webhooks and verify message processing.
  * OAuth: Validate tokens and session persistence workflows.
  * Ingestion: Test BullMQ queues under load and confirm DLQ routing.

### C. Contract Tests
* **Scope:** Strict validation of API structural configurations.
* **Verification Targets:**
  * Validate that API endpoints match defined schemas (e.g., verifying request/response payload formats in `platform/29-API-CONTRACTS.md`).
  * Ensure that API errors conform to the standard error structure.

### D. End-to-End (E2E) Tests
* **Scope:** High-level user interaction simulations.
* **Verification Targets:**
  * User logs in via GitHub OAuth → Connects organization → Asks question → Receives cited answer → Clicks citation link → Confirms Evidence Panel matches.

### E. Agent Tests
* **Scope:** Verifying agent performance and decision outputs individually.
* **Verification Targets:**
  * **Planner Agent:** Validate target entities extraction from ambiguous prompts.
  * **Graph Agent:** Ensure causal paths are resolved correctly.
  * **Timeline Agent:** Confirm event ordering and bucketing are accurate.
  * **Evidence Agent:** Verify that source verification logic rejects ungrounded items.

---

## 3. Hallucination Benchmarks

To protect the "Evidence > AI" core value, Chronicle maintains a test suite of **benchmark questions** with known database states:

* **Test Scenario 1 (No Evidence):** Submit a query where no matching data exists in the database.
  * *Expected Output:* System must bypass synthesis and return the safe fallback state (*"I cannot find any evidence..."*). If the system attempts to answer or generate a summary, the test fails.
* **Test Scenario 2 (Contradictory Evidence):** Input conflicting records (e.g. Commit says "resolved" but Slack says "still failing").
  * *Expected Output:* System must note the conflict in the answer and declare a low confidence score.
