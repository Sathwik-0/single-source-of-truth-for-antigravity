# Hallucination Prevention

This document defines the rules, validation loops, and fallback triggers that protect Chronicle's trust layer. Hallucination prevention is our highest engineering constraint.

---

## 1. Core Principles of Trust

If Chronicle hallucinates a single answer, it loses developer trust permanently. To ensure absolute reliability, the system operates under three strict rules:

```text
  ┌─────────────────────────────────────────────────────────────────────────┐
  │                        Hallucination Prevention                         │
  └─────────────────────────────────────────────────────────────────────────┘
       │
       ├─► [No Evidence = No Answer] ──► If the database has no records,
       │                                 we return a safe fallback state.
       │
       ├─► [Low Confidence = Say Uncertainty] ──► If evidence exists but lacks
       │                                         clarity, we state our confidence.
       │
       └─► [Missing Data = Ask Follow-up] ──► If a query is ambiguous, we ask
                                             the user for details rather than guess.
```

---

## 2. Rule Execution Workflows

### Rule 1: No Evidence = No Answer
* If the [Evidence Ranking Engine](file:///C:/Users/ADMIN/.gemini/antigravity/scratch/single-source-of-truth-for-antigravity/architecture/25-EVIDENCE-RANKING.md) returns zero source nodes above the `0.50` threshold, the query stops immediately.
* The system bypasses the synthesis model and displays:
  > *"I cannot find any evidence to answer this question in your repositories or Slack history."*

### Rule 2: Low Confidence = Say Uncertainty
* When retrieved evidence is sparse or contradictory (e.g., a git commit says "fixed auth" but a subsequent Slack thread says "auth is still failing"), the Summarization Agent is instructed to explicitly call out this conflict:
  > *"Evidence indicates a timeout configuration was changed in Commit [Commit-1], but Slack discussions [Slack-2] suggest the timeout issue was still occurring 2 hours later. Confidence: Low."*

### Rule 3: Missing Data = Ask Follow-up
* If the user asks a query with ambiguous identifiers (e.g. *"Why is the db slow?"* when the workspace contains 5 databases), the Question Planner halts execution.
* Instead of choosing a database at random, the system asks a follow-up:
  > *"Which database are you referring to? (e.g., payment-db, user-db, audit-log)"*

---

## 3. Post-Generation Grounding Verification

All generated text is evaluated by a secondary validator model before streaming to the client. The validator splits the generated response into discrete sentences and verifies that every sentence is supported by at least one cited fact in the retrieved context. If any sentence fails this validation, it is stripped from the output.
