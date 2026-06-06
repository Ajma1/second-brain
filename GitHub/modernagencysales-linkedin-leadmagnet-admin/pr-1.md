---
type: pr
source: github
date: 2026-02-25
url: https://github.com/modernagencysales/linkedin-leadmagnet-admin/pull/1
title: "feat: make profile rewrite editable in admin portal (MOD-311)"
repo: modernagencysales/linkedin-leadmagnet-admin
number: 1
state: closed
author: cyrusagent[bot]
---

# feat: make profile rewrite editable in admin portal (MOD-311)

PR #1 in modernagencysales/linkedin-leadmagnet-admin — closed — by cyrusagent[bot] — [https://github.com/modernagencysales/linkedin-leadmagnet-admin/pull/1](https://github.com/modernagencysales/linkedin-leadmagnet-admin/pull/1)

## Summary
- Make the profile rewrite output (headline, bio, voice style guide) editable in the admin portal so the team can iterate before shipping to clients
- Add `PATCH /api/prospects/update` endpoint with field whitelisting for secure direct-to-Supabase updates
- New `EditableProfileSection` component with Edit/Save/Cancel flow and unsaved changes indicator

## Changes
- **New file**: `app/api/prospects/update/route.ts` — API route that updates `recommended_headline`, `recommended_bio`, and `voice_style_guide` on the `prospects` table. Uses service role client, admin cookie auth, and a strict field whitelist.
- **New file**: `components/prospects/editable-profile-section.tsx` — Client component with toggle between read-only and edit modes. Headline uses text input, bio and voice style guide use resizable textareas.
- **Modified**: `components/prospects/prospect-detail.tsx` — Replaced the read-only Recommended Profile card with the new EditableProfileSection component.

## Testing
- All 51 unit tests passing
- TypeScript clean (`tsc --noEmit`)
- ESLint clean
- Production build succeeds with new `/api/prospects/update` route listed

## Linear Issue
[MOD-311: Profile rewrite in admin portal is uneditable](https://linear.app/modern-agency-sales/issue/MOD-311/profile-rewrite-in-admin-portal-is-uneditable)
