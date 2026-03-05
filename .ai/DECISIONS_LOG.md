# DECISIONS LOG — [YOUR PROJECT NAME]

Format: ADR (Architecture Decision Record).

---

## ADR-001: Choice of Database — PostgreSQL (Supabase)
- **Status:** Accepted
- **Context:** Need a relational DB with real-time capabilities and Auth.
- **Decision:** Use Supabase (PostgreSQL).
- **Consequences:** Easy auth integration, RLS for security, excellent local dev experience.

## ADR-002: AI Model Selection — OpenRouter Hub
- **Status:** Accepted
- **Context:** Need access to multiple models (Claude, GPT, Llama) without managing separate keys.
- **Decision:** Use OpenRouter as the single gateway.
- **Consequences:** Single API key, easy model switching, fallback capabilities.

## ADR-003: Role Separation — Architect vs Executor
- **Status:** Accepted
- **Context:** LLMs struggle when trying to plan and code simultaneously.
- **Decision:** Split roles. Architect chat creates the plan (step files). Executor chat writes the code.
- **Consequences:** Higher quality code, clear specs, but requires two separate chat sessions.

## ADR-004: Token Budget for Repomix
- **Status:** Accepted
- **Context:** Large context windows are expensive and can degrade model performance.
- **Decision:** Limit Repomix context to 8,000 tokens per task. Use targeted includes.
- **Consequences:** Faster responses, lower cost, forced focus on relevant files.

## ADR-005: Anti-Timeout Rule (9-minute limit)
- **Status:** Accepted
- **Context:** Cloudflare drops idle connections after ~10 mins.
- **Decision:** Executors must pause and ask for user confirmation after 9 minutes of processing.
- **Consequences:** Prevents connection drops and lost work.

## ADR-006: Step File Format
- **Status:** Accepted
- **Context:** Need a structured format for tasks that both humans and AI can parse.
- **Decision:** Use Markdown with XML tags (`<step_meta>`, `<task>`, `<repomix>`).
- **Consequences:** Clear structure, easy to parse programmatically if needed.
