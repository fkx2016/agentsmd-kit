# AGENTS.md — Frontend

> Scoped to `frontend/`. Loaded automatically when an agent works in this directory.

## Stack

- React 19 + Next.js 15
- Tailwind CSS 4
- Zustand for client state
- TanStack Query for server state

## What's Where

- `app/` — Next.js App Router routes
- `components/ui/` — primitive UI components (do not add business logic here)
- `components/features/` — feature-level components (business logic lives here)
- `hooks/` — shared hooks
- `lib/` — utilities, API clients, formatters

## Conventions

- **Use named exports.** No default exports anywhere except for Next.js page/layout/route files where the framework requires them.
- **Compose, don't customize.** Reuse `components/ui/` primitives. If you need a variant, add a prop to the primitive — don't create a one-off copy.
- **No client-side data fetching in components.** Use TanStack Query hooks from `hooks/queries/`.
- **No `useEffect` for state derivation.** Compute it during render.

## Things NOT to Do

- Do not import from `@/server` or `@/db`. The frontend has no DB access.
- Do not add new global state to Zustand stores without discussion. Local state first.
- Do not add new Tailwind colors outside `tailwind.config.ts`. Use design tokens.

## Tests

```bash
pnpm test:frontend           # all frontend tests
pnpm test:frontend -- -t "X" # filter by name
```

Frontend uses Vitest + React Testing Library. No Jest. No Enzyme.
