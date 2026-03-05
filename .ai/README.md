# .ai/ Documentation System

> The brain of your project. This folder contains all context, rules, and history for AI assistants.

## File Index

| File | Purpose |
|------|---------|
| `PROJECT_BRIEF.md` | High-level overview: what we are building, for whom, and why. |
| `ARCHITECTURE.md` | Technical stack, diagrams, and structural decisions. |
| `CONVENTIONS.md` | Coding standards, hard bans, and patterns to follow. |
| `DECISIONS_LOG.md` | ADRs (Architecture Decision Records) — history of "why". |
| `WORKFLOW.md` | How we work: roles (Architect/Executor), modes (A/B/C), process. |
| `STEP_TEMPLATE.md` | The mandatory template for creating new tasks (step files). |
| `DOC_REGISTRY.md` | Map of all documentation files and their freshness status. |
| `DB_FIELD_REGISTRY.md` | Source of truth for database schema and field ownership. |
| `WORK_LOG.md` | Chronological log of all completed phases and tasks. |
| `RUNBOOK.md` | Commands for setup, running, and deployment. |
| `DC_REFERENCE.md` | Cheat sheet for Desktop Commander tools. |
| `REPOMIX_REFERENCE.md` | Cheat sheet for Repomix (context packing). |
| `contexts/` | Dynamic context files for specific domains (Master, DB, Frontend, etc.). |
| `tasks/` | Step files for specific tasks (active and history). |

## How to use with Claude / AI

1. **New Chat?** always start by reading `.ai/contexts/ctx-master.md` and `.ai/WORKFLOW.md`.
2. **Planning a task?** Act as Architect. Read `PROJECT_BRIEF.md` and `ARCHITECTURE.md`. Create a step file in `.ai/tasks/` using `STEP_TEMPLATE.md`.
3. **Executing a task?** Act as Executor. Read the step file and `.ai/CONVENTIONS.md`.
4. **Finished a task?** Update `WORK_LOG.md`, `DOC_REGISTRY.md`, and relevant context files.

## Reading Order for New Projects

1. `PROJECT_BRIEF.md` — Understand the goal.
2. `ARCHITECTURE.md` — Understand the system.
3. `WORKFLOW.md` — Understand the process.
4. `CONVENTIONS.md` — Understand the rules.
5. `RUNBOOK.md` — Get it running.

## ⚠️ Warning

Always update these files **immediately** when decisions change.
If the code contradicts the documentation, the documentation is "buggy" and must be fixed.
