---
name: OpenSpec: Proposal
description: Scaffold a new OpenSpec change and validate strictly.
category: OpenSpec
tags: [openspec, change]
---
<!-- OPENSPEC:START -->
**Guardrails**
- Favor straightforward, minimal implementations first and add complexity only when it is requested or clearly required.
- Keep changes tightly scoped to the requested outcome.
- Refer to `openspec/AGENTS.md` (located inside the `openspec/` directory—run `ls openspec` or `openspec update` if you don't see it) if you need additional OpenSpec conventions or clarifications.
- Treat these as your sources of truth for context (in this order):
  1. `.ops/build/product-vision-strategy.md` (high-level product context from Clavix)
  2. `.ops/build/v{x}/prd.md` (build-level plan/scope from Clavix)
- Identify any vague or ambiguous details and ask the necessary follow-up questions before editing files.
- Do not write any code during the proposal stage. Only create design documents (prd.md, epic.md, design.md, and feature specs). Implementation happens in the apply stage after approval.

**Steps**
1. Read `.ops/build/product-vision-strategy.md` for high-level product context, then read `.ops/build/v{x}/prd.md` for the current build’s scope and acceptance criteria.
2. If `.ops/build/v{x}/prd.md` is missing or stale, generate/update it via Clavix before writing any specs.
3. Break the build into concrete **features** (use `<feature-name>`), and create one folder per feature under `.ops/build/v{x}/<feature-name>/`.
4. Draft feature specs in `.ops/build/v{x}/<feature-name>/spec.md` using `## ADDED|MODIFIED|REMOVED Requirements` with at least one `#### Scenario:` per requirement. Cross-reference other feature specs when relevant.
5. Draft `.ops/build/v{x}/epic.md` as an ordered list of small, verifiable work items that deliver user-visible progress, include validation (tests, tooling), and highlight dependencies or parallelizable work.
6. Validate with `openspec validate <id> --strict --no-interactive` (or your wrapper) and resolve every issue before sharing the proposal.

**Reference**
- Use `openspec show <id> --json --deltas-only` or `openspec show <spec> --type spec` to inspect details when validation fails.
- Search existing requirements with `rg -n "Requirement:|Scenario:" .ops/build` before writing new ones.
- Explore the codebase with `rg <keyword>`, `ls`, or direct file reads so proposals align with current implementation realities.
<!-- OPENSPEC:END -->
