# Bug Handbook

This document defines the bug classification tiers, triage SLA parameters, and the root-cause analysis (RCA) template for Chronicle.

---

## 1. Bug Classifications

Bugs are triaged into four severity levels based on their impact on user trust and operational security:

* **P0: Trust Failures (Fix Immediately)**
  * *Description:* Outages or accuracy degradations that violate our core values.
  * *Examples:* AI engine generates hallucinated or ungrounded answers; database data corruption; workspace-isolation policy bypass (cross-tenant leakage).
  * *SLA:* Continuous work until resolved and verified.
* **P1: Broken Workflows (High Priority)**
  * *Description:* Loss of primary system operations.
  * *Examples:* OAuth authentication is failing; BullMQ ingestion sync queue is frozen; GitHub/Slack webhook listener rejects events.
  * *SLA:* Resolved within 24 hours.
* **P2: UX Issues (Medium Priority)**
  * *Description:* Performance degradation or interface edge cases.
  * *Examples:* Search latencies exceed 5 seconds; missing loading skeletons or empty states on timeline interfaces; broken keyboard shortcuts.
  * *SLA:* Resolved in the next sprint iteration.
* **P3: Cosmetic Issues (Low Priority)**
  * *Description:* Visual glitches or minor padding alignments.
  * *Examples:* Font size misalignment; slight HSL color token mismatch.
  * *SLA:* Resolved as schedule permits.

---

## 2. Root Cause Analysis (RCA) Template

Every P0 or P1 bug resolution must be accompanied by an RCA document committed to the decisions or incident history folder:

```markdown
# Root Cause Analysis (RCA-XXX)

## Problem
A clear, concise description of what the user experienced.

## Cause
Detailed explanation of why the bug occurred, including database logs, stack traces, or code lines.

## Impact
Who was affected, for how long, and what data was exposed or lost.

## Fix
Step-by-step description of the changes made to resolve the bug, including git commit SHAs.

## Prevention
What automated tests, lint rules, or database constraints were added to prevent this bug from recurring.
```
