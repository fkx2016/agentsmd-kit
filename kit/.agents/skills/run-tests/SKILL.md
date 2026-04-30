---
name: run-tests
description: Run the project's test suite with the right command for the changed area. Trigger after modifying source files to verify nothing broke. Trigger when the user asks to "run tests", "verify changes", or "check if it works".
---

# Run Tests Skill

Use this skill to run the test suite intelligently based on what was changed.

## Decision Tree

1. **If changes are in `frontend/`:** run `pnpm test:frontend`
2. **If changes are in `backend/`:** run `pnpm test:backend`
3. **If changes are in `db/`:** run `pnpm test:integration` (covers schema)
4. **If changes are in `lib/` or shared code:** run `pnpm test` (full suite)
5. **If unsure:** run `pnpm test:changed` (uses git diff to scope)

## After the Run

- **All passed:** report which suite ran and the test count.
- **Some failed:** show the first 3 failures with file paths and assertion messages. Do not show full stack traces unless asked.
- **Tests timed out:** check if Testcontainers is running (`docker ps`). If not, surface that as the likely cause.

## Things to Avoid

- Do not run `pnpm test:e2e` automatically. It's slow and requires browser setup. Only run if explicitly asked.
- Do not run tests with `--watch` mode in agent contexts. Always one-shot.
- Do not pipe to `head` or `tail` to "summarize" — use the test runner's own summary output.

## Inputs

- Implicit: the git diff of recent changes
- Explicit (optional): a glob or filename to scope the test run

## Outputs

- Pass/fail status
- Test count
- For failures: file path + test name + assertion message (first 3 only)
