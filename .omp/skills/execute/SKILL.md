---
name: execute
description: Implement, verify, diagnose, and review an approved owner-routed plan.
---

# Execute

Invocation: `/execute <slug>`, or ask OMP to follow `skill://execute`.

The main orchestrator is the strong-model integration owner. It owns routing, barriers, evidence, and the report; it does not delegate integration decisions.

## 1. Validate

1. Read `swarm-report/<slug>-plan.md`. If missing, stop blocked and direct the user to `/plan "<feature>"`.
2. Validate every affected item has a unique ID, exact scope, one owner in `code | tests | docs | devops`, resolvable acyclic dependencies, acceptance criteria, and real verification commands.
3. Stop blocked on unresolved blockers, ambiguous/shared ownership, missing prerequisites, or a missing repository-supported definition and command for smoke verification after implementation. Never require smoke results before implementation, and never narrow the plan to proceed.
4. Record all pre-existing changed and untracked paths before edits. Preserve them and exclude them from feature attribution while retaining overlapping-file context.
5. Create or update only `swarm-report/<slug>-execute.md` for execution evidence.

## 2. Implement by dependency wave

- Dispatch at most one instance of each needed writer role.
- `code` and `devops` may start concurrently only when dependencies and file scopes do not overlap.
- Dispatch `tests` and `docs` after the production or infrastructure contracts they depend on are stable; they may run concurrently when independent.
- Give each writer the complete plan, its exact item IDs/files, relevant contracts, dependencies, and pre-existing-path record.
- Writers edit only their assigned files and do not run builds, tests, linters, formatters, or smoke checks.
- Wait for all dispatched writers. A blocked writer makes the run blocked unless a reachable correction can resolve it.

## 3. Verify once after the barrier

Run the plan's targeted checks and all repository-required full checks against the integrated tree, sequentially where they can interfere. Capture for every command:

- exact command and working directory;
- exit code;
- concise actual output;
- criterion proved or failure observed.

Include a real smoke check for end-to-end behavior. No output, skipped checks, inferred success, or tests alone without required smoke evidence can yield `ship`.

## 4. Correct, at most twice

A correction round is consumed whenever a writer edits after the first verification barrier.

For a technical failure:

1. Send the read-only `debugger` the exact command, exit code, output, relevant scoped changes, environment facts, and plan criterion.
2. `cannot_reproduce`, `stuck`, or unsupported diagnosis ends non-ship.
3. Route the evidence-backed diagnosis and proposed minimal correction to the owning writer.
4. Re-run the same verification set, including smoke evidence.

After green verification:

1. Send the read-only `reviewer` the plan, feature-scoped patch and complete new-file contents, pre-existing-path exclusions, and verification evidence.
2. On `rework`, route each finding to the owning writer, then re-run the complete verification set and reviewer.
3. Stop after two correction rounds total. Remaining failures/findings produce `rework` or `blocked`.

## 5. Report

Continuously maintain `swarm-report/<slug>-execute.md` so interrupted or blocked runs retain evidence. It must contain:

```markdown
# Execute: <slug>
## Plan and validation
## Pre-existing changes
## Routing and dependency waves
## Changed files
## Verification evidence
## Debug and correction rounds
## Review
## Final status
ship | rework | blocked
```

Changed files must be feature-scoped and assigned to their owner; explicitly list excluded pre-existing paths. Include real commands, exit codes, output, reviewer findings, diagnoses, corrections, round count, unresolved risks, and the reason for final status. Never create separate build, review, or debug reports and never declare `ship` without green verification plus smoke evidence and reviewer approval.
