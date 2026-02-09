---
description: "Audit SDD workflow files for consistency across instructions.md, swarm-config.md, AGENTS.md, and agents"
---

# /sdd:audit-workflow — Audit SDD Workflow Consistency

Check consistency across all SDD workflow configuration files. Pass `$ARGUMENTS` to focus on specific areas (e.g., "tier assignments" or "agent roster").

## Process

1. Read all source files:
   - `.claude/agents/swarm-config.md` — canonical tier DAG and roster
   - `.claude/agents/instructions.md` — workflow instructions and agent I/O table
   - `AGENTS.md` — agent coordination reference
   - All `.claude/agents/*.md` — actual agent definitions

2. Cross-reference checks:

### Roster Consistency
- [ ] Every agent file in `.claude/agents/` is listed in AGENTS.md roster
- [ ] Every agent in AGENTS.md roster has a corresponding file
- [ ] Every swarm agent is listed in swarm-config.md roster
- [ ] No user-invoked-only agents in swarm-config.md roster
- [ ] Agent names, roles, categories match across all files

### DAG Consistency
- [ ] Tier assignments match between swarm-config.md, instructions.md, and AGENTS.md
- [ ] Tier numbering is consistent (no gaps, no mismatches)
- [ ] Sequential/parallel annotations match across all DAG representations
- [ ] Always-spawn list matches across all files

### Artifact Flow Consistency
- [ ] Prerequisite rules match between instructions.md and AGENTS.md
- [ ] Agent Reads/Writes in instructions.md match agent file definitions
- [ ] Execution modes match between swarm-config.md, instructions.md, and AGENTS.md

### Content Duplication
- [ ] No duplication between CLAUDE.md and AGENTS.md
- [ ] No duplication between instructions.md and AGENTS.md (cross-references OK)
- [ ] Definitions (specs.md, tasks.yaml paths) consistent everywhere

3. Report findings:

```
| Source A | Source B | Inconsistency | Severity | Fix |
|----------|----------|---------------|----------|-----|
```

4. Provide specific fix instructions for each inconsistency
