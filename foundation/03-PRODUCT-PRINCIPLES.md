# Product Principles

These principles guide every design, architectural decision, and line of code written for Chronicle. If a feature or technical implementation violates these principles, it must be rejected.

---

## 1. Evidence > AI

AI is a powerful synthesizer, but it is not a primary source of truth.
* Chronicle must never present a synthesized AI answer without clear, clickable citations to raw evidence (e.g., Git commits, PRs, Slack messages, logs).
* If no direct evidence exists to answer a query, Chronicle must explicitly state: *"I cannot find evidence to answer this question"* rather than guessing or generating a plausible-sounding response.
* AI-generated summaries must be bounded strictly by the context retrieved from the database.

---

## 2. Memory > Search

We are not building a faster search box. We are building an active memory system.
* Search returns matching documents; memory reconstructs the connections between events.
* Every query should result in a timeline or graph that links people, code, and discussions over time.
* The product must show how a decision evolved, not just where the keywords match.

---

## 3. Trust > Features

Trust is our most valuable asset and our most fragile constraint.
* An engineer will use Chronicle only if they trust that its answers are accurate, current, and evidence-backed.
* A single hallucinated response or misleading link will permanently damage that trust.
* We prioritize accuracy and reliability over shipping half-baked features or broad integrations.

---

## 4. Keyboard First

Chronicle is built exclusively for software engineers.
* The application must be fully navigable and operable without touching a mouse.
* Navigation must feel as fast and intuitive as Vim or Cursor.
* Command palettes (`Cmd+K`), custom shortcuts, and direct command execution are core to the experience.
* Low latency is critical: interactions must resolve in milliseconds.

---

## 5. No Fake Buttons

Every UI element must be backed by functioning, production-ready code.
* We do not build placeholders, disabled states that say "Coming Soon", or mock buttons designed to gauge user interest.
* If a button is visible, it must execute its entire workflow (UI → API → Service → Database → Response) immediately.

---

## 6. No Dead Screens

There are no dead ends in Chronicle.
* Every error state, empty state, or loading state must provide context and actionable next steps.
* If a search returns no results, Chronicle should guide the user on how to adjust their query or invite them to share more context (e.g., connecting a new repository or channel).
