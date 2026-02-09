---
description: "Create or update .claude/agents/instructions.md for the current agent roster"
---

# /sdd:instructions — Update SDD Workflow Instructions

Create or update `.claude/agents/instructions.md` to match the current agent roster and SDD workflow. Pass `$ARGUMENTS` for specific update scope (e.g., "add new agent X" or "full rebuild").

## Process

1. Read current `.claude/agents/instructions.md` (if exists)
2. Read all `.claude/agents/*.md` to inventory the roster
3. Read `.claude/agents/swarm-config.md` for tier DAG and execution modes
4. Read `AGENTS.md` for coordination reference (avoid duplication)
5. Read `CLAUDE.md` for project rules (avoid duplication)

6. Write/update `.claude/agents/instructions.md` with these sections:

### Required Sections
- **General Guidance** — SDD workflow principles, canonical flow, definitions
- **SDD Artifact Flow** — Prerequisite rules table (artifact → prerequisite → on violation)
- **Swarm Orchestration** — Execution modes table, how-it-works steps, agent-to-teammate mapping, message protocol
- **Dependency DAG** — 5-tier structure matching swarm-config.md
- **Agent Inputs and Outputs** — Table with Role, Inputs (reads), Outputs (writes) per agent
- **Folder Structure** — `.ops/` directory tree
- **Gate Output** — checks.yaml location and purpose

### Quality Gates
- Must match current agent roster exactly (no stale agents listed)
- DAG must match `.claude/agents/swarm-config.md`
- No duplication with `CLAUDE.md` or `AGENTS.md` content
- Definitions section must use strict paths (`.ops/build/v{x}/...`)
- Verify with Read tool after writing
