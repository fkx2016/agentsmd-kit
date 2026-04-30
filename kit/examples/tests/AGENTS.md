# AGENTS.md — Tests

> Scoped to `tests/`. Loaded automatically when an agent works in this directory.

## Test Philosophy

- **Integration over unit.** Most tests should exercise real code paths through the system. Unit tests are for pure functions and complex algorithms only.
- **No mocking the DB.** Use Testcontainers. Real Postgres, ephemeral instance per test run.
- **No mocking what we own.** Mock only third-party services we don't control.

## Layout

- `tests/integration/` — full-stack tests (HTTP through to DB)
- `tests/e2e/` — Playwright browser tests
- `tests/unit/` — pure-function tests only
- `tests/fixtures/` — test data factories (use these instead of inline setup)

## Conventions

- **One assertion per test, ideally.** If you need multiple, name the test for the *behavior* being tested, not the technical detail.
- **Use the fixtures.** Don't construct test users inline — use `tests/fixtures/users.ts`. Keeps tests resilient to schema changes.
- **Tests are not throwaway code.** Same review standards as production. No commented-out blocks. No `console.log` left behind.

## Running

```bash
pnpm test                         # everything
pnpm test:unit                    # fast, no containers
pnpm test:integration             # spins up Postgres
pnpm test:e2e                     # spins up full stack + browser
pnpm test -- -t "specific name"   # filter
```

## Things NOT to Do

- Do not commit `.only` or `.skip` modifiers. CI should fail if it sees them.
- Do not add `await new Promise(r => setTimeout(r, 1000))` to "fix" flaky tests. Find and fix the actual race.
- Do not write tests that depend on test order. Each test must work in isolation.
