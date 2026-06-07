---
type: pr
source: github
date: 2026-06-07
url: https://github.com/modernagencysales/maestro-v2-3/pull/3
title: "feat: add LinkedIn public profile fallback"
repo: modernagencysales/maestro-v2-3
number: 3
state: closed
author: Ajma1
---

# feat: add LinkedIn public profile fallback

PR #3 in modernagencysales/maestro-v2-3 — closed — by Ajma1 — [https://github.com/modernagencysales/maestro-v2-3/pull/3](https://github.com/modernagencysales/maestro-v2-3/pull/3)

## Summary
- add an action-only Apify-shaped public LinkedIn profile fallback for Harvest zero-post imports
- normalize fallback posts as provider `apify` and persist through the existing history import path
- add global fallback rate limiting plus handoff docs for MAS Blueprint/maestro-os LinkedIn data-source findings

## Verification
- `CONVEX_DEPLOYMENT=beaming-canary-625 pnpm --filter @maestro/convex codegen`
- `pnpm --filter @maestro/convex test -- convex/workflows/__tests__/historyImport.test.ts convex/lib/__tests__/linkedinHistoryImport.test.ts convex/lib/__tests__/rateLimits.test.ts`
- `pnpm --filter @maestro/convex typecheck`
- `pnpm lint`
- `git diff --check`
- `pnpm test`
- `pnpm build`

## Remaining live proof
- configure `APIFY_API_TOKEN` in Convex or provide a real LinkedIn export ZIP
- rerun import against a real 50+ authored-post profile/account

<!-- This is an auto-generated comment: release notes by coderabbit.ai -->

## Summary by CodeRabbit

## Release Notes

* **New Features**
  * Added automatic fallback mechanism for LinkedIn history import: when the primary history source returns no posts, the system now attempts to retrieve data from a public profile source.

* **Improvements**
  * Enhanced reliability of LinkedIn data import with improved error handling and fallback support.

<!-- end of auto-generated comment: release notes by coderabbit.ai -->
