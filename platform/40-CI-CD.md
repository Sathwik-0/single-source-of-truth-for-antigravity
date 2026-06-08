# CI/CD & Release Policy

This document defines the branching strategy, testing gates, preview environment setups, and rollback policies for Chronicle.

---

## 1. Branching Strategy

Chronicle uses a Git workflow focused on code quality and clean history:

```text
       [ feature/auth-setup ] ──► (Create PR) ──► [ Preview Env ]
                                                     │
                                                 (Approved)
                                                     ▼
  [ develop ] (Staging / Integration Branch) ────────┴─────────────────────
         │
     (Release)
         ▼
  [ main ] (Stable Production Branch) ────────────────────────────────────
```

* **`main`:** Production code. Only modified via automated releases from the `develop` branch. Direct commits are forbidden.
* **`develop`:** Staging and integration branch. Developers merge their feature branches here after review.
* **`feature/*`:** Topic branches for implementing single features or bug fixes. Created from `develop`.

---

## 2. Pull Request Gate Rules

A pull request cannot be merged into `develop` unless all of the following checks pass in GitHub Actions:

1. **Compilation Check:** Code must compile/build successfully (`npm run build`).
2. **Type Safety:** TypeScript compiler must pass with zero errors (`npm run typecheck`).
3. **Lint Verification:** Code formatting must conform to Eslint and Prettier rules (`npm run lint`).
4. **Test Suite Success:** All unit, integration, and contract tests must pass (`npm run test`).
5. **Code Review:** Must receive approval from at least one core engineering maintainer.

---

## 3. Deployment Pipeline

When code is merged into `main`, the CI/CD pipeline (GitHub Actions) executes these steps:

```text
  Push to main
       │
       ▼
  [ Build ] (Verify Next.js and Fastify compilation)
       │
       ▼
  [ Run Tests ] (Execute complete test suite)
       │
       ▼
  [ Security Scan ] (Vulnerability check on dependencies via Snyk / Dependabot)
       │
       ▼
  [ Production Deploy ] (Upload assets to Vercel, apply Supabase database migrations)
```

---

## 4. Preview Environments

Every open Pull Request automatically triggers the generation of an isolated **Preview Environment**:
* Vercel provisions a unique URL containing the PR's frontend build.
* GitHub Actions creates a temporary, isolated database schema in Supabase (`preview_pr_{number}`).
* The environment is torn down automatically when the PR is merged or closed.

---

## 5. Rollback Policy & Releases

### Instant Rollbacks
* **Frontend:** If a deployment introduces a critical bug, the maintainer triggers a Vercel instant deployment revert, pointing traffic back to the previous stable commit within seconds.
* **Database:** Migration scripts must include a corresponding `down.sql` file. Database changes can be rolled back using the Supabase CLI: `supabase db rollback`.

### Release Policy
* **Small, Frequent, Reversible:** We prioritize shipping small, discrete increments multiple times a day instead of large, high-risk monthly releases. Every change must be backward-compatible and easily reversible.
