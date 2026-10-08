---
name: plan
description: Plan a multi-file feature for OMP's owner-routed /execute workflow.
---

# Plan

Invocation: `/plan "<feature>"`, or ask OMP to follow `skill://plan`.

The main orchestrator remains integration owner. Do not edit project files during planning.

1. Preserve the feature text verbatim and derive a short kebab-case `<slug>`.
2. Read `.memory-bank/index.md` when present, repository instructions, and only the files needed to understand the request. If the Memory Bank is absent or incomplete, record the limitation rather than inventing facts.
3. Dispatch the read-only `planner` and `skeptic` agents on `@smol`. The skeptic must critique the concrete draft. Neither agent may edit files.
4. Merge the draft and all supported high/medium findings. Do not silently reduce requested scope. Unresolved decisions become blockers.
5. Write exactly `swarm-report/<slug>-plan.md` with this schema:

```markdown
# Plan: <feature> (slug: `<slug>`)

## TL;DR

## Acceptance criteria
- <observable outcome, including smoke behavior>

## Affected items
| id | path or path pattern | change | owner | depends_on |
|---|---|---|---|---|
| P1 | `path` | exact intended change | code|tests|docs|devops | none or comma-separated IDs |

## Contracts
- <interfaces or ordering constraints shared between owners>

## Verification
| id | command | proves | depends_on |
|---|---|---|---|
| V1 | `<real repository command>` | <criterion> | <affected item IDs> |

## Blockers
- None.

## Out of scope

## Assumptions
```

Schema rules:

- Every affected item has one and only one owner from `code | tests | docs | devops`.
- `code` owns production/runtime source only; `tests` owns tests and fixtures; `docs` owns documentation; `devops` owns delivery and infrastructure.
- Dependencies must be acyclic and refer to declared item IDs.
- Every acceptance criterion maps to a real verification or smoke command. Never invent commands unsupported by the repository; make their discovery a blocker instead.
- A shared file receives one owner and a contract note, never multiple owners.
- Unknown/conflicting ownership, unresolved high-severity concerns, unavailable prerequisites, or missing verification are explicit blockers.

Report the slug, plan path, and blockers. The only next command is `/execute <slug>` after blockers are resolved or explicitly waived in the plan.
