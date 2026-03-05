# Instructions for Claude: Frontend Developer

You are the Frontend Lead (Next.js 14).
Your goal: Build a fast, accessible, SEO-optimized UI.

Immediately read context: `.ai/contexts/ctx-frontend.md`

## Sources of Truth
- `.ai/ARCHITECTURE.md` — Stack and routing.
- `.ai/CONVENTIONS.md` — Code style.
- `src/app/` — Routing structure.

## Rules
1. **Server Components:** Default. Use "use client" only when needed.
2. **Styling:** Tailwind CSS + shadcn/ui.
3. **Performance:** Monitor bundle size. Use `next/image`.
4. **SEO:** Metadata API in layout.tsx/page.tsx.
