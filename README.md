# Provider-Agnostic AI Development Workflow

> **"Files are the brain. The chat window is ephemeral."**

This repository is a production-ready reference implementation of a **Provider-Agnostic AI Development Workflow**. It effectively turns any LLM (Claude, Gemini, OpenAI) into a stateless reasoning engine that operates on a stateful project filesystem.

This system was born out of necessity when Claude Desktop went down for 3 days, proving that relying on a single provider's chat history is a critical failure point for professional engineering. 

---

## 📖 Table of Contents

- [The Core Philosophy](#-the-core-philosophy)
- [The Three Problems Solved](#-the-three-problems-solved)
- [The Stack](#-the-stack)
- [Directory Structure (.ai/)](#-the-ai-directory-structure)
- [Roles & Responsibilities](#-roles--responsibilities)
- [The Workflow (Handoff Protocol)](#-the-workflow-handoff-protocol)
- [One-Line Boot & Fallback](#-one-line-boot--fallback-strategy)
- [The Step File Contract](#-the-step-file-contract)
- [Repomix & Context Strategy](#-repomix--context-strategy)
- [Why Gemini CLI?](#-why-gemini-cli)
- [Quick Start](#-quick-start)

---

## 🧠 The Core Philosophy

If your workflow depends on a single provider (like Claude) being online, you are fragile. If your project state lives in a chat window history, you are losing data.

**The Solution:** Move **all** project state—plans, decisions, rules, context, and logs—out of the chat window and into files on disk.

- **The Agent is Stateless:** It starts every session fresh. It reads its "operating system" from files.
- **The Project is Stateful:** The `.ai/` directory contains the entire memory of the project.
- **Providers are Interchangeable:** Since the context is in files, you can switch from Claude to Gemini to OpenAI just by pointing a different model at the same files.

---

## 🎯 The Three Problems Solved

### 1. Context Loss (Agent Amnesia)
Long chat sessions degrade. The model forgets constraints you set 40 messages ago.
*   **Fix:** Every session loads fresh context from the `.ai/` directory. No memory degradation.

### 2. Vendor Lock-In
If Claude is down, your work stops.
*   **Fix:** The workflow is just text files. Switch to Gemini CLI, point it at the same files, and continue exactly where you left off.

### 3. Scalability
Complex features don't fit in one chat context.
*   **Fix:** Work is broken down into atomic `step-XX.md` files. Each step is verifiable and independent.

---

## 🛠 The Stack

There are four pieces that make this work:

1.  **[Desktop Commander](https://github.com/wonderwhy-er/DesktopCommanderMCP) (DC):** An MCP server that gives the agent real filesystem access, terminal control, and safety guardrails. It allows agents to read/write files and run tests.
2.  **[Repomix](https://github.com/yamadashy/repomix):** Packs repository context into optimized prompt bundles. Instead of dumping your entire codebase into the chat, Repomix lets you surgically include *only* the files relevant to the current task.
3.  **The `.ai/` Directory:** The brain of the project. Every document that matters lives here.
4.  **[Gemini CLI](https://github.com/google-gemini/gemini-cli) / Claude Desktop:** The reasoning engine. Interchangeable logic processors.

---

## 📂 The `.ai/` Directory Structure

This repository includes the complete scaffold. Copy the `.ai` folder to your project to inherit this "operating system".

```text
PROJECT_ROOT/
├── .ai/
│   ├── ARCHITECTURE.md          # High-level system design & stack decisions
│   ├── CONVENTIONS.md           # Coding standards, bans, and patterns
│   ├── DECISIONS_LOG.md         # ADRs (Architecture Decision Records)
│   ├── DOC_REGISTRY.md          # Map of all documentation files & freshness
│   ├── PROJECT_BRIEF.md         # What are we building and why?
│   ├── RUNBOOK.md               # Common CLI commands for setup/deploy
│   ├── STEP_TEMPLATE.md         # Strict template for task definitions
│   ├── WORK_LOG.md              # Chronological log of completed work
│   ├── WORKFLOW.md              # The rules of the game (Roles & Modes)
│   │
│   ├── contexts/                # ROLE DEFINITIONS ("Personas")
│   │   ├── ctx-master.md        # Dynamic context for the Architect
│   │   ├── project_instructions-master.md         # Architect Activation (Claude)
│   │   ├── project_instructions-master_gemini.md  # Architect Activation (Gemini)
│   │   ├── project_instructions-dev.md            # Executor Activation (Claude)
│   │   └── project_instructions-dev_gemini.md     # Executor Activation (Gemini)
│   │
│   └── tasks/                   # STATE MANAGEMENT
│       ├── BACKLOG.md
│       ├── phase-1/
│       │   ├── PHASE_PLAN.md
│       │   ├── step-01.md         # Task definition (INPUT)
│       │   └── step-01-result.md  # Task completion report (OUTPUT)
```

---

## 👥 Roles & Responsibilities

This workflow mimics a human engineering team. You act as the manager passing files between two distinct AI roles.

### 1. The Architect (The Brain)
*   **Goal:** Plan *what* to do, not *how* to write code.
*   **Responsibilities:**
    *   Reads `WORK_LOG.md` and `BACKLOG.md`.
    *   Breaks features into atomic, verifiable steps.
    *   Writes `step-XX.md` files to disk.
    *   **NEVER writes code.**
*   **Why:** When Architects write code, they hallucinate. They must focus on high-level design.

### 2. The Executor (The Hands)
*   **Goal:** Execute one atomic task perfectly.
*   **Responsibilities:**
    *   Activates by reading the Executor instructions.
    *   Receives one `step-XX.md` file.
    *   Runs the **Preflight Check** (validates inputs).
    *   Runs **Repomix** to get fresh code context.
    *   Writes code, runs tests, fixes errors.
    *   Reports results in `step-XX-result.md`.
*   **Why:** Executors have a narrow context window focused only on the files they need to change.

---

## 🔄 The Workflow (Handoff Protocol)

Here is the exact sequence of a working session:

1.  **Architect Session:** You ask the Architect to create `step-01.md`.
2.  **Handoff:** You copy the path to that file (e.g., `.ai/tasks/phase-1/step-01.md`).
3.  **Executor Session:** You open a **new** chat (cleaning context), activate the Executor, and tell it to run the step file.
4.  **Execution:** The Executor does the work, runs tests, and writes `step-01-result.md`.
5.  **Review:** You go back to the Architect session, say "Task done", and point to the result file.
6.  **Loop:** The Architect reads the result, updates documentation (`DOC_REGISTRY`, `WORK_LOG`), and plans the next step.

**The User's Role:** You are the messenger. You don't copy-paste code. You just pass file paths.

---

## 🔌 One-Line Boot & Fallback Strategy

This is what makes the system portable. To start any session, you give the agent a single line pointing to its "persona" file.

### Primary: Claude Desktop
> **"Read instructions and context from .ai/contexts/project_instructions-master.md"**

### Fallback: Gemini CLI
> **"Read instructions and context from .ai/contexts/project_instructions-master_gemini.md"**

### 🛡️ Fallback Playbook (When Claude Goes Down)
1.  **Switch Tools:** Open your terminal and launch `gemini`.
2.  **Activate:** Run the activation prompt for **Gemini**.
3.  **Execute:** The Gemini agent reads the **exact same** `step-XX.md` file. It doesn't know Claude wrote it.
4.  **Report:** The Gemini agent writes the **exact same** `step-XX-result.md` file.
5.  **Resume:** When Claude returns, feed it the result file. It continues seamlessly.

---

## 📝 The Step File Contract

The `step-XX.md` file is a formal contract between Architect and Executor. It is structured to eliminate lazy behavior.

```markdown
# step-01 -- [Task Name]

<environment>
HARDCODED PATHS to venv, node_modules.
Prevents the agent from asking "how do I run this?".
</environment>

<data_contract>
Specific database fields/tables involved.
COPIED from DB_FIELD_REGISTRY.md by the Architect.
Eliminates guessing column names.
</data_contract>

<task>
High-level definition of WHAT to do (not HOW).
</task>

<repomix>
The EXACT command to generate context:
`repomix --include "src/components/Header.tsx" --style xml`
</repomix>

<steps>
1. git checkpoint
2. PREFLIGHT CHECK (Mandatory: verify inputs)
3. Execute Repomix
4. Implementation...
5. Verification (QA)
6. Write result file
</steps>
```

---

## 📦 Repomix & Context Strategy

Naive agents fail because they load too much (wasteful) or too little (hallucinations) context. Repomix solves this with **Surgical Context Loading**.

The Architect defines exactly which files are needed. The Executor runs the command to get the *latest* version of those files.

**Token Budget Targets:**
- **Targeted Context:** < 5,000 tokens (Full source code).
- **Overview Context:** < 3,000 tokens (Compressed/AST-only via `--compress`).
- **Total per Step:** < 8,000 tokens.

This keeps costs predictable and agent attention focused.

---

## 💎 Why Gemini CLI?

While Claude is excellent, Gemini CLI is the perfect fallback (and often primary) tool because:

1.  **Actually Free:** It authenticates with your Google Account (`gemini auth`). No API keys, no credit card required for the standard tier.
2.  **High Limits:** You can comfortably run ~25 full development cycles (Executor runs) per day on the free tier (`gemini-2.5-flash` or `gemini-3-flash-preview`).
3.  **Fast:** Flash models are incredibly fast for code tasks.

---

## ⚡ Quick Start

### 1. Prerequisites
*   **[Desktop Commander](https://github.com/wonderwhy-er/DesktopCommanderMCP):** `npm install -g @wonderwhy-er/desktop-commander`
*   **[Repomix](https://github.com/yamadashy/repomix):** `npm install -g repomix`
*   **[Gemini CLI](https://github.com/google-gemini/gemini-cli):** Follow the installation guide for free-tier usage.

### 2. Setup
Clone this repository or copy the `.ai` folder to your project.
```bash
git clone https://github.com/AlexSHamilton/ai-development-workflow.git
cd ai-development-workflow
```

### 3. Initialize
Fill in your project details:
- Edit `.ai/PROJECT_BRIEF.md` (What are you building?)
- Edit `.ai/ARCHITECTURE.md` (What is your stack?)
- Edit all other files in `.ai/` directory.

### 4. Run Your First Task

**Architect Session:**
Open your AI and paste:
> "Read instructions from .ai/contexts/project_instructions-master.md"

Ask it to create `step-01.md`.

**Executor Session:**
Open a **new** chat window and paste:
> "Read instructions from .ai/contexts/project_instructions-dev.md, then execute .ai/tasks/phase-1/step-01.md"

---

## ⚠️ Known Risks & Mitigations

| Risk | Mitigation |
|------|------------|
| **Documentation Drift** | Make "Renew Documentation" a mandatory final step for the Architect. |
| **Lazy Executors** | The `STEP_TEMPLATE.md` enforces explicit numbered steps. Step 2 is Preflight. Step 3 is Repomix. |
| **File Overwrites** | Prefer `edit_block` over `write_file`. Use `DC_SIZE_CHECK` to prevent deleting code. |

---

*This workflow is opinionated, battle-tested, and built to survive the reality of AI development.*
