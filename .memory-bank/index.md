# Memory Bank — index

> Source of truth for project context. The `/plan` and `/execute` workflows and their agents read this first. Replace placeholders with real project facts. If another knowledge base is authoritative, link it here.

## Project

- **What:** <what is being built and for whom>
- **Goal:** <observable definition of success>
- **Stack:** <languages, frameworks, runtimes, and key services>

## Repository workflow

- **Install:** <real command or instructions>
- **Targeted checks:** <commands and when to use them>
- **Required full checks:** <tests, build, lint, typecheck, or validation commands>
- **Smoke check:** <real end-to-end command or procedure required before ship>

## Architecture and constraints

- Architecture/modules → `./architecture.md`
- Decisions already made → `./decisions.md`
- Open questions → `./open-questions.md`
- Security/operational constraints → <path or summary>

Create linked files only when useful. Keep this index concise and current; do not record secrets or unverified assumptions.
