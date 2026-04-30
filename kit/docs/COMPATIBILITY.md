# Compatibility Reference

Which agent reads which file, as of April 2026.

## AGENTS.md

| Agent | Native support | Notes |
|---|---|---|
| OpenAI Codex | ✅ | First-class. Walks down from project root to cwd. |
| Cursor | ✅ | First-class. |
| GitHub Copilot | ✅ | First-class. |
| Gemini CLI | ✅ | First-class. |
| Google Jules | ✅ | First-class. |
| Sourcegraph Amp | ✅ | First-class. Co-authored the standard. |
| Windsurf | ✅ | First-class. |
| Aider | ✅ | First-class. |
| Zed | ✅ | First-class. |
| Warp | ✅ | First-class. |
| RooCode | ✅ | First-class. |
| Factory | ✅ | First-class. Co-authored the standard. |
| **Claude Code** | ❌ | **Not natively supported.** Use the `@AGENTS.md` shim in `CLAUDE.md`. [Tracking issue](https://github.com/anthropics/claude-code/issues/6235). |

## CLAUDE.md

| Agent | Native support | Notes |
|---|---|---|
| Claude Code | ✅ | First-class. Walks up from cwd to project root. |
| Anthropic Claude (claude.ai) | Partial | Reads if you upload as project file. No automatic discovery. |
| All others | ❌ | Not supported. |

## AGENTS.override.md

| Agent | Native support | Notes |
|---|---|---|
| OpenAI Codex | ✅ | First-class at any directory level. |
| Other AGENTS.md readers | Varies | Most respect the override pattern. Test in your specific tool. |
| Claude Code | ❌ | Use `CLAUDE.local.md` (deprecated) or `@AGENTS.override.md` import. |

## .agents/skills/SKILL.md (Open Agent Skills standard)

| Agent | Native support | Notes |
|---|---|---|
| OpenAI Codex | ✅ | First-class. The canonical implementation. |
| **Claude Code** | ❌ | Uses `.claude/skills/` instead, [despite Anthropic authoring the open spec](https://github.com/anthropics/claude-code/issues/31005). Symlink workaround partially broken. |
| Others | Varies | Adoption is in progress. |

## Recommendation

Write to the **AGENTS.md** standard. Use the `CLAUDE.md` → `@AGENTS.md` shim for Claude Code compatibility today. When Anthropic ships native support, delete the shim.

For skills, use `.agents/skills/`. If you also use Claude Code, duplicate the skill folder under `.claude/skills/` until the symlink bug is fixed.
