# AGENTS.md — Backend

> Scoped to `backend/`. Loaded automatically when an agent works in this directory.

## Stack

- Node.js 20 + TypeScript 5.4
- Fastify for HTTP
- Prisma + PostgreSQL 16
- BullMQ for background jobs

## What's Where

- `routes/` — HTTP route handlers (thin — call into services)
- `services/` — business logic (no HTTP concerns here)
- `db/` — Prisma schema and migrations
- `jobs/` — background job definitions
- `lib/` — shared utilities

## Conventions

- **Routes are thin.** Validate input, call a service, format the response. No business logic in routes.
- **Services are testable.** They receive their dependencies (DB client, logger, etc.) as constructor arguments. No global imports of the DB client inside services.
- **All errors are typed.** Use the `AppError` class from `lib/errors.ts`. Never throw raw `Error`.
- **All endpoints are added to `routes/manifest.ts`.** Otherwise the typed RPC client doesn't see them.

## Database

- **Never edit committed migrations.** Create a new one: `pnpm db:migrate dev --name [description]`.
- **Schema changes require a backfill plan.** Document it in the migration file as a comment.
- **Use Prisma transactions for multi-table writes.** Don't trust eventual consistency.

## Tests

```bash
pnpm test:backend                      # full backend suite
pnpm test:backend -- routes/users.test # specific test file
```

Tests use Vitest with a real PostgreSQL container (Testcontainers). No mocks for DB. Integration tests are the default.

## Things NOT to Do

- Do not add new dependencies without approval. Backend deps are aggressively minimized.
- Do not call external APIs without a timeout and retry policy. Use `lib/http-client.ts`.
- Do not bypass `middleware/auth.ts`. Even for "internal" endpoints. Especially for "internal" endpoints.
