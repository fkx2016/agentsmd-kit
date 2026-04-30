# AGENTS.md Starter Kit

> **The universal AI-agent context kit.** One repo. Every coding agent. No vendor lock-in.

This kit gives your project a memory file that works with **every major AI coding agent** — Codex, Cursor, GitHub Copilot, Gemini CLI, Jules, Windsurf, Aider, Zed, Warp, RooCode, Amp, Factory, **and Claude Code** — using the open [`AGENTS.md`](https://agents.md/) standard with a one-line compatibility shim for Claude Code.

Drop these files into your project root and your agent stops re-asking the same questions every session.

---

## What you get

```
your-project/
├── AGENTS.md                    # ← canonical agent instructions
├── CLAUDE.md                    # ← one line: @AGENTS.md (Claude Code compat)
├── AGENTS.override.md.example   # ← template for personal overrides (gitignored)
├── .gitignore                   # ← ignores .override.md, .agents/skills caches
├── examples/
│   ├── frontend/AGENTS.md       # ← subpackage scoping example
│   ├── backend/AGENTS.md        # ← subpackage scoping example
│   └── tests/AGENTS.md          # ← subpackage scoping example
├── .agents/
│   └── skills/
│       └── run-tests/SKILL.md   # ← reusable skill in the open format
└── docs/
    ├── COMPATIBILITY.md         # ← which agent reads what
    └── HIERARCHY.md             # ← precedence rules and overrides
```

---

## Why two files?

`AGENTS.md` is the open standard. Created mid-2025 by OpenAI, Sourcegraph, Google, Cursor, Amp, and Factory; now stewarded by the Linux Foundation. Read natively by every major coding agent except one.

`CLAUDE.md` is Claude Code's proprietary equivalent. As of April 2026, [Claude Code does not natively read AGENTS.md](https://github.com/anthropics/claude-code/issues/6235), despite a 3,000+ upvote feature request open since August 2025.

The fix is one line. Your `CLAUDE.md` contains:

```markdown
@AGENTS.md
```

That tells Claude Code to import everything from `AGENTS.md`. Single source of truth. When Anthropic eventually ships native support, you delete the stub.

---

## 60-second setup

1. Copy `AGENTS.md`, `CLAUDE.md`, and `.gitignore` into your project root.
2. Edit `AGENTS.md` — fill in the `[bracketed placeholders]` with your project's actual conventions.
3. Optional: copy `AGENTS.override.md.example` to `AGENTS.override.md` for personal local-only rules. It's already gitignored.
4. Optional: copy `examples/*/AGENTS.md` patterns into your subpackages for scoped rules.
5. Optional: drop the `.agents/skills/` skill into your project for a reusable test-runner capability.
6. Commit `AGENTS.md`, `CLAUDE.md`, and `.gitignore`. Do NOT commit `AGENTS.override.md`.

That's it. Open your editor with any AI agent. It now knows the project.

---

## What goes in AGENTS.md?

A good `AGENTS.md` answers the questions a new senior engineer would ask on day one:

- What is this project? What stack?
- How do I build it? Run it? Test it?
- What conventions matter? What's deprecated?
- What are the gotchas — the things I'd waste a day on if I didn't know?
- Where's the rest of the docs?

It is **not** a place to dump every coding standard you've ever had an opinion about. Frontier models reliably follow ~150-200 instructions before quality degrades. The Claude Code system prompt alone consumes ~50 of those. Spend your remaining instruction budget on what actually moves the needle for your project.

The `AGENTS.md` template in this kit follows that discipline. Use it as the shape, fill in your specifics, and resist the urge to bloat.

---

## What this kit does NOT do

- **Doesn't fix [the symlink bug for skills](https://github.com/anthropics/claude-code/issues/20820)** — Claude Code pollutes `.claude/skills/` with internal files, breaking the `.agents/skills/` symlink trick. Until Anthropic fixes it, you may need to duplicate skill folders or accept that skills are Codex/open-spec only for now.
- **Doesn't replicate Claude Code's auto-memory** — that's a Claude-only feature. If you want it, you'll still get it on the Claude Code side; nothing here interferes.
- **Doesn't enforce anything at runtime** — `AGENTS.md` is context, not config. Agents may not follow rules perfectly. Pair with linters, CI, and hooks for hard guarantees.

---

## Further reading

- [`agents.md` official site](https://agents.md/) — the open standard
- [Codex AGENTS.md guide](https://developers.openai.com/codex/guides/agents-md) — OpenAI's docs
- [Claude Code memory docs](https://code.claude.com/docs/en/memory) — Anthropic's docs
- [`docs/COMPATIBILITY.md`](docs/COMPATIBILITY.md) — which agent reads what
- [`docs/HIERARCHY.md`](docs/HIERARCHY.md) — how precedence and overrides work

---

## Credits

Maintained by [Kurka Labs](https://kurkalabs.dev). Part of the [`claudemd.kurkalabs.dev`](https://claudemd.kurkalabs.dev) toolkit family.
