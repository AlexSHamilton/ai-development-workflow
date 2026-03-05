# WORKFLOW — Three Modes for Desktop Commander + Repomix

> How tasks are executed in [YOUR PROJECT NAME].
> This file is the operational manual. Read at the start of every new chat.

---

## Mode Selection

| Condition | Mode |
|-----------|------|
| DC available + task involving files/commands | **A** (Full Automation) |
| DC available + discussion/review/docs | **B** (Chat Only) |
| DC unavailable | **B** |

---

## Mode A — Claude + DC + Repomix

### ⛔ Role Boundaries

| | Master (Architect) | Executor (Developer) |
|---|---|---|
| **Creates Files** | ONLY `.ai/tasks/step-XX.md` | Code in repo (`.py`, `.tsx`, `.sql`, etc.) |
| **Writes Code** | ❌ FORBIDDEN (max: signature + pseudocode) | ✅ Full implementation |
| **Reads step-file** | Creates it | Reads and executes it |
| **Preflight Check** | Fills `<environment>` + `<data_contract>` | Checks sufficiency → STOP if missing |
| **Repomix** | Specifies command in step | Executes, reads context |
| **Git** | Does not commit | Checkpoint → code → diff → STOP |
| **Handover** | Reads result.md on close | Creates result.md before STOP |
| **Guessing** | ❌ Do not leave gaps in spec | ❌ Do not invent things not in spec |

**Rule:** Architect and Executor are DIFFERENT Claude chats. Do not combine.

### Master Role (Planning)
1. Read `ctx-master.md` → current phase, dependencies.
2. Read `ctx-{domain}.md` → domain state.
3. Formulate `step-XX.md` using `STEP_TEMPLATE.md` (XML tags inside markdown).
4. ⛔ DO NOT write ready-made code in the step file — only task description.
5. ⛔ DO NOT create code files on disk — only `.ai/tasks/`.
6. Include mandatory Repomix command (for code tasks).
7. Write to disk via DC: `.ai/tasks/phase-X/step-XX.md`.
8. Notify user: path to file + brief description.

### Executor Role (Execution)
1. Read `step-XX.md` via DC.
2. Read specified contexts.
3. **⚡ PREFLIGHT CHECK — Mandatory step before any work:**
   - Check `<environment>`: Is it clear where venv, repo, and `.env` are?
   - Check `<data_contract>`: Are specific tables and fields listed? Do they match the repomix context?
   - Check `<spec>`: Are all utilities/imports mentioned and visible in context?
   - **If anything is missing → STOP.** Write in chat:
     "⚠️ PREFLIGHT: Missing data to start work:
     1. [specifically what is missing]
     2. [specifically what is missing]
     Suggestion: [where to get it]. Confirm or clarify step file."
   - **If all OK** → Write "✅ PREFLIGHT OK" and proceed.
   - ⛔ **FORBIDDEN:** Inventing DB field names, file paths, environment variables. If not in step file — ask.
4. **Execute Repomix command** from `<repomix>` section — get code context.
5. **Git checkpoint:** `git add -A && git commit -m "checkpoint: before step-XX"`
6. Write code and execute task step-by-step.
7. After each significant action → brief report.
8. Execute QA tests from step file (including lint/types and file size check).
9. Show result + `git diff --stat`.
10. Check file size "before/after".
11. **Handover file:** Create `.ai/tasks/phase-X/step-XX-result.md` via DC with result: what was done (files), what FAILED, git diff summary, next step.
12. **STOP.** Wait for user confirmation.

### ⛔ Rules for Long DC Processes

**Long process** — any script with network requests or batches >100 elements.

**Forbidden:** Reading long process output via `read_process_output` line-by-line — burns tokens and drops connection.

**Launch Pattern:**
```bash
python3 pipeline/script.py --max-N 300 --quiet > /tmp/run.log 2>&1 \
  && echo "EXIT OK" && tail -5 /tmp/run.log
```

**`timeout_ms` ≤ 90 000** for any DC call with waiting.

**9-Minute Rule:** After ~9 minutes of total waiting without user response — **STOP**:
> "⏱ Working for ~9 minutes without confirmation. Type 'continue' — context saved, will resume."

Wait for user response. **Reason:** Cloudflare drops idle connections after ~10-15 mins; only user response keeps it alive.

### Master Role (Closing)
1. Receive from user: "step-XX done"
1a. Read handover file: `.ai/tasks/phase-X/step-XX-result.md` (full context without user retelling — saves tokens).
2. Update: `WORK_LOG`, `ctx-{domain}`, `DB_FIELD_REGISTRY` (if DB), `DECISIONS_LOG` (if decision).
3. Update `DOC_REGISTRY.md` — dates.
4. Mark `step-XX.md` as DONE.
5. If last step of phase → full audit via `DOC_REGISTRY` checklist.

---

## Mode B — Without DC

### User executes commands manually:
1. Master gives instructions in chat (or step file via copy-paste).
2. User executes commands in terminal.
3. User copies result to chat.
4. Claude analyzes, gives next instructions.

### When to use:
- DC broken / Docker not running.
- Task is pure architecture discussion.
- Saving tokens: last 20% of limit.
- Simple edits (1 file, < 10 lines).

---

## Stop Conditions (Built into every step)

Executor MUST stop if:
- **PREFLIGHT failed** — missing data (DB fields, paths, vars) → ask.
- Command failed with error (max 1 attempt to fix → STOP).
- Task requires changing more than 3 files (show list → wait for approval).
- Cannot do it with available tools → ask.
- QA tests failed after 2 attempts → STOP.
- New package installation required → ask user.

⛔ **FORBIDDEN to guess:**
- DB field/table names, if no `<data_contract>`.
- File paths, if not in step file or repomix context.
- Env vars, if not listed in `<environment>`.
- Data format, if not described in `<spec>`.
If missing — **STOP and ask**, do not improvise.

---

## Git Workflow

### Checkpoints (Automatic, via DC)
```bash
git add -A && git commit -m "checkpoint: before step-XX [name]"
```

### Final Commit (User, via GitHub Desktop)
- Commit text — from step file.
- User checks diff in GitHub Desktop.
- Push — only via GitHub Desktop.

### Rollback
```bash
# Soft (undo uncommitted changes)
git checkout .

# Hard (return to checkpoint)
git reset --hard HEAD

# Clean (remove new files)
git clean -fd
```

---

## Context Contamination Rule

If Claude fails twice on the same question and cannot correct itself:
1. STOP — do not push further.
2. Fix current state (git commit or stash).
3. Start NEW chat.
4. In new chat: "Read step-XX.md. Previous attempt failed due to [reason]. Start over."

---

## Token Economy Checklist

- [ ] Prompts → to disk (not chat).
- [ ] `edit_block` instead of `write_file`.
- [ ] `tail`/`grep`/`head` instead of full read.
- [ ] `repomix --compress` for overview.
- [ ] Contexts: Executor reads from disk.
- [ ] Long processes: `> /tmp/run.log 2>&1 && tail -5` — never read full log.
- [ ] `timeout_ms` ≤ 90 000 for every DC call.
- [ ] After ~9 mins total waiting — pause and request user confirmation.
- [ ] Results: to disk, return path.
- [ ] No more than 1-2 MCP servers.
- [ ] If context is long → new chat with handover.
