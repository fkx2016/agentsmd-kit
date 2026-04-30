---
description: Deploy the agentsmd-kit Worker and Pages site, in the right order
---

You are deploying the agentsmd-kit project to production.

## Steps

1. Run `git status`. If there are uncommitted changes, stop and tell me. We don't deploy from a dirty tree.

2. Run the kit build:
   ```
   bash scripts/build-kit.sh
   ```
   Verify `dist/agentsmd-kit.zip` exists and is < 50KB.

3. Upload the kit to R2:
   ```
   cd worker && npx wrangler r2 object put kurkalabs-public/agentsmd-kit.zip --file=../dist/agentsmd-kit.zip --content-type=application/zip --remote
   ```

4. Deploy the Worker:
   ```
   cd worker && npx wrangler deploy
   ```
   Verify deployment succeeded -- check the output for the deployed URL and confirm the route `agentsmd.kurkalabs.dev/api/*` is bound.

5. Deploy the Pages site:
   ```
   npx wrangler pages deploy landing/public --project-name=agentsmd-kit --branch=main
   ```
   Use `--branch=main` for production. Never `--branch=production`.

6. Smoke test:
   ```
   curl https://agentsmd.kurkalabs.dev/api/health
   ```
   Expect `{"ok":true,"service":"agentsmd-kit-subscribe"}`.

7. Report back: what was deployed, what URLs are live, any warnings.
