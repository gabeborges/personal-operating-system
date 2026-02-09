---
description: "Audit agent files for missing reads/writes, overlapping responsibilities, and governance gaps"
---

# /sdd:audit-agents — Audit Agent Files

Audit all agent definitions in `.claude/agents/` for quality and consistency. Optional: pass specific agent name(s) via `$ARGUMENTS` to audit a subset.

## Process

1. Read all `.claude/agents/*.md` files (or subset from `$ARGUMENTS`)
2. Read `.ops/analysis/agent-governance-report.md` if it exists (known gaps)
3. For each agent, check:

### Structural Checks
- [ ] YAML frontmatter present (name, description, category, tools)
- [ ] Has Reads, Writes, Rules, Process sections
- [ ] Has Escalation section
- [ ] Under 100 lines (except workflow-orchestrator)

### Contract Checks
- [ ] Every artifact in Reads exists or is a known SDD artifact path
- [ ] Every artifact in Writes exists or is a known SDD artifact path
- [ ] No Read/Write overlaps with other agents (except explicitly shared artifacts)
- [ ] Role boundaries are clear (does / does not)

### Governance Checks
- [ ] Agent is listed in `.claude/agents/swarm-config.md` roster (if swarm agent)
- [ ] Agent is NOT in swarm-config.md (if user-invoked only)
- [ ] Agent is listed in `AGENTS.md` roster
- [ ] Tier assignment matches dependency DAG logic
- [ ] No responsibility overlap with other agents in the same tier

### Quality Checks
- [ ] No keyword-list padding or filler content
- [ ] Rules are actionable, not aspirational
- [ ] Process steps are concrete and ordered

4. Report findings as a table:

```
| Agent | Issue | Severity | Recommendation |
|-------|-------|----------|----------------|
```

5. Flag any agents that should be merged, split, or removed
