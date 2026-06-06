---
type: issue
source: github
date: 2026-05-21
url: https://github.com/modernagencysales/maestro-v2/issues/1
title: "Add /pricing page (factory smoke test)"
repo: modernagencysales/maestro-v2
number: 1
state: closed
author: Ajma1
---

# Add /pricing page (factory smoke test)

Issue #1 in modernagencysales/maestro-v2 — closed — by Ajma1 — [https://github.com/modernagencysales/maestro-v2/issues/1](https://github.com/modernagencysales/maestro-v2/issues/1)

## Goal
Build a static `/pricing` page to validate the autonomous factory loop end-to-end.

## Acceptance criteria
- New route: `app/pricing/page.tsx` renders without errors
- Three pricing tiers: **Starter**, **Pro**, **Enterprise**
- Each tier shows: name, monthly price (USD), 3-5 bullet features, primary CTA button
- Layout: responsive 3-column grid on `md+`, stacked on mobile
- Styling: Tailwind only, dark-mode aware (use semantic classes like `bg-background`/`text-foreground` or stick with current next-app palette)
- `pnpm build` passes
- `pnpm typecheck` passes
- `pnpm lint` passes
- No new dependencies required

## Non-goals
- No backend / Stripe integration
- No auth gate
- No animation library (CSS transitions only if any)

## Notes
- The current homepage at `app/page.tsx` is the next-app default — leave it untouched
- Page title should be `Pricing — Maestro`
