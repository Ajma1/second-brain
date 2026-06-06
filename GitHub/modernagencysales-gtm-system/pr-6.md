---
type: pr
source: github
date: 2026-01-24
url: https://github.com/modernagencysales/gtm-system/pull/6
title: "fix: Handle email confirmation errors on login (MOD-35)"
repo: modernagencysales/gtm-system
number: 6
state: closed
author: cyrusagent[bot]
---

# fix: Handle email confirmation errors on login (MOD-35)

PR #6 in modernagencysales/gtm-system — closed — by cyrusagent[bot] — [https://github.com/modernagencysales/gtm-system/pull/6](https://github.com/modernagencysales/gtm-system/pull/6)

## Summary
- Fix login error handling for users who registered but haven't confirmed their email
- Add helpful error messages explaining the need to confirm email
- Add "Resend Confirmation Email" button for users with unconfirmed accounts

## Test plan
- [x] Verify login shows helpful message when email not confirmed
- [x] Verify "Resend Confirmation Email" button appears
- [x] Verify generic "Invalid login credentials" error includes guidance about email confirmation

🤖 Generated with [Claude Code](https://claude.com/claude-code)
