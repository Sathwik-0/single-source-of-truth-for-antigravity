# Activation Metrics & Friction Removal

This document defines the user activation milestones, target metrics, and friction-reduction requirements for Chronicle.

---

## 1. Defining the Activation Event

In Chronicle, user activation is not defined by simple registration or logging in. A user is activated if and only if they execute the **Core Value Loop**:

```text
       User Asks Question
               │
               ▼
   Receives Evidence-Backed Answer
               │
               ▼
  Opens At Least One Cited Source Node (Evidence Panel)
```

Opening a cited source confirms that the user is actively verifying the AI synthesis, engaging with the trust layer, and experiencing Chronicle’s core "Evidence > AI" value.

---

## 2. Target Activation Rate

* **V1 Target:** **> 40%** of signed-up workspaces must complete the Activation Event within 7 days of repository connection.

---

## 3. Key Telemetry Metrics

To monitor and optimize the activation funnel, the following metrics are tracked:

* **Time to First Question (TTFQ):** The duration between integration connection completion and the submission of the user's first query.
* **Time to First Answer (TTFA):** The duration between query submission and the response rendering on screen (Target: < 2 seconds).
* **Time to First Source Click (TTFSC):** The duration between the answer rendering and the user clicking on an inline citation.

---

## 4. Friction Removal Rules

To achieve a 40%+ activation rate, all manual setup friction is systematically removed:

* **Zero Custom Training Required:** The system requires no manual model fine-tuning or prompt configuration by the user. It works out-of-the-box.
* **Two-Click Integrations:** Connection to GitHub and Slack must use native, pre-vetted OAuth installations that request the minimum necessary scopes in a single confirmation.
* **Instant Suggested Queries:** If the user’s search box is empty, Chronicle dynamically populates it with clickable suggestions derived from recent repository commits, avoiding the "empty search box" friction.
