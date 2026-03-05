# GETTING STARTED

Welcome! This guide will help you set up this template project.

## 1. What is this template?
This is a production-ready Next.js + Supabase template optimized for AI-assisted development. It includes a structured `.ai` folder that gives AI agents (like Claude or Gemini) context about the project.

## 2. What is the `.ai/` folder?
This folder is the "brain" of the project. It contains:
- `WORKFLOW.md`: How to work with AI agents.
- `ARCHITECTURE.md`: Technical decisions.
- `tasks/`: Step-by-step plans for features.

**Rule #1:** Always update these files when the project changes.

## 3. Reading Order
1. `.ai/README.md` — Index of docs.
2. `.ai/WORKFLOW.md` — How to execute tasks.
3. `.ai/PROJECT_BRIEF.md` — Fill this first!

## 4. Setting up your environment
1. Install Node.js 20+ and Python 3.11+.
2. Install Supabase CLI: `npm install -g supabase`.
3. Install Desktop Commander (for AI): `npm install -g @wonderwhy-er/desktop-commander`.
4. Install Repomix (for context packing): `npm install -g repomix`.

## 5. Configuring Desktop Commander
If you use Claude Desktop, follow `.ai/DC_CONFIG_PROMPT.md` to set up the MCP tool.

## 6. Starting your first task
1. Create a plan in `.ai/tasks/phase-1/PHASE_PLAN.md`.
2. Create a step file using `.ai/STEP_TEMPLATE.md`.
3. Give the step file to the AI Executor.

## 7. Common mistakes to avoid
- **Don't write code in step files**: You are the Architect, let the AI write the code.
- **Don't read .env files in code**: Use `process.env` or `os.getenv()`.
- **Always use the full venv path**: For Python scripts in AI steps.
- **Don't use glob patterns in Repomix**: Be specific to save tokens.

## 8. Role System
- **Master (Architect):** Plans, writes docs, creates step files.
- **Executor (Developer):** Writes code, runs tests.
**Separate these roles into different chats!**

## 9. Context Contamination
If the AI gets confused or stuck, **start a new chat**. Don't try to fix a broken context.

## 10. Git Workflow
- Commit before every AI task (checkpoint).
- Commit after every successful step.
