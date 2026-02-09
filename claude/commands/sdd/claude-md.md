---
description: "Create or optimize CLAUDE.md — project rules auto-loaded every session"
---

# /sdd:claude-md — Write/Optimize CLAUDE.md

Create or optimize the project's `CLAUDE.md` file. Pass `$ARGUMENTS` for specific instructions (e.g., "add testing commands" or "full rebuild").

## Claude Code Mechanics — CLAUDE.md

- **Auto-loaded into system prompt every session** — every line costs tokens on every turn
- Hierarchy (higher overrides lower): managed policy > project root > user home > nested > local overrides
- Supports `@path/to/file` imports (max 5 hops, relative to containing file)
- If CLAUDE.md > 200 lines, it's too long — prune or move content to skills
- Test each line: "would removing this cause Claude to make mistakes?" — if no, delete it
- Move rarely-needed knowledge to skills (loaded on-demand, not always)

## Process

1. Read existing `CLAUDE.md` (if exists)
2. Explore project structure: package.json, config files, directory layout
3. Identify conventions that differ from Claude's defaults (only these need documenting)

4. Write/update `CLAUDE.md` with:
   - Stack and package manager
   - Code style rules (only non-default conventions)
   - Testing commands
   - Git conventions
   - Architecture decisions unique to the project
   - Security rules (non-negotiable section)
   - Autonomy policy (always proceed vs always ask)
   - Common gotchas / required env vars

5. Verify with Read tool after writing

## Quality Gates

- **< 150 lines** — ruthlessly prune
- No obvious/redundant rules (things Claude does correctly by default)
- Bullet-point format, not prose
- Every line earns its place: "would removing this cause mistakes?"
- No duplication with AGENTS.md (CLAUDE.md = rules, AGENTS.md = coordination)
- Rarely-needed knowledge moved to skills, not kept in CLAUDE.md
