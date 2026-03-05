# STEP_TEMPLATE — Step File Template

> Instructions for the Architect (Master Folder) when creating new tasks.
> Each step file MUST contain mandatory sections.
> Optional sections — add as needed.
> **Format:** Markdown + XML tags for structured sections (hybrid for readability + AI parsing).
> **Decision:** Preflight Check + data_contract + environment

---

## ⛔ HARD BANS FOR THE ARCHITECT

1. **DO NOT WRITE CODE in the step file.**
   Step = assignment for the Executor, NOT a ready-made solution.
   Allowed: pseudocode (3-5 lines), function signature, call example.
   Forbidden: ready-made working code, full function implementations.

2. **DO NOT CREATE code files on disk.**
   The Architect creates ONLY files in `.ai/tasks/`.
   The code is created by the Executor after reading the step file.

3. **DO NOT COMBINE roles.**
   Architect ≠ Executor. Even if "it's obvious how to write it" —
   the Architect's task is WHAT to do, not HOW (code).

4. **Repomix command IS MANDATORY** for any task involving code.
   The Executor must receive the context of the existing code.

5. **⛔ NEVER exclude a file from repomix** with the wording "executor has already seen it" or "familiar from past steps".
   Each Executor chat is a blank sheet. It does not remember previous steps.
   Repomix must contain ALL files on which the task directly depends.

---

## Template: Copy and Fill

