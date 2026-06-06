---
type: issue
source: linear
date: 2026-05-21
url: https://linear.app/modern-agency-sales/issue/MAE-58/56-scheduled-email-sender-loop
title: "5.6 · Scheduled-email sender loop"
identifier: MAE-58
status: Backlog
priority: None
team: Maestro V2
project: Maestro V2 MVP
tags: [linear, issue]
---

# 5.6 · Scheduled-email sender loop

MAE-58 — Backlog — priority None — team Maestro V2 — by Tim Keen
https://linear.app/modern-agency-sales/issue/MAE-58/56-scheduled-email-sender-loop

**Why.** Sending is a *stateful* subsystem — the old version's fire-and-forget approach had no retries and no exactly-once guarantee. We make it explicit: a delivery email goes immediately, nurture emails at their delay; each email sends exactly once even across a service restart.

**What.** A poll loop over `scheduled_emails` that claims due rows transactionally (`UPDATE … RETURNING`); exactly-once via `UNIQUE (contact_id, piece)` + an idempote… (truncated, use `get_issue` for full description)
