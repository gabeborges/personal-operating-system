---
description: "Create a properly structured SDD agent file with frontmatter and concise sections"
---

# /sdd:create-agent — Create Agent File

Create a new agent definition in `.claude/agents/`. Input: role description via `$ARGUMENTS`.

## Claude Code Mechanics — Agent Files

- Live in `.claude/agents/` as individual markdown files
- YAML frontmatter (name, description, category, tools) + markdown body = system prompt
- Agents do NOT auto-inherit CLAUDE.md — must be explicitly referenced via `@` import
- Each agent gets a fresh, isolated context window when spawned
- The entire file becomes the system prompt — every line costs tokens

## Process

1. Read `$ARGUMENTS` for role description and responsibilities
2. Read existing agents in `.claude/agents/` to understand conventions
3. Create the agent file with this structure:

```markdown
---
name: "{Agent Name}"
description: "{One-line role description}"
category: "{planning|design|implementation|quality|security|compliance|orchestration}"
tools: {comma-separated tool list}
---

{One-sentence role summary. What it does and does NOT do.}

## Reads
- {artifact paths this agent reads}

## Writes
- {artifact paths this agent writes}

## Rules
**Must do**:
- {clear, actionable rules}

**Must NOT do**:
- {explicit boundaries}

## Process
{Step-by-step workflow}

## Escalation
- {When to stop and ask}
```

4. Verify with Read tool after writing

## Quality Gates

- **< 100 lines** for most agents (orchestrator is the exception)
- Concise + actionable (follow developer.md pattern), NOT keyword-list padding
- Clear boundaries: what it does / does not do
- Every Read/Write must reference a specific artifact path
- Escalation section required
- If agent is part of swarm: update `.claude/agents/swarm-config.md` roster and tier DAG
- If agent is user-invoked only: do NOT add to swarm-config.md
