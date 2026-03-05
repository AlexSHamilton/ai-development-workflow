# CONVENTIONS — [YOUR PROJECT NAME]

Code style, rules, and bans.

---

## General Rules

1. **DB Migrations** — Always via CLI. Never edit schema manually in production.
2. **Data Entry Points** — Define where data enters the system (e.g., raw tables first).
3. **Deduplication** — Define how duplicates are handled (e.g., fingerprints).

## Language Specifics

### Python
- **Virtual Environment**: Always use venv.
- **Imports**: Use absolute imports where possible.
- **Type Hinting**: Mandatory for all functions.

### TypeScript / Next.js
- **Strict Mode**: Enabled.
- **Components**: Functional components only.
- **Styling**: Tailwind CSS classes.

## Anti-Patterns and Bans

- ❌ **Hardcoded Secrets**: Never commit keys/tokens.
- ❌ **Direct DB Writes**: Always use the defined API or raw tables.
- ❌ **Guessing**: Do not guess field names or paths; check the registry.
- ❌ **System Python**: Never use system python; use the venv python.

## URL Naming Conventions (if applicable)
[Define rules for URLs, slugs, etc.]
