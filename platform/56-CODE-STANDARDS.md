# Coding Standards

This document defines the language specifications, naming conventions, structural guidelines, and quality rules for the Chronicle codebase.

---

## 1. Core Technology Constraint

* **TypeScript Only:** The entire codebase (frontend apps, backend Fastify services, shared packages, and database scripts) must be written exclusively in TypeScript.
* **No `any`:** The use of `any` is strictly prohibited. If a type is unknown or dynamic, developers must use `unknown`, generics, or define explicit custom types. The build script will fail if `any` is detected.

---

## 2. Naming Conventions

* **Variables & Functions:** `camelCase` (e.g. `validateSessionToken`, `databasePoolSize`).
* **Classes & Interface Types:** `PascalCase` (e.g. `TimelineEngine`, `CausalGraphEdge`).
* **Folders & File Basenames:** `kebab-case` (e.g. `timeline-engine.ts`, `auth-permissions.md`).

---

## 3. Structural Guidelines

* **Small & Pure Functions:** Functions should do one thing and have zero side-effects where possible. Large, nested methods must be refactored.
* **Single Responsibility Services:** Backend modules must contain unique business scopes (e.g., `github-oauth-connector` does not handle database query execution).
* **Reusable Components:** Frontend components (e.g. AnswerCard, LoadingSkeleton) must be decoupled from data routing, utilizing shared UI tokens.
* **Versioned APIs:** All public and internal API endpoints must be explicitly versioned in the route path (e.g., `/api/v1/questions`).
* **Structured Errors:** API errors must return the standard error envelope (code, message, `requestId`) as defined in the API contracts specification.
* **JSON Logging:** All services must output standard JSON logs to stdout/stderr.
* **Self-Explaining Comments:** Comments must explain **why** code is written, not *what* the code does. Code readability should indicate what it does.
