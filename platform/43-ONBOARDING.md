# Onboarding Plan

This document defines the First Run Experience (FRE) and onboarding flow for new users, ensuring they experience Chronicle's core value within their first session.

---

## 1. Onboarding Goal: Value in 5 Minutes

The success of Chronicle's onboarding is measured by the time it takes a new user to reach their first "Aha!" moment. We define this as: **A user receives a precise, evidence-backed answer about their own private codebase within 5 minutes of signing up.**

---

## 2. First Run Experience (FRE) Pipeline

To reach value inside the 5-minute threshold, the onboarding experience is structured as a frictionless, linear funnel:

```text
  Create Account (GitHub / Google OAuth)
           │
           ▼
  Connect Integrations (GitHub and Slack bots connected in 2 clicks)
           │
           ▼
  Discover Repositories (Auto-scans and lists active repositories)
           │
           ▼
  Build Initial Timeline (Syncs last 30 days of metadata in the background)
           │
           ▼
  First Query Invitation (Presents suggested questions mapped to their codebase)
           │
           ▼
  Receive Cited Answer (Visualizes answer with side-by-side evidence panel)
```

---

## 3. First Question Suggestions

During the initial sync, the UI prompts the user with specific, high-value questions tailored to their repository metadata:

* **Caching context:** *"Why was Redis introduced?"* or *"Why do we use caching in this service?"*
* **Domain ownership:** *"Who owns the authentication module?"* or *"Who is the primary maintainer of this repository?"*
* **Recent activities:** *"What changed in our deployment last week?"*
* **Incident history:** *"What caused the latest payment service failure?"*

---

## 4. Onboarding Success Criteria

A user has successfully completed onboarding only when they have seen:
1. **The Timeline:** A visual representation of their code evolution.
2. **The Citations:** Clickable source links (e.g. `[Evidence-1]`) embedded directly in the answer.
3. **The Evidence Panel:** A side-by-side view showing the raw commit diff or Slack discussion thread.
4. **The Confidence Score:** A transparent metric indicating the accuracy and source density of the response.
