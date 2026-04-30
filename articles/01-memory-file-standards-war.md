# AGENTS.md, CLAUDE.md, and the standards war 3,000 people are upvoting

*One file in your repo decides whether your AI agent shows up to work confused or competent. There are now two formats for that file. Only one of them is going to win.*

---

Anthropic created MCP — the Model Context Protocol — explicitly to keep the AI tooling ecosystem from fragmenting into a dozen incompatible vendor walls. It worked. MCP is the standard for how agents talk to tools, and Anthropic gets credit for the foresight.

Then on the memory-file side — the file that tells your coding agent how your project actually works — they're at risk of walking into the very trap they built MCP to avoid. Or so the community is increasingly saying.

Here's the situation as of April 2026.

## The two files

When you start a coding session with Claude Code, it reads a file called `CLAUDE.md` from your project root. That file holds the things you'd otherwise have to re-explain every session: "we use pnpm, not npm." "Tests run with `make test-integration`, not pytest." "Don't import from `@company/utils` — it's deprecated. Use `@company/utils-v2`."

The pattern is good. The pattern is so good that in mid-2025, six other vendors got together and standardized it. OpenAI (Codex), Sourcegraph (Amp), Google (Jules), Cursor, Factory, and a growing list of others agreed on a single filename: `AGENTS.md`. Same idea as `CLAUDE.md`. Different name. The Linux Foundation's Agentic AI Foundation now stewards the spec. Over [60,000 open-source projects on GitHub](https://agents.md/) ship one.

Read by `AGENTS.md` natively, in April 2026: **Codex, Cursor, GitHub Copilot, Gemini CLI, Jules, Windsurf, Aider, Zed, Warp, RooCode, Amp, Factory.**

Read by `AGENTS.md` natively: **everything except Claude Code.**

## The community has noticed

