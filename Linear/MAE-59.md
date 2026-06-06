---
type: issue
source: linear
date: 2026-05-21
url: https://linear.app/modern-agency-sales/issue/MAE-59/57-webhooks-suppression-unsubscribe
title: "5.7 · Webhooks + suppression + unsubscribe"
identifier: MAE-59
status: Backlog
priority: None
team: Maestro V2
project: Maestro V2 MVP
tags: [linear, issue]
---

# 5.7 · Webhooks + suppression + unsubscribe

MAE-59 — Backlog — priority None — team Maestro V2 — by Tim Keen
https://linear.app/modern-agency-sales/issue/MAE-59/57-webhooks-suppression-unsubscribe

**Why.** Two things: we need delivery feedback (opens, clicks, bounces) to track the funnel; and CAN-SPAM compliance — one-click unsubscribe and a suppression list — is legally non-negotiable, not a v2 nicety.

**What.** `POST /webhooks/resend` (Resend signature verified; the secret is trimmed + length-checked — trailing whitespace has caused incidents) → `campaign_events`; a bounce/complaint adds the email to `suppressions`; `GET /u/<token>` un… (truncated, use `get_issue` for full description)
