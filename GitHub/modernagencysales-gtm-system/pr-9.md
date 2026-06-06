---
type: pr
source: github
date: 2026-02-05
url: https://github.com/modernagencysales/gtm-system/pull/9
title: "feat: DM Intelligence system for LinkedIn DM prioritization"
repo: modernagencysales/gtm-system
number: 9
state: closed
author: kimprobably
---

# feat: DM Intelligence system for LinkedIn DM prioritization

PR #9 in modernagencysales/gtm-system — closed — by kimprobably — [https://github.com/modernagencysales/gtm-system/pull/9](https://github.com/modernagencysales/gtm-system/pull/9)

## Summary

- **DM Intelligence dashboard** for prioritizing LinkedIn DM conversations from Kondo
- **ICP-based scoring**: Configure allowed titles, countries, and company sizes to automatically flag high-value contacts
- **Engagement scoring**: Recency, frequency, and inbound-last signals to surface active conversations
- **AI Coach**: Claude-powered response suggestions with style matching and qualification ladder reasoning
- **Negative signal detection**: Auto-flag contacts showing disinterest for removal from outreach

## What's Included

### Backend
- Database migration: `dm_contacts`, `dm_messages`, `icp_rules` tables with RLS
- Enhanced Kondo webhook to process V2 payloads and build conversation threads
- ICP evaluation and engagement scoring logic (683 lines of tests)
- AI response suggestion API using Claude claude-sonnet-4-20250514

### Dashboard UI
- `/dashboard/dm` - Priority Queue (ICP matches sorted by engagement)
- `/dashboard/dm/remove` - Remove List (contacts flagged for removal)
- `/dashboard/dm/settings` - ICP Settings (configure title/country/company size rules)
- `/dashboard/dm/[contactId]` - Conversation View with AI Coach panel

## Test Plan

- [x] 802 tests passing (includes 683 new DM scoring tests)
- [x] TypeScript compilation clean
- [ ] Manual test: Configure ICP rules in settings
- [ ] Manual test: View priority queue with Kondo data
- [ ] Manual test: Get AI suggestion for a conversation
- [ ] Manual test: Copy suggested response to clipboard

## Screenshots

_Dashboard UI ready for manual testing once deployed_

---

🤖 Generated with [Claude Code](https://claude.ai/code)
<!-- devin-review-badge-begin -->

---

<a href="https://app.devin.ai/review/kimprobably/gtm-system/pull/9">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://static.devin.ai/assets/gh-open-in-devin-review-dark.svg?v=1">
    <img src="https://static.devin.ai/assets/gh-open-in-devin-review-light.svg?v=1" alt="Open with Devin">
  </picture>
</a>
<!-- devin-review-badge-end -->
