# Hierarchy and Precedence

How AGENTS.md and CLAUDE.md files combine, and which wins when they conflict.

## The Universal Principle

**More specific overrides more general.** Both Codex and Claude Code follow this rule. The differences are in how they discover files and walk the directory tree.

## Codex Discovery Order

Codex builds an instruction chain at session start:

1. **Global scope** (`~/.codex/`)
   - First non-empty file from: `AGENTS.override.md` → `AGENTS.md`
2. **Project scope** (project root → cwd)
   - In each directory along the path, first non-empty file from: `AGENTS.override.md` → `AGENTS.md` → fallback names from `project_doc_fallback_filenames`

**Walking direction:** down (root → cwd).
**Combined size limit:** 32 KiB (`project_doc_max_bytes`, configurable). Once hit, no more files load.
**Empty files:** skipped.

## Claude Code Discovery Order

Claude Code loads memory from these locations (highest to lowest precedence):

1. **Managed Policy** (org-level, can't be overridden)
2. **Managed Drop-ins** (`managed-settings.d/`, v2.1.83+)
3. **Project Memory** (`CLAUDE.md` or `.claude/CLAUDE.md`)
4. **Project Rules** (`.claude/rules/*.md`, path-scoped via globs)
5. **User Memory** (`~/.claude/CLAUDE.md`)
6. **User-Level Rules** (`~/.claude/rules/`)
7. **Local Project Memory** (`CLAUDE.local.md`, deprecated)
8. **Auto Memory** (`MEMORY.md`, Claude's self-written notes)

**Walking direction:** up (cwd → project root).
**Soft size guideline:** ~200 lines per file.
**Imports:** `@path/to/file.md`, recursive up to 5 levels deep.

## Where the Override Lives

| Scenario | Codex | Claude Code |
|---|---|---|
| Personal local override | `AGENTS.override.md` (gitignored, any dir) | `CLAUDE.local.md` (deprecated) |
| Recommended modern equivalent | Same — use `AGENTS.override.md` | Use `@imports` from a gitignored file |
| Org-wide enforcement | (Use repo CI checks) | `Managed Policy CLAUDE.md` |

## Worked Example

Project structure:

```
my-app/
├── AGENTS.md                          # rule: "use 4-space indentation"
├── CLAUDE.md                          # @AGENTS.md
├── AGENTS.override.md                 # local: "use 2-space, I prefer it"
├── frontend/
│   ├── AGENTS.md                      # rule: "frontend uses 2-space"
│   └── components/
│       └── AGENTS.md                  # rule: "no inline styles"
```

When Codex is asked to edit `frontend/components/Button.tsx`, the discovered chain (in load order, later overrides earlier):

1. `~/.codex/AGENTS.md` (your global preferences)
2. `my-app/AGENTS.md` ("4-space")
3. `my-app/AGENTS.override.md` ("2-space, I prefer it")
4. `my-app/frontend/AGENTS.md` ("frontend uses 2-space")
5. `my-app/frontend/components/AGENTS.md` ("no inline styles")

Result: 2-space indentation, no inline styles. The deepest, most specific file wins.

When Claude Code is asked the same question (with the `CLAUDE.md` → `@AGENTS.md` shim):

1. Walks up from `frontend/components/` to project root
2. Loads `my-app/CLAUDE.md`, which imports `AGENTS.md`
3. Does NOT automatically discover the `frontend/` or `frontend/components/` sub-`AGENTS.md` files

**This is a real difference.** Claude Code does not natively walk into subdirectory `CLAUDE.md` files — it only loads the project root's `CLAUDE.md`. To get subdirectory rules, you'd need to add `@frontend/AGENTS.md` and `@frontend/components/AGENTS.md` imports to your root `CLAUDE.md`, or use Claude Code's `.claude/rules/` system with glob patterns.

This is one of the meaningful asymmetries between the two systems. Codex's directory-nesting approach gives you scoped rules for free; Claude Code's import-based approach requires explicit declaration.

## Practical Recommendations

- **Keep your root `AGENTS.md` lean.** Cross-cutting rules only.
- **Push area-specific rules to subdirectory `AGENTS.md` files.** They load automatically in Codex.
- **For Claude Code parity,** explicitly import subdirectory files from your root `CLAUDE.md`:
  ```markdown
  @AGENTS.md
  @frontend/AGENTS.md
  @backend/AGENTS.md
  ```
  Or use Claude Code's path-scoped `.claude/rules/*.md` files as the equivalent mechanism.
- **Test which files are loaded.** In Claude Code, use `/memory`. In Codex, use `codex --cd subdir --ask-for-approval never "Show which instruction files are active."`

## When Things Conflict

Both systems are non-deterministic about conflicts. The model picks one. To avoid surprises:

- Don't write contradictory rules at different levels. If subdirectory rules differ from project rules, make the difference *additive*, not contradictory.
- When you need to override, do so explicitly: "In `frontend/`, the rule above does not apply because [reason]."
- Use overrides (`AGENTS.override.md`) for temporary departures, not as a substitute for fixing the underlying rule.
