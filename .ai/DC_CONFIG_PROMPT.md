# DC_CONFIG_PROMPT — Desktop Commander Config Prompt

> Use this prompt in a SEPARATE Claude Desktop chat to configure DC.
> DO NOT configure DC in the working chat.

---

## Prompt to Copy

```
You are an assistant for configuring Desktop Commander MCP for [YOUR PROJECT NAME].

Configure Desktop Commander with the following parameters:

1. Set blockedCommands:
set_config_value({
  "key": "blockedCommands",
  "value": [
    "rm -rf", "sudo", "su", "chmod", "chown",
    "git push", "git push --force", "npm publish",
    "ssh", "curl", "wget",
    "pkill", "kill -9", "killall",
    "docker rm", "docker rmi", "docker stop",
    "nano", "vim", "vi", "less", "more", "top", "htop", "man",
    "python3 -c", "python -c", "node -e",
    "supabase db push", "supabase db reset", "supabase db push --linked",
    "supabase migration repair",
    "npm run build", "npx next build"
  ]
})

2. Set fileWriteLineLimit:
set_config_value({ "key": "fileWriteLineLimit", "value": 50 })

3. Set fileReadLineLimit:
set_config_value({ "key": "fileReadLineLimit", "value": 1000 })

4. Set allowedDirectories:
set_config_value({
  "key": "allowedDirectories",
  "value": [
    "/Users/[YOUR-USERNAME]/[YOUR-PATH]/[your-web-repo]",
    "/Users/[YOUR-USERNAME]/[YOUR-PATH]/[your-workers-repo]"
  ]
})

5. Disable telemetry:
set_config_value({ "key": "telemetryEnabled", "value": false })

After each setting — show result. At the end — execute get_config({}) and show full config.
```
