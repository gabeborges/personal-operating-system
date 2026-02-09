---
description: "Update .claude/agents/swarm-config.md — tier DAG, agent roster, and auto-detection triggers"
---

# /sdd:swarm-config — Update Swarm Configuration

Create or update `.claude/agents/swarm-config.md` — the reference file for `/orchestrate` that maps agents to tiers and defines auto-detection triggers. Pass `$ARGUMENTS` for specific update scope.

## Process

1. Read current `.claude/agents/swarm-config.md` (if exists)
2. Read all `.claude/agents/*.md` to inventory swarm agents (exclude user-invoked-only agents)
3. Read `.claude/agents/instructions.md` for workflow context

4. Write/update `.claude/agents/swarm-config.md` with these sections:

### Required Sections
- **Agent Roster** — Table: Agent | subagent_type | Tier | Auto-detect trigger | Persistent?
- **Tier Dependency DAG** — ASCII diagram with sequential/parallel annotations
- **Execution Modes** — Table: Command | Tiers | Use Case
- **Auto-Detection Logic** — Pseudocode for bare `/orchestrate` mode selection
- **T2 Freshness Checks** — Skip conditions for each T2 agent
- **Auto-Detection Keywords** — Keyword lists for UI, security, compliance, database triggers
- **Teammate Prompt Template** — Full template with Communication Rules and Pre-loaded Artifacts sections
- **Message Protocol** — Summary routing rules (event-driven)

### Key Rules
- Only swarm agents go in the roster (user-invoked agents are excluded)
- `subagent_type` is always `general-purpose` for Task tool spawning
- Always-spawn agents: fullstack-developer, test-automator, qa, code-reviewer
- Conditional agents triggered by keyword matching in specs.md/tasks.yaml
- Tier 2 is sequential (data dependency chain); all other multi-agent tiers are parallel
- Context-manager is lazy (event-driven, not per-tier)
- Pre-load rule: only specs.md and tasks.yaml, only if under 150 lines

### Quality Gates
- Roster matches actual agent files in `.claude/agents/`
- No user-invoked-only agents in the roster
- Tier numbering is consistent across DAG, roster, and execution modes
- Teammate Prompt Template includes Communication Rules and Pre-loaded Artifacts
- Verify with Read tool after writing
