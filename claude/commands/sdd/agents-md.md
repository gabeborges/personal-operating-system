---
description: "Create or update AGENTS.md — agent coordination reference for orchestrators"
---

# /sdd:agents-md — Update AGENTS.md

Create or update `AGENTS.md` at project root. This is a project convention (not built-in to Claude Code) — serves as agent coordination reference read on-demand by orchestrators. Pass `$ARGUMENTS` for specific update scope.

## Key Principle

**CLAUDE.md = project rules (auto-loaded every turn). AGENTS.md = agent coordination (read on-demand).**

Never duplicate content between them. If something is in CLAUDE.md, don't repeat it in AGENTS.md.

## Process

1. Read current `AGENTS.md` (if exists)
2. Read `CLAUDE.md` to identify content that must NOT be duplicated
3. Read all `.claude/agents/*.md` to inventory the roster
4. Read `.claude/agents/swarm-config.md` for tier DAG and execution modes
5. Read `.claude/agents/instructions.md` for workflow details

6. Write/update `AGENTS.md` with these sections:

### Required Sections
- **SDD Artifacts** — Priority-ordered table with paths and purposes
- **Strict Definitions** — Artifact path definitions
- **Folder Structure** — `.ops/` directory tree
- **Workflow Discipline** — SDD artifact flow, prerequisite rules, spec-change request process
- **Stop Conditions** — When to ask before proceeding
- **Definition of Done** — What makes a ticket complete
- **Agent Roster** — Table (name, role, category, file path)
- **By Category** — Agents grouped by category with one-line descriptions
- **Dependency DAG** — 5-tier structure
- **Agent Selection Guide** — Situation → agent mapping table
- **Swarm Orchestration** — Execution modes and slash commands
- **Parallel Execution Strategy** — Within-tier, within-feature, cross-feature parallelism

### Quality Gates
- No duplication with CLAUDE.md
- Agent roster matches actual files in `.claude/agents/`
- DAG matches `.claude/agents/swarm-config.md`
- Selection guide covers all common scenarios
- Verify with Read tool after writing
