# DC_REFERENCE — Desktop Commander (Cheat Sheet)

> Covers 90% of daily tasks.
> Project: [YOUR PROJECT NAME]. Env: Native MacOS, venv activated.

---

## Config

File: `~/Library/Application Support/Claude/claude_desktop_config.json`

```json
{
  "mcpServers": {
    "desktop-commander": {
      "command": "/bin/bash",
      "args": [
        "-c",
        "source /Users/[YOUR-USERNAME]/[YOUR-PATH]/venv/bin/activate && npx @anthropic-ai/desktop-commander"
      ],
      "env": {
        "HOME": "/Users/[YOUR-USERNAME]",
        "PATH": "/Users/[YOUR-USERNAME]/[YOUR-PATH]/venv/bin:/usr/local/bin:/usr/bin:/bin"
      }
    }
  }
}
```

---

## Blocked Commands

`rm -rf`, `sudo`, `git push`, `ssh`, `curl`, `vim`, `nano`, `python3 -c`...

---

## Limits

- `fileWriteLineLimit`: 50 (chunk large files!)
- `fileReadLineLimit`: 1000

---

## Top Tools

### 1. `read_file`
```
read_file({ path: "/Users/[YOUR-USERNAME]/[YOUR-REPO]/README.md" })
read_file({ path: "/Users/[YOUR-USERNAME]/[YOUR-REPO]/large_file.ts", offset: 10, length: 50 })
```

### 2. `edit_block` (PREFERRED)
```
edit_block({
  "path": "/Users/[YOUR-USERNAME]/[YOUR-REPO]/src/app/page.tsx",
  "content": "file.tsx\n<<<<<<< SEARCH\nold code\n=======\nnew code\n>>>>>>> REPLACE"
})
```

### 3. `write_file`
```
write_file({ path: "/Users/[YOUR-USERNAME]/[YOUR-REPO]/new_file.txt", content: "...", mode: "rewrite" })
```

### 4. `start_process`
```
start_process({ command: "cd ~/path/to/repo && git status" })
```

### 5. `list_directory`
```
list_directory({ path: "/Users/[YOUR-USERNAME]/[YOUR-REPO]/.ai" })
```

### 6. `start_search`
```
start_search({ path: "/Users/[YOUR-USERNAME]/[YOUR-REPO]", pattern: "search_term", type: "content" })
```

---

## Token Saving Patterns

1. `tail -n 50 file.log` instead of full read.
2. `edit_block` instead of `write_file`.
3. `repomix --compress` for overview.
4. **Long processes:** `python3 script.py --quiet > /tmp/run.log 2>&1 && tail -5 /tmp/run.log`.

---

## ⛔ Anti-timeout (ADR-045)

**Rule:** `timeout_ms` ≤ 90 000 ms (90 sec).
**Rule:** After ~9 mins total waiting — **PAUSE** and ask user to type "continue".
