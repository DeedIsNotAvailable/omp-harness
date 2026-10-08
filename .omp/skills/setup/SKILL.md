---
name: setup
description: Install or upgrade this OMP-native harness in a target project using OMP file tools only.
---

# Setup

Install this repository's harness into a user-specified target project. This workflow is shell-free: use OMP `read`, `write`, `edit`, `glob`, and directory-aware file tools only. Never invoke Bash, PowerShell, `cp`, `copy`, `rm`, or recursive directory copying.

## Explicit install manifest

Copy these source files to the identical relative paths in the target:

- `.omp/AGENTS.md`
- `.omp/commands/plan.md`
- `.omp/commands/execute.md`
- `.omp/skills/plan/SKILL.md`
- `.omp/skills/execute/SKILL.md`
- `.omp/skills/setup/SKILL.md`
- `.omp/agents/planner.md`
- `.omp/agents/skeptic.md`
- `.omp/agents/reviewer.md`
- `.omp/agents/debugger.md`
- `.omp/agents/code.md`
- `.omp/agents/tests.md`
- `.omp/agents/docs.md`
- `.omp/agents/devops.md`

Also install `.memory-bank/index.md` and `swarm-report/.gitkeep` only when the target path does not exist. Never overwrite Memory Bank content or existing reports.

## Procedure

1. Resolve and inspect the target with file tools. Reject a missing/non-directory target or a target equal to this harness source.
2. Read every manifest source and the corresponding target before writing. Create missing parent directories through file writes.
3. Merge `.omp/AGENTS.md` as a single managed block; never replace the whole target file:
   - Require the source to contain exactly one valid pair of standalone marker lines, `<!-- OMP-HARNESS:BEGIN -->` followed by `<!-- OMP-HARNESS:END -->`. The complete source block includes both marker lines. The managed payload is the content between them.
   - Inspect the target for both exact markers and any `OMP-HARNESS` marker-like text before writing. Exactly one standalone begin marker followed by exactly one standalone end marker is the only valid marked form. Duplicate, nested, reversed, malformed, or one-sided markers are a blocking conflict: preserve the target unchanged and report it.
   - For a valid target pair, replace only the content between the two target markers with the source managed payload. Preserve the target marker lines and every byte before the begin marker and after the end marker.
   - With no markers or marker-like text, identify any level-one `# OMP Development Harness` section. For migration, the only recognized prior unmarked payload is the source managed block with the two marker lines removed. Migrate only when exactly one such target section equals that recognized payload byte-for-byte, including whitespace and line endings; replace that section with the complete marked source block. Do not normalize or fuzzy-match it.
   - If an unmarked `# OMP Development Harness` section is absent, append the complete marked source block, adding only the line break needed to separate it from existing content. If such a section exists but is not the exact recognized prior payload, preserve the file and report a blocking conflict.
   - Apply the same rules on every run so an unchanged installation is idempotent and never duplicates the managed block.
4. Copy all other managed manifest assets exactly. Re-running setup must produce no duplicate content and no unrelated changes.
5. Retire only these exact legacy harness paths if present and recognizable as this harness: root `AGENTS.md`; `.claude/settings.json`; `.claude/design-gate.json.example`; `.claude/hooks/test-gate.sh`; `.claude/hooks/visual-gate.sh`; legacy `.claude/skills/{setup,plan,build,review,debug,design-cover,visual-verify}/`; and legacy `.claude/agents/{planner,skeptic,reviewer,debugger,devops,backend,frontend,mobile,react-ts,node-ts,python-fastapi,flutter,ios,android,terraform-yandex,design-reader,visual-critic}.md`.
6. Before deleting a legacy file, read it and establish that it is a harness asset. If provenance is uncertain or it contains user modifications, do not delete it: report the exact conflict and leave cleanup blocked for human resolution. Preserve unknown `.claude` files, user hooks, user project instructions, Memory Bank files, reports, and user-owned design data.
7. Remove empty legacy directories only when the file tool supports safe directory deletion and inspection proves they are empty. Never recursively delete an unknown directory.
8. Report installed, updated, preserved, retired, and conflicted paths. Conflicts make setup incomplete; do not claim success.

The installed user loop is `/plan "feature"` then `/execute slug`. Do not install compatibility aliases, Claude settings/hooks, OMP hooks, or any design/mobile/stack-specific assets.
