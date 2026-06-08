# Data Moat

The core defensive barrier of Chronicle is not its code or its AI models. It is the proprietary, highly-connected, historical data graph it accumulates from your engineering organization.

---

## 1. Data Ingestion Sources (Inputs)
Chronicle integrates directly with the developer workflow:
* **GitHub:** Commit logs, code diffs, branches, PR files, comments, and project releases.
* **Slack:** Public messages, direct threads, incident channels, and engineering announcements.
* **Future Sources (Linear, Jira, Notion, Drive):** Requirements, specifications, task histories, and architectural design files.

---

## 2. Proprietary Data Assets
From these raw inputs, Chronicle builds unique internal graphs that no external AI can replicate:
* **Timeline Graph:** A time-series database mapping every developer action, deploy, and discussion thread.
* **Memory Graph:** An evolving semantic catalog of team terms, code microservices, micro-decisions, and domain expertise.
* **Decision Graph:** A record of architectural proposals, debates, iterations, and final resolutions.
* **Incident Graph:** A historical log of Sentry/Datadog spikes matched to code commits, active Slack threads, post-mortems, and the developers who fixed them.

---

## 3. The Compounding Data Loop
As Chronicle is used, its data moat deepens through a self-reinforcing loop:

```text
               More Developer Actions & Deploys
                               ↓
                   More Ingested Raw Events
                               ↓
                  Denser Causal & Memory Graph
                               ↓
              More Accurate, Evidence-Backed Answers
                               ↓
                   Increased Developer Trust
                               ↓
              Higher Usage & More Queries Submitted
```

---

## 4. Why This Matters
An enterprise competitor can easily swap LLM endpoints or copy our frontend layout. However, they cannot replicate three years of an organization's internal conversations, codebase changes, historical incident timelines, and decision trails. 

Our data moat makes the customer's memory irreplaceable and locked to Chronicle.