The [GitHub feature request asking Anthropic to support AGENTS.md](https://github.com/anthropics/claude-code/issues/6235) was opened in August 2025. As I write this in April 2026, it has 3,000+ upvotes, 200+ comments, and zero official response from Anthropic. The duplicate requests filed since then have all been silently auto-closed by bots.

It gets more interesting. The companion spec — Agent Skills, the format for letting agents discover and trigger reusable capabilities — was created and is maintained by Anthropic. It uses `.agents/skills/` as the canonical location. Codex follows that spec exactly. Claude Code? Uses `.claude/skills/` instead. Anthropic shipped the open standard and then didn't follow it in their own product.

You can read that two ways. The charitable read: Claude Code is older than the open standards, and migration costs real engineering time. The less charitable read: the same company that won MCP appears to be sitting out the next round, and nobody at Anthropic has prioritized fixing it.

Either way, the practical question for builders is: which file should you write?

## What's actually different between them

The two formats are 90% the same. They're both markdown. Both load at session start. Both follow the principle that more-specific rules override more-general ones. The differences are at the edges, but the edges are interesting.

**Modularity.** `CLAUDE.md` has a real import system (`@path/to/file.md`, recursive up to 5 levels). You compose at the file level. Codex's `AGENTS.md` doesn't have imports — instead, you nest directories. The OpenAI monorepo itself ships [88 separate AGENTS.md files](https://github.com/openai/codex/blob/main/AGENTS.md), one per meaningful subdirectory, and the agent reads the one closest to whatever it's editing. Two different theories of composition: one Unix-y (small files, glue them together), one Windows-y (drop a config in each folder).

**Overrides.** Codex has a clean pattern: `AGENTS.override.md` is a first-class file at any directory level. You drop one to override the local rules without modifying anything checked in, and remove it when done. Claude Code's equivalent — `CLAUDE.local.md` — is officially deprecated, with `@imports` recommended as the replacement. The community has [tested this and found it less reliable](https://github.com/anthropics/claude-code/issues/2950); the imported override doesn't always win against the parent file's rules.

**Auto-memory.** This one is Claude Code's actual moat. Since v2.1.59, Claude writes its own notes during sessions — patterns it learns, debugging insights, build commands you've corrected it on. They go to a `MEMORY.md` file (first 200 lines load at startup). Codex doesn't have a direct equivalent. It has session logs you can audit, but no synthesized self-written memory. Claude Code does more "learning" between sessions out of the box. The trade-off: auto-memory drifts and accumulates stale entries; Codex never has this problem because it never volunteers.

**Size limits.** Claude Code targets ~200 lines per file as a soft guideline. Codex enforces a hard 32 KiB cap (`project_doc_max_bytes`, configurable) across the whole hierarchy. Once you hit that, no more files load. The Codex limit is more honest — the constraint is real either way (frontier models can reliably follow [~150-200 instructions](https://www.humanlayer.dev/blog/writing-a-good-claude-md) before quality degrades), and Codex makes you confront it.

None of these differences are dealbreakers. They'd all be addressable in a few weeks of engineering work if Anthropic decided to ship `AGENTS.md` support.

## The strategic frame

This is the same dynamic that played out with `README.md` thirty years ago. There were a half-dozen formats — `READ.ME`, `READ_ME.TXT`, project-specific things — until the open-source ecosystem coalesced around one filename and one informal convention. The format wasn't the best one. It was the one with enough vendor coordination behind it to become a Schelling point.

`AGENTS.md` is doing the same thing now, faster. The Linux Foundation backing creates institutional gravity. The six-vendor coalition creates network effects — every new agent that joins makes writing `AGENTS.md` more valuable to every project. By the time Anthropic decides to support it (and they will — the upvote pressure is too high to ignore forever), the question won't be whether `AGENTS.md` is the standard. It'll be whether `CLAUDE.md` is a useful dialect.

## What to actually do today

If you're shipping a project right now and you want to be future-proof, the answer is straightforward.

Write `AGENTS.md` as your real, canonical agent-instructions file. Make `CLAUDE.md` a one-line stub:

```markdown
@AGENTS.md
```

That single character of compatibility hack gets you read by every coding agent that matters, including Claude Code, today. When Anthropic eventually ships native `AGENTS.md` support, you delete the stub and you're done.

For overrides, use `AGENTS.override.md` and gitignore it. Codex reads it natively; Claude Code doesn't, but you can `@import` it from your `CLAUDE.md` stub if you want personal overrides to apply on the Anthropic side too.

For skills, write them under `.agents/skills/<skill-name>/SKILL.md` per the open spec. Symlink `.claude/skills` to `.agents/skills` if you want Claude Code to pick them up — though [the symlink trick is partially broken](https://github.com/anthropics/claude-code/issues/20820) right now because Claude Code pollutes the directory with `.system/` internal files. Until that's fixed, you may need to duplicate.

The pattern, in one sentence: **write to the open standard, alias to the proprietary one for compatibility, and wait for the holdout to relent.**

## The bigger pattern

It's worth saying out loud: the way you tell an AI agent how your project works is going to be one of the most important configuration artifacts in software for the next decade. We've gone from `.bashrc` to `package.json` to `pyproject.toml` — one new "this file describes how to work here" format per major shift in how we build. `AGENTS.md` is the one for the AI-agent shift.

Getting the filename right matters. Getting it standardized matters more. The vendor that wins this is the one that makes the file work the same way across tools, because the file isn't really configuration — it's the contract between humans and machine collaborators about how the project should be approached.

Anthropic, as the company that authored the most successful open standard in this space (MCP), should know exactly how this game is played. The longer they sit on the `AGENTS.md` request, the weirder it looks.

In the meantime: write `AGENTS.md`. Stub `CLAUDE.md`. Keep building.

---

*If you found this useful, two free Kurka Labs kits ship ready-to-use templates:*

*-> [The AGENTS.md Starter Kit](https://agentsmd.kurkalabs.dev) -- universal coverage, the open-standard kit that works with every major coding agent.*

*-> [The CLAUDE.md Starter Kit](https://claudemd.kurkalabs.dev) -- a deeper Claude Code-specific kit with skills, commands, agents, and the full four-layer system.*

*-- Frank*
