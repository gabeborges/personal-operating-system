# GLOBAL CLAUDE.md — Security, Identity, & Baseline Defaults

This file defines **absolute guards** that must be followed for all Claude Code and agent operations across any repo or project.

---

## NEVER EVER DO — GATEKEEPER RULES (Absolute)

### 🚫 NEVER Publish Sensitive Data
- Never publish passwords, API keys, tokens, or credentials in code, documentation, comments, PRs, branch names, commits, issue titles, diffs, or logs.
- Before ANY commit, ensure no secrets exist in staged changes.

### 🔒 NEVER Commit `.env` or Credential Files
- `.env`, `.env.local`, `.env.*`, credential files, secret files, and config files containing secrets must NEVER be committed.
- Confirm `.gitignore` properly excludes these before code generation.

### ❌ NEVER Hardcode Credentials
- Always use secure environment variables or secret stores (e.g., Vercel environment settings, Supabase secrets) for all credentials.
- Do not embed API keys, secret tokens, database credentials, OAuth secret values, or similar in code or configuration.

---

## Project Defaults (Global)

### Identity
- Default GitHub identity:
  - `git config user.name = YourGitHubUsername`
  - `git config user.email = your.email@example.com`
- Default repository origin format for CI/CD:
  - `git@github.com:YourGitHubUsername/<repo>.git`

### Repository Baseline
- Required at project creation:
  - `.gitignore` covering all secret files
  - `.env.example` with placeholder values (no actual secrets)
  - `README.md` with environment setup instructions

---

## Enforced Tool Use

### PreToolUse / Guard Hooks (Highly Recommended)
- Strategies (optional but recommended):
  - PreToolUse hook that rejects edits to any `*.env*` file
  - PreToolUse hook that rejects commits with secret patterns (`KEY=`, private tokens)
- These hooks run before Claude Code or local tools execute.

---

## Safety & Model Constraints

### Output Constraints
- The model MUST NOT output secrets, even if they exist locally.
- If a suggestion would include sensitive data, it MUST issue a warning like:
  > “Proposal contains sensitive data — remove before proceeding.”

### Fault Reporting
- If illegal/sensitive output is generated, stop and log the occurrence into:
  - `audit/clause/security_incidents.log`

---

## Security Priority
These rules are **non-negotiable** and apply across all repositories, agents, and use cases.

---

END OF GLOBAL CLAUDE.md
