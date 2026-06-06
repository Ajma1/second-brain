---
type: pr
source: github
date: 2026-01-26
url: https://github.com/modernagencysales/gc-member-portal/pull/3
title: "Migrate LMS backend from Airtable to Supabase with cohort independence"
repo: modernagencysales/gc-member-portal
number: 3
state: closed
author: cyrusagent[bot]
---

# Migrate LMS backend from Airtable to Supabase with cohort independence

PR #3 in modernagencysales/gc-member-portal — closed — by cyrusagent[bot] — [https://github.com/modernagencysales/gc-member-portal/pull/3](https://github.com/modernagencysales/gc-member-portal/pull/3)

## Summary
- Add Supabase-backed LMS with cohort-independent curriculum management
- Add idempotent SQL migration for 7 LMS tables with RLS policies
- Add cohort duplication stored procedure for full curriculum cloning
- Add TypeScript types, service layer, and React Query hooks

## Test plan
- [ ] Verify migration runs successfully on Supabase
- [ ] Test cohort CRUD operations in admin portal
- [ ] Test cohort duplication creates independent copies
- [ ] Verify student portal shows only their cohort's visible content
- [ ] Test all content types render correctly

🤖 Generated with [Claude Code](https://claude.com/claude-code)
