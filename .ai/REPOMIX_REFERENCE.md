# REPOMIX_REFERENCE — Repomix Cheat Sheet

> Covers 90% of tasks.
> Repomix packs repository files into one text for AI.

---

## Ready Commands

### Overview (Compressed)
```bash
cd [your-repo]
repomix --compress
```

### Targeted (Specific Files)
```bash
repomix --include "src/app/**/*.tsx,lib/**/*.ts" --style xml
```

### Documentation
```bash
repomix --include ".ai/**/*.md" --ignore ".ai/archive/**" --style xml
```

---

## ⛔ Token Budget 

**Problem:** Glob patterns (`**/*.py`) capture ALL files, wasting tokens.

**Rules:**

1. **Targeted Context (Mandatory):** List files by name. Budget ≤ 5,000 tokens.
2. **Overview Context (Optional):** Glob + `--compress`. Budget ≤ 3,000 tokens.
3. **Total:** ≤ 8,000 tokens.

**Check:**
```bash
repomix --token-count-tree
```

---

## Key Options

```bash
repomix --compress                 # ~70% savings
repomix --include "patterns"       # Only needed files
repomix --ignore "patterns"        # Exclude files
repomix --style xml                # XML format
repomix -o output.xml              # Output to file
repomix --copy                     # Copy to clipboard
repomix --token-count-tree         # Show token usage
```
