# Release Checklist

This document defines the pre-deployment quality checks that must be executed and validated before any version of Chronicle is released to production.

---

## 1. Pre-Deployment Validation Steps

Before deploying code to production, the release coordinator must verify that the following command checks execute successfully:

* **Build Compilation:**
  ```bash
  npm run build
  ```
* **Test Suite Success:**
  ```bash
  npm test
  ```
* **Type Safety Verification:**
  ```bash
  npm run typecheck
  ```
* **Lint Constraints Check:**
  ```bash
  npm run lint
  ```

---

## 2. Interactive & System Checks

The release coordinator must manually confirm the following criteria are met:

* **Keyboard Navigation:** The application must be fully navigable without touching the mouse (verified using Tab traversal and command palette triggers).
* **Error States:** Every screen contains working error recovery layouts. No blank pages or raw backend stack traces are visible to the user.
* **Loading States:** Skeletal loading animations render instantly on all async transitions.
* **Telemetry Verification:** Verify that mock search events and API latency metrics are successfully captured in the dev logging service.
* **Observability Verification:** Verify that trace spans are populated across API, database, and LLM layers.
* **Rollback Verification:** Confirm that a mock deployment revert can be executed inside 30 seconds.

---

## 3. The Veto Rule

> **"If a single checklist item fails or is unchecked, the deployment is blocked."**

We do not bypass build or test failures for expediency. Chronicle is a trust-based product. If a lint rule, typecheck, or test benchmark fails, the pipeline halts immediately, and the release is blocked until a fix is pushed and verified.
