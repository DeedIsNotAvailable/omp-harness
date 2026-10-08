# OMP Development Harness

A file-based, stack-neutral development loop for OMP: **`/plan` → `/execute`**. It uses native OMP skills, commands, project context, and agents. There is no wrapper CLI, hook, Claude Code adapter, or Codex compatibility layer.

## Quick start

Start OMP on this repository with a strong model, then follow `skill://setup` and provide the target project path. Setup uses OMP file tools rather than shell commands. It preserves existing Memory Bank content, reports, and unrelated project files while installing the explicit OMP asset manifest.

In the target project, use:

```text
/plan "add email login"
/execute add-email-login
```

The workflows are also discoverable as the `plan` and `execute` skills.

## The loop

### `/plan "<feature>"`

The orchestrator reads project context, then uses the weak read-only `planner` and `skeptic`. It writes `swarm-report/<slug>-plan.md` with observable acceptance criteria, exact affected items, dependencies, contracts, real verification commands, blockers, and one owner per item:

- `code` — production and runtime source;
- `tests` — tests, fixtures, and test-only configuration;
- `docs` — user and developer documentation;
- `devops` — CI/CD, deployment, containers, hosting, IaC, and environment/secret wiring.

Resolve plan blockers before execution. Unknown or conflicting ownership blocks the run.

### `/execute <slug>`

The strong main orchestrator remains integration owner. It validates the plan, records pre-existing changes, and dispatches writers by dependency: `code`/`devops` when their scopes are independent, then `tests`/`docs` once relevant contracts are stable. Writers do not run verification.

After one integration barrier, the orchestrator runs the real targeted and project-required checks once, including smoke evidence. Failures go to the weak read-only `debugger`; evidence-backed corrections return to the owning writer. Green evidence goes to the weak read-only `reviewer`. Review corrections follow the same owner route. The process allows at most two correction rounds.

The only execution artifact is `swarm-report/<slug>-execute.md`, containing routing, feature-scoped changes, excluded pre-existing paths, commands, exit codes, actual output, diagnoses, review, correction rounds, unresolved risks, and final status `ship | rework | blocked`. OMP must not claim `ship` without green verification, real smoke evidence, and reviewer approval.

## Models and roles

The parent/orchestrator model is intentionally not hardcoded. Start OMP with a strong model.

| Role | Access | Model | Responsibility |
|---|---|---|---|
| main orchestrator | integration owner | user-selected strong model | routing, barriers, verification, evidence, final status |
| `code` | writer | `@default` | production/runtime source only |
| `tests` | writer | `@smol` | tests and fixtures |
| `docs` | writer | `@smol` | documentation |
| `devops` | writer | `@smol` | delivery and infrastructure |
| `planner` | read-only | `@smol` | plan draft |
| `skeptic` | read-only | `@smol` | plan critique |
| `debugger` | read-only | `@smol` | evidence-backed diagnosis |
| `reviewer` | read-only | `@smol` | scoped review and verdict |

## Repository layout

```text
.omp/
  AGENTS.md
  commands/
    plan.md
    execute.md
  skills/
    setup/SKILL.md
    plan/SKILL.md
    execute/SKILL.md
  agents/
    code.md
    tests.md
    docs.md
    devops.md
    planner.md
    skeptic.md
    debugger.md
    reviewer.md
.memory-bank/
  index.md
swarm-report/
  .gitkeep
```

## Setup safety

`skill://setup` copies only an explicit manifest. Repeated installation is idempotent. Existing Memory Bank files and reports are never overwritten. In `.omp/AGENTS.md`, setup replaces only the content bounded by `<!-- OMP-HARNESS:BEGIN -->` and `<!-- OMP-HARNESS:END -->`; all content outside those markers is preserved. Exact legacy harness assets are retired only when their provenance is clear. Modified or uncertain legacy files are left in place and reported as blocking conflicts. Setup installs no settings, shell scripts, hooks, compatibility aliases, design workflow, mobile roles, or stack-specific roles.
