# FILE EDITING CONVENTIONS

> Updated: 2026-02-23

---

## 1. Access Check

Model must verify access to:
```
/Users/[YOUR-USERNAME]/[YOUR-PATH]/[your-repo]
```

## 2. DC Editing Rules

### Priorities:

1. **`edit_block`** — FOR ANY CHANGES in existing files.
2. **`write_file`** — ONLY for creating NEW files.
3. **`read_file`** with `offset`/`length` — for large files.
4. **`tail`/`head`/`grep`** via `start_process` — for logs.

### Critical Rule: `edit_block` over `write_file`

WRONG: Rewriting 500-line file for 3 lines of change.
RIGHT: `edit_block` with exact fragment.

### Limits
- `fileWriteLineLimit=50` — write_file limited to 50 lines (chunk larger files!).
- `fileReadLineLimit=1000` — read_file max 1000 lines.

---

## 3. Fallback: Shell Script

If DC is unavailable:
- Model generates a Shell Script to create files.
- Uses absolute paths.
- User runs script in terminal.