```markdown
# step-XX — [Short Task Name]

<step_meta>
Status: TODO
Executor Folder: [2-DB | 3-Frontend | 4-Parsers | 5-AI]
Repo: [[your-web-repo] | [your-workers-repo]]
Mode: [A | B | C]
</step_meta>

<context>
Read before execution:
- `.ai/contexts/ctx-{domain}.md`
- [other files if needed]
</context>

<environment>
- **OS:** macOS (local development). NOT Docker, NOT Linux CI.
- **Python venv:** `/Users/[YOUR-USERNAME]/[YOUR-PATH]/venv/bin/python3`
- **Node/Next.js:** `/Users/[YOUR-USERNAME]/[YOUR-PATH]/[your-web-repo]/`
- **Environment Variables:** `.env` in repo root. Loaded automatically locally (venv activate / dotenv). **DO NOT** read `.env` programmatically or look for it — variables are already available via `os.getenv()`.
- **Supabase:** Local project via CLI (`supabase start`) OR cloud (URL/KEY in `.env`). Executor MUST NOT change connection string — use client utility.
- **⛔ DO NOT read `.env` via code, DO NOT parse `.env` files, DO NOT check for `.env`.** All variables are already in the environment.
- **⛔ DO NOT use system `python3`**. Always specify full path:
  ```bash
  # Template for all Python commands in step files:
  VENV_PY="/Users/[YOUR-USERNAME]/[YOUR-PATH]/venv/bin/python3"
  cd /Users/[YOUR-USERNAME]/[YOUR-PATH]/[your-workers-repo]
  PYTHONPATH=. $VENV_PY pipeline/script.py
  ```
</environment>

<data_contract>
<!-- MANDATORY for tasks working with DB. Architect MUST specify specific tables and fields. -->
<!-- Executor MUST NOT guess field names — they are listed here. -->

### Tables and fields used in this task:

**[table_name] — [action: READ / WRITE / UPSERT]:**
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| field_name | type | yes/no | description |

<!-- If task does NOT work with DB — write: "Task does not interact with DB." -->
<!-- Source of truth: `.ai/DB_FIELD_REGISTRY.md` — Architect copies needed fields HERE. -->
</data_contract>

<task>
[Specific description: what to do, in which file, what result is expected.
One or two sentences. If it doesn't fit — the task is too big, split it.]

⛔ This section describes WHAT to do, NOT containing ready-made code.
</task>

<repomix>
### Targeted Context (MANDATORY)
Only files that the task changes or directly depends on (2-7 files by name).
⛔ Glob patterns (`**/*.py`, `**/*.tsx`) ARE FORBIDDEN in targeted context.
```bash
cd /path/to/repo && repomix --include "path/to/file1.py,path/to/file2.py" --style xml -o /tmp/step-XX-context.xml
```

### Overview Context (Optional — if task touches new area)
Compressed folder overview to understand structure (signatures only, ~70% savings):
```bash
repomix --include "folder/**/*.py" --compress --style xml -o /tmp/step-XX-overview.xml
```

### Token Budget
- Targeted context: ≤ 5,000 tokens
- Overview context: ≤ 3,000 tokens
- Total: ≤ 8,000 tokens
- Check: `repomix --token-count-tree` before finalizing step file
- If exceeds → narrow down include or remove optional files

Executor MUST execute repomix command and read the result before writing code.
</repomix>

<steps>
1. `git add -A && git commit -m "checkpoint: before step-XX"`
2. **PREFLIGHT CHECK:**
   - Read ENTIRE step file + specified contexts
   - Check: are all tables/fields from `<data_contract>` clear?
   - Check: do all imports/utilities from `<spec>` exist in repomix context?
   - Check: is environment from `<environment>` clear?
   - **If anything is missing** → STOP. Write in chat:
     "⚠️ PREFLIGHT: Missing data to start work:
     1. [specifically what is missing / unclear]
     2. [specifically what is missing / unclear]
     Suggestion: [where to get / how to solve]. Confirm or clarify step file."
   - **If all OK** → write: "✅ PREFLIGHT OK — sufficient data, starting work."
3. Execute Repomix command, read context
4. [Specific action — WHAT to do, not HOW]
5. [Specific action]
6. Pause - briefly report result - request confirmation to proceed to next step.
7. Execute QA tests (section below)
8. Check file size before/after (DC pattern)
9. Show `git diff --stat`
10. Pause - briefly report result - request confirmation to proceed to next step.
11. Create handover file (step-XX-result.md)
12. **STOP — wait for user confirmation**
</steps>

<done_criteria>
- [ ] [what must be true after execution]
- [ ] [what must be true after execution]
</done_criteria>

<qa>
```bash
# Standard checks (include as appropriate):
# Lint (Next.js):  npx next lint
# Types (Next.js): npx tsc --noEmit
# Syntax (Python): python3 -m py_compile file.py
# Size before/after: wc -l file
[commands to verify result]
```
</qa>

<dc_size_check>
**Applicability:** All files >100 lines.
1. Before edit: `wc -l path/to/file` — remember
2. Execute edit
3. After edit: `wc -l path/to/file` — compare
4. If size decreased >20% → STOP, check for losses
</dc_size_check>

<commit_message>
[type](scope): [description]

- [detail]
- [detail]
</commit_message>

<handover>
Create `.ai/tasks/phase-X/step-XX-result.md`:
- What was done (specific files and changes)
- What FAILED and why
- git diff summary

⛔ **FORBIDDEN to write "Next step" in result file.**
Executor does not know the full plan — next step is defined by Architect via PHASE_PLAN.md.
</handover>

```

---

## Optional Sections

### Dependencies
```markdown
<dependencies>
- Requires: step-XX-1 (DONE)
- Blocks: step-XX+1
</dependencies>
```

### DC Commands
```markdown
<dc_commands>
```bash
start_process({ command: "cd ~/..." })
```
</dc_commands>
```

### Specification (for complex tasks)
```markdown
<spec>
Allowed to include:
- Function signature: `async def fetch_all_sources() -> dict`
- Call example: `stats = await fetch_all_sources(); print(stats)`
- Pseudocode (3-5 lines max)
- Data structure: `ProxyRecord(ip, port, protocol, country_code, source_name)`
- Link to sample: "by analogy with utils/supabase_client.py"

⛔ NOT allowed: full function implementation, ready-made working code >10 lines
</spec>
```

### Notes
```markdown
<notes>
[Context from discussions, ADR links, research]
</notes>
```

### Long Process (Mandatory for network scripts and batches)
<long_process>
**Applicability:** script makes network requests (httpx, aiohttp, Telethon) or processes batches >100 elements.
**⛔ Forbidden:** launch and read output via `read_process_output` — this burns tokens (httpx logs every request).

