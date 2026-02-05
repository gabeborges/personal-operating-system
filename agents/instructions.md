## General guidance
You finalized a deterministic, artifact-driven SDD workflow where:
* Specs are authoritative (`specs.md`), not code.
* Tasks are traceable via implements: pointers back to spec nodes.
* QA validates contracts, not just behavior.
* DB changes are gated by a "database-administrator" (no unsafe migrations).
* Security and compliance are first-class, with both by-design and audit roles.
* Claude Tasks act as the execution workspace, while repo artifacts are the source of truth.
* "workflow-orchestrator" agent closes the loop, routing implementation constraints back to Clavix/OpenSpec instead of allowing silent drift.
* "context-manager" persists state so work is resumable, auditable, and non-chatty.
* All implements: pointers in: `.ops/build/v{x}/<feature>/tasks.md` must reference nodes defined in: `.ops/build/v{x}/<feature>/specs.md`. Example: implements: `/paths/users/get`

In short: spec → system design → tasks → safe execution → validation → gated release → logged state → spec feedback if needed.

### Definitions
- `tasks.md` = feature-level tickets stored in `.ops/build/v{x}/<feature-name>/tasks.md` and consumed by coding agents
- `specs.md` = feature requirements + acceptance criteria stored in `.ops/build/v{x}/<feature-name>/specs.md` 

## Swarm Orchestration

The `/orchestrate <feature-path>` slash command executes the SDD build phase using a swarm of agents managed by the `workflow-orchestrator` as team leader.

### How it works

1. **Entry**: `/orchestrate .ops/build/v1/<feature>` reads `specs.md` and `tasks.md` from the feature workspace, and uses build context from `../prd.md` and `../../product-vision-strategy.md`
2. **Auto-detection**: Task and spec content is scanned for keywords to determine which optional agents to spawn (see `.claude/agents/swarm-config.md` for the full keyword table)
3. **Task creation**: Each ticket in `tasks.md` becomes a `TaskCreate` entry with `implements:` pointers preserved and `addBlockedBy` set per the tier DAG
4. **Tier-based spawning**: Agents spawn via the `Task` tool as `general-purpose` subagents, tier by tier:
   - T1: context-manager (always) → T2: spec-writer (if needed) → T3: project-task-planner (if needed) → T4: ui-designer, security-engineer, compliance-engineer (if detected, parallel) → T5: frontend-designer, database-administrator (if detected, parallel) → T6: fullstack-developer, test-automator (parallel) → T7: qa, code-reviewer, security-auditor, compliance-auditor, debugger (parallel)
5. **Gate checking**: Each tier must complete before the next starts. If any agent creates `spec-change-requests.md`, the orchestrator halts and notifies the user
6. **Completion**: All tasks verified complete, results summarized

### Agent-to-teammate mapping

Each agent's system prompt is read from `.claude/agents/<agent-name>.md` and combined with the feature workspace path to form the teammate's prompt. See `.claude/agents/swarm-config.md` for the full roster, tiers, and auto-detection triggers.

### Message protocol

- Teammates return their summary to the orchestrator when done
- Spec violations → `spec-change-requests.md` → orchestrator halts
- Context-manager appends all decisions to `decisions-log.md`
- `implements:` pointers flow from `tasks.md` into `TaskCreate` descriptions for traceability

## Agents team (final list)

