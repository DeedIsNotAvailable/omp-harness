<!-- OMP-HARNESS:BEGIN -->
# OMP Development Harness

## Working agreement

- Accuracy comes before speed. Never claim success without real verification and smoke evidence.
- Read `.memory-bank/index.md` before planning or execution. If it is missing or incomplete, state that limitation; do not invent project facts.
- Follow repository-local instructions, manifests, lockfiles, tool versions, architecture, and established conventions.
- Disagree clearly when a request is unsafe, internally inconsistent, or unnecessarily broad; offer one concrete alternative.
- Prefer surgical edits to new abstractions. Add files only when the approved plan requires them.
- Write comments only for non-obvious invariants, not to narrate code.
- Validate external input at boundaries and never hardcode secrets.
- Do not perform destructive or externally visible actions without explicit user authorization.
- Preserve pre-existing user changes and keep them outside feature-scoped review evidence.
- The main OMP orchestrator is the integration owner. Start OMP with a strong model; model choice is intentionally not hardcoded.

## Development loop

The only user-facing development loop is:

1. `/plan "<feature>"` creates `swarm-report/<slug>-plan.md`.
2. Resolve any blockers in that plan.
3. `/execute <slug>` implements, verifies, reviews, and writes `swarm-report/<slug>-execute.md`.

The same workflows are discoverable as the `plan` and `execute` skills. There are no compatibility commands or report aliases.

## Roles and ownership

Only four agents may write project files:

- `code` (`@default`): production and runtime source only.
- `tests` (`@smol`): test source, fixtures, and test-only configuration.
- `docs` (`@smol`): user and developer documentation.
- `devops` (`@smol`): CI/CD, deployment, containers, hosting, infrastructure as code, and environment or secret wiring.

Every affected item in a plan has exactly one owner from `code | tests | docs | devops` and explicit dependencies. Shared files still receive one owner. Ambiguous ownership blocks execution; the orchestrator must not silently reassign or shrink scope.

Read-only weak roles are `planner`, `skeptic`, `reviewer`, and `debugger`, all on `@smol`. They may inspect and report but never edit project files. The owning writer applies every correction.

## Execution invariants

- Validate the plan schema and blockers before edits.
- Record pre-existing changed and untracked paths before dispatch; never attribute them to the feature.
- Dispatch `code` and `devops` as dependencies permit, followed by `tests` and `docs` once their relevant contracts are stable. Never let two agents write the same file.
- Wait at one barrier, then run targeted and project-required verification once against the integrated tree.
- A failing check goes to the read-only debugger with the exact command, exit code, and output; its diagnosis returns to the owning writer.
- Green verification goes to the read-only reviewer with the plan, feature-scoped patch/new-file content, and evidence.
- Review rework returns to the owning writer, then the same verification and review repeat.
- Allow at most two correction rounds total. Exhaustion, missing evidence, `cannot_reproduce`, unresolved blockers, or failed smoke evidence produces `rework` or `blocked`, never `ship`.
- `/execute` owns verification evidence. Its only execution artifact is `swarm-report/<slug>-execute.md`, with final status `ship | rework | blocked`.
<!-- OMP-HARNESS:END -->
