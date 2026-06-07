---
type: pr
source: github
date: 2026-06-07
url: https://github.com/modernagencysales/maestro-v2-3/pull/2
title: "fix: package admin user backend gates"
repo: modernagencysales/maestro-v2-3
number: 2
state: open
author: Ajma1
---

# fix: package admin user backend gates

PR #2 in modernagencysales/maestro-v2-3 — open — by Ajma1 — [https://github.com/modernagencysales/maestro-v2-3/pull/2](https://github.com/modernagencysales/maestro-v2-3/pull/2)

## Summary\n- Fix public /health auth bootstrap drift and root lint failure.\n- Move LinkedIn history import to action-only Harvest client and remove active LinkedIn history pagination path.\n- Enforce personal/team workspace kind boundary while preserving agency plan/tables.\n- Add admin audit coverage guard and build-log heartbeat evidence.\n\n## Verification\n- pnpm install --frozen-lockfile\n- pnpm lint\n- pnpm --filter @maestro/convex test -- convex/lib/__tests__/adminAuditCoverage.test.ts convex/workflows/__tests__/historyImport.test.ts convex/lib/__tests__/schemaIdentityTenancy.test.ts\n- CONVEX_DEPLOYMENT=beaming-canary-625 pnpm --filter @maestro/convex codegen\n- pnpm --filter @maestro/convex typecheck\n- pnpm test\n- pnpm build\n- pnpm typecheck\n- git diff --check\n\n## Notes\n- Built from a clean worktree based on origin/main.\n- Excludes unrelated dirty agency-branch calendar edit.\n- External gates remain: real Harvest 50+ post proof, LinkedIn export upload, Voice DNA human review, provider log streaming alert proof.

<!-- This is an auto-generated comment: release notes by coderabbit.ai -->
## Summary by CodeRabbit

* **Bug Fixes**
  * Prevent workspace bootstrapping from running on the public health endpoint.

* **Refactor**
  * Removed "agency" workspace kind — workspaces are now "personal" or "team".
  * History import now uses the Harvest-backed importer (Harvest client/action).

* **Tests**
  * Added auth-provisioning, audit-coverage, schema-tenancy, and history-import workflow tests.

* **Documentation**
  * Added multiple heartbeat/status build-log entries.

* **Chores**
  * Updated ESLint ignore list. 

* **Style**
  * Minor avatar component markup cleanup.
<!-- end of auto-generated comment: release notes by coderabbit.ai -->
