# PROJECT CLAUDE.md — Execution Guardrails (SDD + Next.js + Stack)

This file defines **project-specific execution constraints** for Claude Code and agent execution.

Stack: Next.js (App Router), Tailwind, shadcn/ui, Headless UI, Supabase, Stripe, Google OAuth  
Testing: Vitest + Playwright  
Package manager: npm  
Development workflow: SDD (Clavix + OpenSpec + Agents)

---

## 0) SDD First (Mandatory)

Before writing or changing code, you MUST read:
- `.ops/build/product-vision-strategy.md`
- `.ops/build/v{x}/prd.md`
- `.ops/build/v{x}/epic.md`
- `.ops/build/v{x}/<feature-name>/spec.md`
- `.ops/build/v{x}/<feature-name>/tasks.md`

**Do not proceed with coding if any artifact above is missing or ambiguous. Ask questions.**

---

## 1) Workflow Discipline

### Spec vs Implementation
- Write `spec.md` and `epic.md` only in the proposal phase.
- Only implement code from tasks in `tasks.md` (feature-level).

### File Roles
- `epic.md` → version-level epic in `.ops/build/v{x}/epic.md`
- `spec.md` → requirements + acceptance criteria in `.ops/build/v{x}/<feature-name>/spec.md`
- `tasks.md` → feature tickets in `.ops/build/v{x}/<feature-name>/tasks.md` with `implements:` pointers into `spec.md`

---

## 2) Directory & Version Safety

- Work within `.ops/build/v{x}/<feature-name>/`
- Do not mix build versions (`v0`, `v1`, `v2`) in a single change or commit.

---

## 3) Next.js (App Router) Norms

### App Router Enforcement
- Stick to App Router conventions.
- Keep components modular and colocated with routes or features.

### Client vs Server
- Prefer **Server Components** by default.
- Use `"use client"` only where required by UI behavior.

---

## 4) Styling/UI Rules

- Use Tailwind for utility-style consistency.
- Use components from shadcn/ui.
- Use Headless UI for accessible primitives.

Do not add new UI libs without explicit approval or task references.

---

## 5) Data / Auth / Payments

### Supabase
- Respect existing RLS and permission boundaries.
- Sensitive logic goes server-side.

### Google OAuth
- Do not relax OAuth scopes, callback URLs, or token handling without spec reference.

### Stripe
- Respect existing semantics for billing, products, prices, and webhooks.
- Always verify webhook signatures.

---

## 6) Security & Secrets (Project Level)

- Do not log secrets.
- Do not commit credentials.
- Do not embed secret values in code or config.
- If spec conflicts with secure implementation:
  - Create a `spec-change-requests.md` entry and stop.

---

## 7) Testing & Validation

Before marking a task done:
- Validate code satisfies acceptance scenarios in `spec.md`.
- Add tests consistent with:
  - **Vitest** for logic and API tests
  - **Playwright** for E2E flows

Run project scripts (inspect `package.json`):
- `npm run lint`
- `npm run test`
- `npm run test:e2e`
- `npm run build`

---

## 8) Change Discipline (Minimal Diff)

- Implement the *smallest change* that satisfies the task.
- Do not refactor unrelated code.
- Avoid renaming files unless required by tasks.

---

## 9) Output Requirements

When completing a ticket:
- Describe what puzzles/scenarios it satisfies (`implements:` pointers).
- Show tests run and results.
- Attach reproducible steps or validation notes.

---

## 10) Stop & Ask Conditions

Stop and ask:
- If build version `v{x}` isn’t clear
- If required spec artifacts are missing
- If tasks lack `implements:` pointers
- If a secret situation/security risk is unclear

---

END OF PROJECT CLAUDE.md
