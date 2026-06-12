---
type: pr
source: github
date: 2026-06-12
url: https://github.com/modernagencysales/maestro-v2-3/pull/7
title: "[DRAFT] fix(security): auth-gate remaining raw-userId public functions [EH-1] MAE-235"
repo: modernagencysales/maestro-v2-3
number: 7
state: open
author: kimprobably
---

# [DRAFT] fix(security): auth-gate remaining raw-userId public functions [EH-1] MAE-235

PR #7 in modernagencysales/maestro-v2-3 — open — by kimprobably — [https://github.com/modernagencysales/maestro-v2-3/pull/7](https://github.com/modernagencysales/maestro-v2-3/pull/7)

## DRAFT — Security Hotfix: EH-1 (MAE-235)

> **This is a DRAFT PR for Tim's review. Do not merge without explicit sign-off.**

### What was broken

An independent audit found 16 public Convex functions that accepted a caller-supplied `userId` argument with no authentication check. Any authenticated (or unauthenticated in demo mode) caller could pass any arbitrary `userId` and read or write another user's data — a classic cross-tenant IDOR.

### What this fixes

**`packages/convex/convex/agents/orchestrator.ts`**
- `sendCoPilotMessage` — adds `requireSelfUserActionAccess` as the first statement
- `sendCoPilotVoiceMessage` — adds `requireSelfUserActionAccess` as the first statement

**`packages/convex/convex/agents/content.ts`** (7 ungated actions)
- `draftPost`, `draftPostFromVoiceMemo`, `searchTrending`, `improvePost`, `suggestComment`, `repurposeContent`, `generateCarousel` — each gets `requireSelfUserActionAccess` as the first statement

**`packages/convex/convex/agents/email.ts`** (all 6 public functions — zero guards existed)
- `createSequence`, `updateSequenceStep`, `triggerSequence`, `listSequences`, `listSequencesForMcp`, `listSequenceRuns`, `listSequenceRunsForMcp` — each gets `requireLegacySelfUserId` (exact content.ts pattern)
- Note: `createSequence` also removes the previous `ctx.db.get(args.userId)` check, which verified existence but not identity — `requireLegacySelfUserId` fully replaces it with an identity-verified lookup

### Auth library used (no new abstractions)

| Function type | Guard used | Source |
|---|---|---|
| `action` | `requireSelfUserActionAccess(ctx, args.userId)` | `lib/auth/requireUserAccess.ts` (existing) |
| `query` / `mutation` | `requireLegacySelfUserId(ctx, args.userId)` | Local helper, exact copy of content.ts pattern |

`requireSelfUserActionAccess` internally calls an internal mutation (`resolveSelfUserActionAccess`) which calls `ensureSelfUserAccess` — this is the established action-safe auth path already in the codebase.

### Regression tests

Extended `v1Killer.test.ts` with 3 new static-analysis tests that will fail CI if any guard is removed in the future:
- `"keeps legacy content action APIs guarded by authenticated user"` — checks all 7 content actions
- `"keeps email sequence APIs guarded by authenticated user"` — checks all 6 email functions
- `"keeps orchestrator co-pilot actions guarded by authenticated user"` — checks both orchestrator actions

### Review checklist for Tim

- [ ] Guard placement is correct (first statement in handler, before any db reads)
- [ ] `email.ts` `createSequence` — confirm removing the old `ctx.db.get` check is acceptable (the new guard does a stricter identity-verified lookup via Clerk)
- [ ] No regression on the co-pilot flow (sendCoPilotMessage / sendCoPilotVoiceMessage)
- [ ] No regression on the content generation flow (draftPost etc.) — the guard adds one internal mutation round-trip per call
- [ ] CI passes (v1Killer + typecheck)

🤖 Generated with [Claude Code](https://claude.com/claude-code)
