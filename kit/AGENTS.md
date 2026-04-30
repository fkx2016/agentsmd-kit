# AGENTS.md

> **Quick edit checklist:** Replace every `[bracketed]` placeholder. Delete sections that don't apply. Keep this file under ~200 lines — frontier models follow ~150–200 instructions reliably, and the agent's system prompt already consumes ~50.

## Project Overview

**[Project name]** is a [one-sentence description: what is it, who is it for, what problem does it solve].

**Stack:**
- Language: [e.g., TypeScript 5.4, Python 3.12, Rust 1.78]
- Framework: [e.g., Next.js 14, FastAPI, Axum]
- Database: [e.g., PostgreSQL 16 via Prisma, SQLite via D1]
- Deploy target: [e.g., Cloudflare Workers + Pages, AWS ECS, Vercel]

**Repo layout:**
```
[paste tree of top-level dirs with one-line descriptions]
src/         - [what's in here]
tests/       - [what's in here]
scripts/     - [what's in here]
```

## Setup

```bash
[install command, e.g., pnpm install]
[any env-var setup needed for first run]
[any local DB or service setup]
```

If setup fails, [where to look — common gotchas, doc links].

## Build, Run, Test

| Task | Command |
|---|---|
| Build | `[build command]` |
| Dev server | `[dev command]` |
| Run tests | `[test command]` |
| Run a single test | `[targeted test command]` |
| Lint | `[lint command]` |
| Typecheck | `[typecheck command]` |
| Format | `[format command]` |

**Always run tests after modifying [language] files.** [Customize: which kinds of changes warrant which tests.]

## Code Style

- **Indentation:** [spaces or tabs, count]
- **Imports:** [sort order, named vs default, any banned imports]
- **Comments:** Prefer comments that explain *why*, not *what*. Code should be self-documenting.
- **Error handling:** [project's error pattern — Result types, try/catch, error returns, etc.]
- **Tests:** [TDD or test-after, framework, where tests live]

[Add 3–5 project-specific style rules that aren't enforced by your linter. Don't restate things the linter already catches — that's wasted instruction budget.]

## Conventions That Matter

- **[Convention 1]:** [What it is. Why it exists. Counter-example.]
- **[Convention 2]:** [What it is. Why it exists. Counter-example.]

Example:
- **Always import from `@company/utils-v2`, never `@company/utils`.** The latter is deprecated and will break in CI.
- **All new endpoints must be added to `routes/manifest.ts`.** Otherwise the type-safe client won't see them.

## Architecture Notes

[2–4 paragraphs of orientation an agent would otherwise have to reverse-engineer from the codebase. Where does business logic live? How does data flow? What are the seams?]

## Security

- **Never commit secrets.** Use `.env.local` (gitignored) for development. Production secrets live in [your secret manager].
- **Never log:** [list of things — auth tokens, PII, full request bodies, etc.]
- **Always validate:** [list of inputs — user input, webhook payloads, query params]

## Pull Requests

- **Branch from:** `[main / develop]`
- **Merge style:** [squash, rebase, merge commit]
- **Commit message format:** [Conventional Commits, plain, custom format]
- **PR description must include:** [what tests were run, screenshots for UI, migration notes]

## Things NOT to do

- Do not [thing 1 — concrete, with reason].
- Do not [thing 2 — concrete, with reason].
- Do not modify files in `[directory]` without explicit approval. They are [reason].

Example:
- Do not add new dependencies without approval. We aggressively minimize the dep tree.
- Do not bypass the auth middleware in `src/middleware/auth.ts`, even for "internal" endpoints.
- Do not modify migration files in `db/migrations/` once they've been merged. Create a new migration instead.

## Gotchas

[The 2–5 things that have wasted half a day for someone joining the project. Document them here.]

Example:
- The dev server caches Prisma schemas. After editing `schema.prisma`, run `pnpm db:generate` *and* restart the dev server.
- The CI test matrix runs against Node 18 and Node 20. Some `Array.prototype` methods (e.g., `findLast`) work locally but fail on Node 18.

## Where Else to Look

- `README.md` — human-facing project intro
- `docs/architecture.md` — deeper architecture deep dive
- `docs/api.md` — API reference
- `examples/*/AGENTS.md` — subpackage-specific rules (loaded automatically when working in those dirs)

## Last Updated

[YYYY-MM-DD] — [your name / team]
