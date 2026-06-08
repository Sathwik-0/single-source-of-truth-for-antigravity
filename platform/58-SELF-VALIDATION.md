# AI Self-Validation Protocol

This document defines the self-validation guidelines for AI developers (such as Antigravity or Codex) working on Chronicle. The AI must execute this self-review before declaring any task complete.

---

## 1. The Realism Test

* **Is this feature real?** 
  Verify that all four layers exist and connect:
  * **UI:** A functional frontend interface.
  * **API:** Working Fastify backend endpoints.
  * **DB:** Real database schemas, columns, indexes, and RLS policies.
  * **Service:** Underlying TypeScript business logic.
* **Are there mocks or placeholders?**
  Inspect your code. If you find mock data, "coming soon" overlays, or dummy functions, do not mark the task complete. Delete the placeholders and build the end-to-end functionality.

---

## 2. The Evidence Test

* **Is there physical evidence of success?**
  Do not declare victory based on assumptions. Run tests or local commands to confirm:
  * Do compile scripts pass?
  * Do database insertion tests succeed?
  * Are webhooks actively processed?
* **If evidence cannot be shown, do not claim success.**

---

## 3. The Interactive Test

* **Does the button actually work?**
  Trace the execution path of the interactive components you implemented. Confirm that:
  * Clicking an element triggers the API call.
  * The API updates the database correctly.
  * The response payload matches the contract exactly.
  * There are no fake success messages or mock loading loops.

---

## 4. The Resiliency Test

* **Can this feature handle failure?**
  Validate how the feature handles edge cases:
  * **Timeouts:** Does the query handle slow database or LLM network connections?
  * **Expired Tokens:** What happens when a user's GitHub OAuth token is invalidated?
  * **Empty Data:** Does the UI render a helpful empty state when search returns zero results?
  * **Network Outages:** Do background sync jobs retry using BullMQ backoff schedules?

---

## 5. The Telemetry Test

* **Is this feature fully observable?**
  Confirm that:
  * Key actions trigger structured JSON logs.
  * API routes generate tracing span markers.
  * Performance metrics are recorded in the analytics database.