Mandatory script code requirements:
1. Flag `--quiet`: output ONLY final JSON `{"fetched":N,"alive":M,...}`, suppress httpx/asyncio logs
2. Flag `--max-N` for batch limit (e.g., `--max-proxies 300`, `--max-jobs 50`)
   - default: 0 (no limit) — for production
   - in tests ALWAYS specify limit

Mandatory launch pattern (Executor):
```bash
python3 pipeline/script.py --max-N 300 --quiet > /tmp/script.log 2>&1 \
  && echo "EXIT OK" && tail -5 /tmp/script.log
```
Executor reads ONLY `tail -5`. Never reads full log.

Logging setup in script:
```python
logging.getLogger("httpx").setLevel(logging.WARNING)
logging.getLogger("asyncio").setLevel(logging.WARNING)
```

**Connection Retention Rule (anti-timeout):**
- `timeout_ms` for any DC call — no more than 90 000 ms (90 sec)
- If process did not finish in 90 sec — write status to chat and continue with next call
- After ~9 minutes total waiting without user response — **MANDATORY PAUSE**:
  write: "⏱ Working for ~9 minutes without confirmation. Type 'continue' — context saved."
  Wait for response. User response resets counter and updates TCP connection.
- **Reason:** Cloudflare drops idle connection after ~10-15 mins of model silence;
  only messages **from user** to chat keep connection alive.
</long_process>
```

---

## Rules for Architect

1. **One task = 1-3 files, 1 function, < 300 lines of changes**
   If more — split into multiple step files

2. **Task is described in one sentence**
   "Create normalize_phone function in utils/phone_utils.py" — OK
   "Make the whole parser" — NO

3. **⛔ DO NOT WRITE CODE (repeat for reliability)**
   Step file = Spec. Code is written by Executor.
   Max: signature + pseudocode + call example (section `<spec>`).

4. **⛔ DO NOT CREATE code files on disk**
   Architect writes to disk ONLY `.ai/tasks/phase-X/step-XX.md`.

5. **Repomix MANDATORY for code tasks**
   Section `<repomix>` — mandatory for Folders 2-5. For Folder 1 — not needed.
   ⛔ **Glob patterns FORBIDDEN** in targeted context (`utils/**/*.py` → list files).
   **Token Budget:** targeted ≤ 5,000; overview (--compress) ≤ 3,000; total ≤ 8,000.
   Architect MUST check `repomix --token-count-tree` before writing step file.

   ⛔ **FORBIDDEN to exclude file from repomix with wording "executor already saw" or "executor familiar".**
   Each Executor chat starts from scratch — no memory of previous steps.
   Check before writing step file: "If executor has not seen a single line of project code — can they complete task with only this step + repomix context?"
   If no → add missing files. Exclude file ONLY when token budget exceeded (≤8,000), documenting reason explicitly.

6. **`<environment>`**
   Each step contains `<environment>` section with: OS, path to venv, path to repo,
   rule about `.env`. Can be copied from template — data is stable.

7. **`<data_contract>` MANDATORY for DB tasks**
   If task writes/reads DB — Architect MUST copy from `DB_FIELD_REGISTRY.md`
   specific fields into `<data_contract>` section. Executor **must not** search fields
   independently and **must not** invent names.

8. **Step 1 ALWAYS git checkpoint**
   Already built into template. Do not remove.

7. **Last step ALWAYS — STOP**
   Executor does not proceed to next task without confirmation.

8. **QA tests mandatory**
   Even for documentation: "check that file exists and is not empty"

9. **Commit text — ready to use**
   User copies to GitHub Desktop. Format: conventional commits.

10. **Handover file mandatory**
    Executor creates `step-XX-result.md` before STOP.
    Result file contains only: what was done, what failed, git diff summary.

11. **Non-standard task**
    If task doesn't fit template — add to beginning:
    `## ⚠️ Non-standard format` and explain why.
