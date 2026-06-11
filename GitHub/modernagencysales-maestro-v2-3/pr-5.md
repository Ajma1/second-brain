---
type: pr
source: github
date: 2026-06-11
url: https://github.com/modernagencysales/maestro-v2-3/pull/5
title: "QA hardening: close cross-tenant data leaks + fix demo-mode crashes"
repo: modernagencysales/maestro-v2-3
number: 5
state: closed
author: Ajma1
---

# QA hardening: close cross-tenant data leaks + fix demo-mode crashes

PR #5 in modernagencysales/maestro-v2-3 — closed — by Ajma1 — [https://github.com/modernagencysales/maestro-v2-3/pull/5](https://github.com/modernagencysales/maestro-v2-3/pull/5)

## Summary

Production-readiness QA pass over the whole monorepo (web + admin + agency + convex). Mapped the system, exercised every route in demo mode, ran the full verification gates, and fixed the defects found along the way.

All gates green after changes:
- **typecheck** 6/6 packages
- **lint** clean (`--max-warnings=0`)
- **tests** 6/6 (~994: convex 787, web 189, admin 8, agency 10) — includes a new TDD unit test
- **production build** 6/6 apps/packages

## What changed & why

### 1. Cross-tenant data leaks (security)
`getUserProfileForMcp` and `getBillingSubscriptionForMcp` were **public** Convex queries that returned any user's profile/email/plan/billing given only a raw `userId` (verified the leak live against the deployment). They are only ever called server-side from the MCP dispatch with a key-derived userId, so they are now `internalQuery` (confirmed `internal` via deployed function-spec). `seedGlobalBrain` was likewise a public action that could reseed platform-wide global-brain knowledge — now `internalAction`.

The remaining `userId`-arg public functions still consumed by the web client (`getMvpDataOverview`, `updateUserProfile`, `disconnectLinkedIn`) now go through a new **secure-by-default** guard `assertSelfUserUnlessDemo`: production requires an authenticated identity that owns the `userId` (closing the cross-tenant IDOR), while the no-Clerk local/demo workspace opts out explicitly via `MAESTRO_DEMO_MODE`. Documented in `.env.example` with a never-in-production note.

### 2. `/data` crashed in demo mode
`AppDataOverviewWorkspace` called `auth.ensureProvisioned` directly, which throws `UNAUTHENTICATED` without Clerk and rendered "Could not seed Convex data". Now uses the shared demo-aware `useProvisionedUser` hook like every other page.

### 3. `/brain` full-page 500 in demo mode
`ScoreWorkspace` called the auth-gated `workspaces.listMine`, which threw `UNAUTHENTICATED` and took down the whole route via the global error boundary. It now skips that query in demo mode and renders the graceful "No Score yet" connect state.

### 4. Polish: admin/agency favicon 404
Both apps lacked an `app/icon.svg` (only web had one), logging a `/favicon.ico` 404 on every page. Added the Maestro mark tinted to each portal's accent (admin blue, agency gold).

## Reviewer notes

- **Demo flag:** `MAESTRO_DEMO_MODE` must stay **unset** in production — leaving it unset is what enforces per-user tenancy. Set to `1` only on local/demo deployments without Clerk.
- **New test:** `packages/convex/convex/lib/__tests__/selfUserDemoGuard.test.ts` covers the demo-allow / production-enforce / self-vs-other / unauthenticated paths (written test-first).
- **Follow-up (not in this PR):** `storeLinkedInOAuthToken` (`users.ts`) is still a public mutation that writes a LinkedIn token onto an arbitrary `userId` with no caller check — a token-injection vector. It is called server-to-server from the OAuth callback, so it needs internalizing behind the state-verified Convex HTTP action rather than the self-guard. Tracked separately.
- **Pre-existing smell (left as-is):** when `NEXT_PUBLIC_CONVEX_URL` is unset, each app's `lib/convex-url.ts` falls back to a hardcoded `beaming-canary-625.convex.cloud`. Worth replacing with a fail-fast, but risky to change blind in this pass.

🤖 Generated with [Claude Code](https://claude.com/claude-code)

<!-- This is an auto-generated comment: release notes by coderabbit.ai -->

## Summary by CodeRabbit

## Release Notes

* **New Features**
  * Added demo mode support for local development and testing, allowing workspace and data access without Clerk authentication

* **Security & Access Control**
  * Implemented access validation for user-scoped data operations to prevent unauthorized access
  * Transitioned sensitive endpoints to internal access only, restricting to authorized callers

<!-- end of auto-generated comment: release notes by coderabbit.ai -->
