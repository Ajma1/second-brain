---
type: pr
source: github
date: 2026-01-27
url: https://github.com/modernagencysales/gtm-system/pull/5
title: "feat: Simplify UI with dark mode and Linear design system"
repo: modernagencysales/gtm-system
number: 5
state: closed
author: kimprobably
---

# feat: Simplify UI with dark mode and Linear design system

PR #5 in modernagencysales/gtm-system — closed — by kimprobably — [https://github.com/modernagencysales/gtm-system/pull/5](https://github.com/modernagencysales/gtm-system/pull/5)

## Summary

Simplifies the UI and implements the Linear design system with dark mode as the default theme. The changes address the cluttered interface and unclear workflow.

## Changes

### UI Simplification
- Added sidebar navigation for main sections (Dashboard, Content Pipeline, Analytics)
- Consolidated content pipeline tabs from 8 to 5 (merged Planner, Styles, Sources into Settings)
- Cleaned up dashboard header with minimal elements

### Linear Design System
- Dark mode as default (no light mode toggle)
- Zinc color scale: zinc-950 background, zinc-900 surfaces, zinc-800 borders
- Violet-500 accents for primary actions and active states
- Inter font family with font-medium/font-semibold weights
- Subtle borders instead of shadows
- Consistent rounded-lg corners

### New Components
- `src/components/layout/Sidebar.tsx` - Navigation sidebar with logo, nav items, and user section
- `src/components/content-pipeline/settings/SettingsView.tsx` - Unified settings combining Business Context, Writing Styles, and Transcript Sources

### Modified Components
- Dashboard page now uses sidebar layout
- All dashboard components updated with dark mode styling (MetricCard, FunnelChart, LeadSourceChart, ActivityFeed, SystemHealth, CampaignTable)
- BusinessContextForm restyled for dark mode

### Code Quality Improvements
- Added env var validation for Supabase client (fail-fast on missing config)
- Added res.ok checks to deletion handlers for better error feedback
- Used Intl.NumberFormat for consistent percentage formatting

## Testing

- TypeScript compilation passes (`npx tsc --noEmit`)
- All 41 unit tests pass (`npm test`)

## Breaking Changes

None. This is a visual/UX update only.

---

Resolves [MOD-34](https://linear.app/modern-agency-sales/issue/MOD-34/simplify-ui-and-make-it-easier-to-use)

🤖 Generated with [Claude Code](https://claude.com/claude-code)
<!-- devin-review-badge-begin -->

---

<a href="https://app.devin.ai/review/kimprobably/gtm-system/pull/5">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://static.devin.ai/assets/gh-open-in-devin-review-dark.svg?v=1">
    <img src="https://static.devin.ai/assets/gh-open-in-devin-review-light.svg?v=1" alt="Open with Devin">
  </picture>
</a>
<!-- devin-review-badge-end -->
