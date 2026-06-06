---
type: pr
source: github
date: 2026-05-21
url: https://github.com/modernagencysales/maestro-v2/pull/2
title: "feat(pricing): add /pricing route with three tiers"
repo: modernagencysales/maestro-v2
number: 2
state: open
author: kimprobably
---

# feat(pricing): add /pricing route with three tiers

PR #2 in modernagencysales/maestro-v2 — open — by kimprobably — [https://github.com/modernagencysales/maestro-v2/pull/2](https://github.com/modernagencysales/maestro-v2/pull/2)

## Summary
Implements the plan from `plan.md` (Issue #1 — factory smoke test): adds a static, dark-mode-aware `/pricing` route in the App Router with three pricing tiers.

## Changes
- **New**: `app/pricing/page.tsx` — Server Component with inline `tiers` data and Tailwind-only styling. Renders Starter ($19), Pro ($49, featured + "Most popular" badge), Enterprise ($199) cards in a responsive 1-col → 3-col grid (`md:grid-cols-3`). Uses semantic `bg-background`/`text-foreground` tokens so dark mode works via the existing `prefers-color-scheme` block.
- **No other files modified**. No new dependencies. `app/page.tsx` untouched.

## Verification
- ✅ `pnpm typecheck` passes
- ✅ `pnpm lint` passes
- ✅ `pnpm build` passes — `/pricing` listed as a static (○) prerendered route
- ✅ `git diff package.json pnpm-lock.yaml` is empty

## Acceptance criteria mapping
- [x] Route `/pricing` exists and prerenders statically
- [x] Three cards: Starter, Pro, Enterprise (in that order)
- [x] Each card shows name, price (`$N/mo`), 4–5 feature bullets, a CTA button
- [x] Responsive: stacked on mobile, 3-column at `md` and above
- [x] Dark-mode aware via semantic tokens
- [x] Tab title is `Pricing — Maestro` (em-dash U+2014)
- [x] Homepage unchanged; no new deps

<!-- This is an auto-generated comment: release notes by coderabbit.ai -->
## Summary by CodeRabbit

* **New Features**
  * Added a dedicated Pricing page presenting three subscription tiers (Starter, Pro, Enterprise) with monthly pricing, detailed feature lists, feature checkmarks, and prominent call-to-action buttons; the Pro plan is highlighted as the recommended/featured option.
* **Documentation**
  * Added a detailed implementation and verification plan describing the Pricing page structure, UI behavior, and acceptance checklist.

<!-- review_stack_entry_start -->

[![Review Change Stack](https://storage.googleapis.com/coderabbit_public_assets/review-stack-in-coderabbit-ui.svg)](https://app.coderabbit.ai/change-stack/modernagencysales/maestro-v2/pull/2?utm_source=github_walkthrough&utm_medium=github&utm_campaign=change_stack)

<!-- review_stack_entry_end -->
<!-- end of auto-generated comment: release notes by coderabbit.ai -->
