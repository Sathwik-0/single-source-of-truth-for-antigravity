# User Journeys

These user journeys demonstrate how Chronicle operates in real-world scenarios to deliver context, synthesis, and understanding.

---

## 1. The New Engineer (First Week Onboarding)
* **Objective:** Understand how the system's authentication flow works to implement a new feature.
* **Timeline:**
  * **Day 1:** The user connects the team's GitHub organization and primary Slack workspace to Chronicle.
  * **Day 1 (Indexing):** Chronicle indexes the repos, pull requests, commits, and public Slack channels in the background.
  * **Day 2:** The user opens Chronicle and asks: *"How does authentication work in the gateway service?"*
  * **Day 2 (Reconstruction):** Chronicle constructs a synthesis page showing:
    * The files containing the auth logic.
    * The PR where the auth flow was rewritten last year.
    * A Slack thread between senior engineers debating why JWTs were chosen over session cookies.
  * **Outcome:** The new engineer understands the system architecture, constraints, and historical reasons without booking a meeting or interrupting team members.

---

## 2. The On-Call Engineer (Incident Mitigation)
* **Objective:** Find the root cause of a sudden production failure in the payment gateway.
* **Timeline:**
  * **12:00 PM:** PagerDuty alert triggers: `PaymentGatewayTimeout`.
  * **12:02 PM:** The engineer queries Chronicle: *"Why did the payment gateway start failing yesterday?"*
  * **12:03 PM (Synthesis):** Chronicle displays a timeline of the last 48 hours:
    * **Event 1:** A config change committed to main at 3:00 PM yesterday.
    * **Event 2:** A Slack discussion in `#eng-deploy` where a developer mentioned a timeout adjustment.
    * **Event 3:** An active incident log from Sentry showing database lockups.
  * **12:04 PM (Resolution):** The engineer clicks the cited config commit, finds the database pool size was reduced, and reverts the change.
  * **Outcome:** Mean Time to Resolution (MTTR) is cut from hours to under 5 minutes through chronological, cross-tool correlation.

---

## 3. The Tech Lead (Code Review & Legacy Refactoring)
* **Objective:** Determine why Redis was introduced in a legacy module and if it can be safely replaced.
* **Timeline:**
  * **10:00 AM:** While refactoring the caching layer, the developer notices an old Redis integration.
  * **10:01 AM:** The developer queries Chronicle: *"Why was Redis introduced here?"*
  * **10:02 PM (Synthesis):** Chronicle reconstructs the decision history:
    * It links to a PR from 2025 where Redis was added to mitigate write-amplification on PostgreSQL.
    * It highlights the benchmark results saved in a developer's comment inside the PR review.
    * It shows a Slack discussion thread where the team agreed to use Redis as a temporary rate-limiter.
  * **Outcome:** The developer refactors with high confidence, knowing exactly why the dependency exists and what edge cases to protect against.
