---
description: "Create a properly structured Claude Code skill (slash command) with frontmatter and body"
---

# /sdd:create-skill — Create Skill File

Create a new skill (slash command) in `.claude/commands/`. Input: skill description via `$ARGUMENTS`.

## Claude Code Mechanics — Skills

- Live in `.claude/commands/<name>.md` or `.claude/commands/<group>/<name>.md`
- YAML frontmatter supports: `description` (required), plus optional `model`, `tools`, `context`
- **Description** (in frontmatter) — always loaded into context every session (~2-5KB budget per skill). Keep it short (one sentence).
- **Body** (markdown below frontmatter) — loaded on-demand only when invoked. Put all detail here.
- Use `$ARGUMENTS` for dynamic user input
- Group related skills in subdirectories: `.claude/commands/<group>/<name>.md` → invoked as `/<group>:<name>`

## Process

1. Read `$ARGUMENTS` for skill name, purpose, and behavior
2. Read existing skills in `.claude/commands/` to understand conventions
3. Determine if this is a standalone skill or part of a group

4. Create the skill file:

```markdown
---
description: "{One-sentence description — keep SHORT, always in context}"
---

# /<name> — {Title}

{What this skill does. When to use it.}

## Process

1. {Step-by-step instructions}
2. {Use $ARGUMENTS for user input}
3. {Verification step}

## Quality Gates

- {What makes the output correct}
```

5. Verify with Read tool after writing

## Quality Gates

- Description in frontmatter: ONE short sentence (always loaded, costs tokens every turn)
- Body: detailed instructions (loaded on-demand, can be longer)
- `$ARGUMENTS` used for dynamic input where appropriate
- If part of a group: file goes in `.claude/commands/<group>/<name>.md`
- Verification step included in the process
- No application code in skill files — skills are instructions, not implementations