| Subagent | Role | Inputs (reads) | Outputs (writes) | SDD workflow responsibility |
| --- | --- | --- | --- | --- |
| workflow-orchestrator | Orchestration + routing | `specs.md`, ``, `tasks.md`, `decisions-log.md`, repo status | Updates `tasks.md` ordering (optional), creates `spec-change-requests.md` when needed | Enforces the SDD sequence; triggers the right agents; routes spec breaks back to spec-writer/user update loop; ensures required gates ran |
| context-manager | Memory + decision log | Everything in `.ops/build/decisions-log.md` (what changed/decisions/follow-ups) | Maintains a stable “execution state” in-repo so each iteration starts where you left off |
| project-task-planner | Spec handoff / ticket writer | `specs.md`, Clavix intent summary (if stored), ``, existing `decisions-log.md` | `tasks.md` (tickets with `implements:` pointers), optional updates to `specs.md` skeleton | Parses OpenSpec and creates implementable tickets with `implements:` pointers + acceptance hooks |
| ui-designer | UX intent + flows | Clavix intent, `tasks.md` scope, existing UI patterns in repo | `ui.md` (flows, screens, states), optional notes in `specs.md` | Defines UX expectations that tasks + QA can verify (including accessibility intent) |
| frontend-designer | Design→implementation translator | `ui.md`, codebase components, `tasks.md` | Updates `ui.md` with component breakdown OR writes `components.md` UI section | Produces implementable component plan (components/props/states) to reduce dev ambiguity |
| fullstack-developer | Primary builder | `tasks.md`, `specs.md`, `ui.md`, `db-migration-plan.md` (if any), repo code | Code changes + tests; updates `components.md` when relevant; links in `.ops/build/decisions-log.md` | Implements tickets; keeps code aligned with `implements:` pointers; adds/updates tests; executes non-DB rollout steps |
| database-administrator (DB Change Guardian) | Migration strategist / safety gate | `specs.md` schema intent (relevant `implements:`), current DB schema, `tasks.md` | `db-migration-plan.md` (expand/contract/backfill/rollback), notes in `.ops/build/decisions-log.md` | Validates DB changes are production-safe; blocks destructive migration strategies; signs off plan before execution |
| qa | Contract validation + exploratory | `specs.md` (`implements:` pointers), `tasks.md`, `specs.md`, running app/test outputs | Updates `specs.md` (contract checks + manual scripts), may open issues in `tasks.md` | Validates responses match OpenSpec schemas; defines and re-runs acceptance/regression checks |
| test-automator *(optional but useful)* | Automated test implementer | `specs.md`, `tasks.md`, repo test setup | Test files + fixtures; notes in `.ops/build/decisions-log.md` | Converts acceptance/contract checks into runnable automated tests (unit/integration/e2e) |
| debugger | Root-cause investigator | Failing test logs, QA repro steps, recent diffs | Updates `tasks.md` with “Fix:” tickets; notes in `.ops/build/decisions-log.md` | Investigates failures; narrows root cause; produces concrete fix tasks (minimal, verifiable) |
| code-reviewer | Quality gate | PR diff, `tasks.md`, `specs.md`, relevant `specs.md` nodes | Review notes (commentary); may update `tasks.md` with required fixes; notes in `.ops/build/decisions-log.md` | Ensures implementation truly satisfies spec + acceptance criteria; flags maintainability/risk |
| security-engineer | Secure-by-design implementer | `tasks.md`, auth model, infra context, threaty endpoints | `security.md` (patterns/decisions), updates `specs.md` with security checks | Defines secure implementation patterns + required checks (authz, input validation, secrets) |
| security-auditor | Security reviewer | PR diff, `security.md`, runtime config, dependency list | Findings list (commentary); updates `tasks.md` with remediation tasks; notes in `.ops/build/decisions-log.md` | Independent security review + required remediation before “done” |
| compliance-engineer (new) | Compliance-by-design | `tasks.md`, `specs.md`, data flows/PHI assumptions | `compliance.md` (requirements), updates `specs.md` with compliance checks | Translates healthcare compliance needs into concrete technical requirements + acceptance checks |
| compliance-auditor | Compliance reviewer | `compliance.md`, PR diff, logs/audit trails, data handling | Findings list; updates `tasks.md` with remediation; notes in `.ops/build/decisions-log.md` | Independent review to ensure compliance requirements are actually implemented |

## File output folder structure

`.ops/ (product + global constraints)`
* product-vision-strategy.md  
* ui-design-system.md (if present)
* product-principles.md (not yet implemented)
* tech-stack-constraints.md (not yet implemented)
* system-invariants.md (not yet implemented)
* security-baseline.md (not yet implemented)
* compliance-baseline.md (not yet implemented)
* db-standards.md (not yet implemented)

`.ops/build/ (spans multiple versions)`
* design.md (product level system design)
* api-standards.md (not yet implemented)
* testing-strategy.md (not yet implemented)
* release-policy.md (not yet implemented)
* decision-log.md (optional)

`.ops/build/v{x}/ (version-level dev artifacts)`
* prd.md (Clavix)
* proposal.md (OpenSpec propose / solution approach)
* design.md (version-level system design)
*  (version-level epics and tasks)

`.ops/build/v{x}/<feature-name>/ (feature-level dev artifacts)`
* specs.md (OpenSpec feature spec; source for implements: pointers; feature-level contract checks + exploratory scripts)
* tasks.md (feature tickets; each ticket includes implements: pointers into specs.md)
* ui.md
* db-migration-plan.md (if DB changes)
* security.md (if needed beyond baseline)
* compliance.md (if needed beyond baseline)
* decisions-log.md (append-only “what changed/decisions/follow-ups”)
* spec-change-requests.md (when implementation constraints break spec)

### Gate Output (AI-first)
- `.ops/build/v{x}/<feature-name>/checks.yaml`: single YAML file with sections for security/compliance/qa/code_review/testing.

### System Design (product-level, evolving)
- `./ops/build/system-design.yaml`: AI-first system design for the whole product.
  - Updated after specs are generated for a build (`spec-writer` → `architect`).
  - If `architect` adds `spec_change_requests`, rerun `spec-writer` for impacted features.
