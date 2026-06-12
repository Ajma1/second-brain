---
type: pr
source: github
date: 2026-06-12
url: https://github.com/modernagencysales/maestro-v2-3/pull/6
title: "QA round 2: stop auth-gated post pages crashing in demo mode + boundary safety net"
repo: modernagencysales/maestro-v2-3
number: 6
state: open
author: Ajma1
---

# QA round 2: stop auth-gated post pages crashing in demo mode + boundary safety net

PR #6 in modernagencysales/maestro-v2-3 — open — by Ajma1 — [https://github.com/modernagencysales/maestro-v2-3/pull/6](https://github.com/modernagencysales/maestro-v2-3/pull/6)

## Summary

Second production-hardening QA pass, run against the latest `main` (Tier-1 features, fact-check pipeline, prompt-injection hardening, eval depth — all of which landed after round 1). Re-mapped the route surface, browser-tested every page in demo mode, and fixed the demo-mode crashes found in the new surfaces.

Baseline on latest `main` was green before changes: typecheck 6/6, lint clean, **1106 tests** (convex 879, web 209, admin 8, agency 10), build 6/6.

## What changed & why

Three new pages added since round 1 call the auth-gated `getPost` query, which throws `UNAUTHENTICATED` without a Clerk session. In the no-Clerk demo workspace that exception propagated to the global error boundary and took down the **entire route** with the alarming "SOMETHING BROKE ON MAESTRO" 500:

1. **Fact-check view** (`/posts/[id]/provenance`) — `FactCheckWorkspace` now skips its auth-gated queries in demo mode and renders a calm "Fact-check needs a signed-in workspace" state (matching the round-1 score-panel pattern).
2. **Post editor** (`/posts/[id]`) — `MountedPostEditorWorkspace` does the same, keeping the app shell and showing a sign-in notice instead of crashing.
3. **Systemic safety net** (`app/error.tsx`) — a thrown `UNAUTHENTICATED` / `USER_NOT_PROVISIONED` now renders a recoverable "Sign in required" state instead of the 500, so a single auth-gated panel can never take down a whole route again. This also improves production UX for genuinely expired sessions.

## Verification

- Every web route (incl. new `/process`, `/posts/[id]`, `/posts/[id]/provenance`, `/admin/data`) browser-tested in demo mode — 0 functional console errors after the fixes.
- Public flows checked: client-invite bad-token → polished "isn't recognised" state; public lead-magnet → graceful "not found".
- typecheck 6/6 · lint clean · tests 1106 · production build 6/6 (web 36 pages, admin 11, agency 8).

## Reviewer notes

- These are demo-mode robustness fixes; production behaviour (with Clerk) is unchanged — the auth-gated queries still run and the pages render their real content.
- **Observation, not a bug:** the demo seed marks a lead magnet `status: "live"` with a `landingPageSlug` but never creates the `landingPages` row that `launchLeadMagnet` would, so its public `/p/[slug]` page shows "not found". The production launch path (`agents/leadMagnets.launchLeadMagnet`) creates the landing page correctly — this is demo-seed fidelity, not a code defect.

🤖 Generated with [Claude Code](https://claude.com/claude-code)

<!-- This is an auto-generated comment: release notes by coderabbit.ai -->

## Summary by CodeRabbit

* **Bug Fixes**
  * Improved authentication error handling with specific "Sign in required" messaging and retry functionality.
  * Fixed crashes in demo mode when accessing protected features like fact-checking and post editing by adding appropriate sign-in prompts.

<!-- end of auto-generated comment: release notes by coderabbit.ai -->
