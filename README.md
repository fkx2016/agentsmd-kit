# agentsmd-kit

The AGENTS.md Starter Kit - landing page, lead-magnet capture, downloadable kit, and the article that explains it. A Kurka Labs project. Sister to [claudemd-kit](https://github.com/fkx2016/claudemd-kit).

## Architecture

- **Landing**: Cloudflare Pages serving `landing/public/` (static HTML, no build step)
- **API**: Cloudflare Worker (`worker/`) handling `POST /api/subscribe`
- **Storage**: R2 hosts `agentsmd-kit.zip` (in shared bucket `kurkalabs-public`)
- **Email**: Resend transactional + audience `agentsmd-kit-subscribers`
- **Bot protection**: Cloudflare Turnstile on the form
- **Domain**: `agentsmd.kurkalabs.dev` (subdomain on existing Kurka Labs zone)

## Build / Test / Deploy

- Build the downloadable kit: `bash scripts/build-kit.sh` or `pwsh scripts/build-kit.ps1` (produces `dist/agentsmd-kit.zip`)
- Deploy worker: `cd worker && npx wrangler deploy`
- Deploy landing: `npx wrangler pages deploy landing/public --project-name=agentsmd-kit`
- Upload kit zip to R2: `cd worker && npx wrangler r2 object put kurkalabs-public/agentsmd-kit.zip --file=../dist/agentsmd-kit.zip --content-type=application/zip --remote`

## Conventions

- Articles live in `articles/` as Markdown.
- Kit source lives in `kit/`. The build script is the only thing that produces zips. Never commit a zip.
- Landing page is intentionally a single HTML file. No build step, no framework. Edit `landing/public/index.html` directly.
- Worker uses ES modules (`export default { fetch(...) }`). No CommonJS.
- All git commits via Desktop Commander shells: write message to a temp file outside the repo (e.g. `$env:TEMP\msg.txt`), then `git commit -F <file>`. Never `-m` with quoted strings.

## Relationship to claudemd-kit

Both projects ship together as part of Kurka Labs' lead-magnet family. They serve different audiences:

- **claudemd-kit** -> Claude Code users specifically. Comprehensive `.claude/` system with skills, rules, hooks, agents, commands, installer.
- **agentsmd-kit** -> Universal cross-agent compatibility. Minimal flat-file kit. Codex, Cursor, Copilot, Gemini, Jules, Windsurf, Aider, Zed, Warp, RooCode, Amp, Factory, plus Claude Code via a one-line shim.

Each kit cross-references the other in its README. Future unification (an "advanced dynamic universal" kit) is on the roadmap once we have data on which configurations users actually pick.
