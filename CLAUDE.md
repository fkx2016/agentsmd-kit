@~/.claude/CLAUDE.md
@~/.claude/templates/cloudflare-worker.md

# agentsmd-kit

The AGENTS.md Starter Kit -- landing page, lead-magnet capture, downloadable kit, and the article that explains it. A Kurka Labs project. Sister to [claudemd-kit](https://github.com/fkx2016/claudemd-kit).

## Architecture

- **Landing**: Cloudflare Pages serving `landing/public/` (static HTML, no build step)
- **API**: Cloudflare Worker (`worker/`) handling `POST /api/subscribe`
- **Storage**: R2 hosts `agentsmd-kit.zip` (in shared bucket `kurkalabs-public`)
- **Email**: Resend transactional + audience `agentsmd-kit-subscribers` (id `670bbd73-c49e-43fa-86a0-68f5e8b2427c`)
- **Bot protection**: Cloudflare Turnstile (sitekey `0x4AAAAAADGC6FYhGkZT2xEf`)
- **Domain**: `agentsmd.kurkalabs.dev` (subdomain on existing Kurka Labs zone)

## Build / Test / Deploy

- Build the downloadable kit: `bash scripts/build-kit.sh` or `pwsh scripts/build-kit.ps1` (produces `dist/agentsmd-kit.zip`)
- Deploy worker: `cd worker && npx wrangler deploy`
- Deploy landing: `npx wrangler pages deploy landing/public --project-name=agentsmd-kit --branch=main`
- Upload kit zip to R2: `cd worker && npx wrangler r2 object put kurkalabs-public/agentsmd-kit.zip --file=../dist/agentsmd-kit.zip --content-type=application/zip --remote`
- Smoke test: `curl https://agentsmd.kurkalabs.dev/api/health` -- expect `{"ok":true,"service":"agentsmd-kit-subscribe"}`

## Conventions

- Articles live in `articles/` as Markdown. Filenames are numbered (`01-...`) for ordering.
- Kit source lives in `kit/`. The build script is the only thing that produces zips. Never commit a zip.
- Landing page is intentionally a single HTML file. No build step, no framework. Edit `landing/public/index.html` directly.
- Worker uses ES modules (`export default { fetch(...) }`). No CommonJS.
- All secrets via `wrangler secret put`, never in `wrangler.toml` or code.
- All git commits via Desktop Commander shells: write message to a temp file outside the repo (e.g. `$env:TEMP\msg.txt`), then `git commit -F <file>`. Never `-m` with quoted strings.

## Immutable Rules

1. Never commit the zip. Build artifacts live in `dist/` (gitignored). The zip is uploaded to R2 by the upload script.
2. Never commit `.dev.vars`, `.wrangler/`, or anything in `node_modules/`.
3. The Turnstile sitekey is public (it's in the HTML); the secret is set via `wrangler secret put TURNSTILE_SECRET_KEY` and never appears in any committed file.
4. Email validation lives in the Worker, not just the client. Client-side validation is UX, not security.
5. The `kit/AGENTS.md` template users download must NOT contain Frank-specific references. It's a fillable template with `[bracketed placeholders]` for them to customize.

## Relationship to claudemd-kit

Both projects ship together as part of Kurka Labs' lead-magnet family. They serve different audiences:

- **claudemd-kit** -> Claude Code users specifically. Comprehensive `.claude/` system with skills, rules, hooks, agents, commands, installer.
- **agentsmd-kit** -> Universal cross-agent compatibility. Minimal flat-file kit. Codex, Cursor, Copilot, Gemini, Jules, Windsurf, Aider, Zed, Warp, RooCode, Amp, Factory, plus Claude Code via a one-line shim.

Each kit cross-references the other in its README. Future unification (an "advanced dynamic universal" kit) is on the roadmap once we have data on which configurations users actually pick.
