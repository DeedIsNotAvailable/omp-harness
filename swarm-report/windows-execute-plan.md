# Plan: OMP-native `/plan` → `/execute` harness (slug: `windows-execute`)

## TL;DR

Replace the Claude Code, hook, design, mobile, and stack-specific harness with a clean OMP-native file layout. The main strong orchestrator owns integration and evidence; four explicit writers own production code, tests, docs, and delivery infrastructure; four weak roles remain read-only. `/execute` performs dependency-aware implementation, one verification barrier, diagnosis, scoped review, and at most two owner-routed correction rounds.

## Acceptance criteria

1. The only user development loop is `/plan "<feature>"` then `/execute <slug>`, also discoverable as OMP skills.
2. Native assets live under `.omp`: project context, commands, skills, and custom agents with valid `name`, `description`, and model frontmatter.
3. The four writers are `code`, `tests`, `docs`, and `devops`. `code` writes production/runtime source only and uses `@default`; the other writers use `@smol`.
4. `planner`, `skeptic`, `reviewer`, and `debugger` are read-only and use `@smol`.
5. Every affected plan item has exactly one writer owner and explicit dependencies. Ambiguous ownership or unresolved blockers stops execution.
6. `/execute` records pre-existing changes, dispatches code/devops as dependencies permit, then tests/docs after stable contracts, and waits at one integration barrier before verification.
7. Real targeted and project-required checks run once after the barrier and include smoke evidence. Failures receive read-only diagnosis; corrections return to the owning writer.
8. Review receives only feature-scoped changes/new-file contents and actual evidence. At most two correction rounds are allowed.
9. The sole execution artifact is `swarm-report/<slug>-execute.md` with final status `ship | rework | blocked`; `ship` requires green checks, smoke evidence, and reviewer approval.
10. Setup is OMP-native and shell-free, uses an explicit manifest, is idempotent, preserves Memory Bank/user files/reports, and retires only clearly identified exact legacy assets.
11. Root `AGENTS.md` and all `.claude` content are removed after applicable guidance is migrated to `.omp/AGENTS.md`.
12. README, Memory Bank template, ignore rules, agent responsibilities, and orchestration terminology agree. No design/mobile/stack-specific workflow or Claude Code/Codex compatibility claim remains.

## Affected items

| id | path | change | owner | depends_on |
|---|---|---|---|---|
| O1 | `.omp/AGENTS.md` | OMP context, ownership, evidence and correction invariants | docs | none |
| O2 | `.omp/commands/{plan,execute}.md` | native slash-command entry points using `$ARGUMENTS` and `skill://` | code | O1 |
| O3 | `.omp/skills/{plan,execute,setup}/SKILL.md` | owner-routed planning, execution state machine, shell-free installation | code | O1 |
| O4 | `.omp/agents/*.md` | four writers and four read-only roles with consistent models/scopes | code | O1,O3 |
| O5 | `README.md`, `.memory-bank/index.md`, `.gitignore` | document and template the OMP-only workflow | docs | O1-O4 |
| O6 | `AGENTS.md`, `.claude/**` | clean deletion after migration | devops | O1-O5 |
| O7 | `swarm-report/windows-execute-plan.md` | record this approved OMP contract | docs | O1-O6 |

## Contracts

- Parent model is selected by the user and must be strong; no provider/model is hardcoded.
- `code` inherits the parent via `@default`; every other custom agent uses `@smol`.
- Writers never run verification. `/execute` owns commands, exit codes, output, smoke evidence, review, and final status.
- Setup installs only its explicit manifest and never installs hooks or settings.

## Verification

Verification belongs to the main orchestrator after implementation. Required evidence is an inventory of exact native assets; a repository-wide negative scan for legacy commands, `.claude`, design/mobile/stack-specific claims, and compatibility claims; frontmatter inspection; and consistency review of plan/execute ownership and correction rules. No verification is performed by the implementation worker.

## Blockers

None.

## Out of scope

- Compatibility aliases or adapters for other hosts.
- Hooks, settings, or shell-based setup.
- Design/Figma/visual workflows.
- Mobile or framework-specific agents.
- Automatic removal of uncertain user-owned target files.

## Assumptions

- OMP resolves `.omp/AGENTS.md`, `.omp/skills/<name>/SKILL.md`, `.omp/commands/*.md`, and `.omp/agents/*.md` natively.
- `$ARGUMENTS`, `skill://<name>`, `@default`, and `@smol` have their documented OMP meanings.
- Historical reports other than this approved plan and `.gitkeep` are user-owned and preserved.
