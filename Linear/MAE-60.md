---
type: issue
source: linear
date: 2026-05-21
url: https://linear.app/modern-agency-sales/issue/MAE-60/58-campaignstats-funnel-tracking
title: "5.8 · campaign.stats — funnel tracking"
identifier: MAE-60
status: Backlog
priority: None
team: Maestro V2
project: Maestro V2 MVP
tags: [linear, issue]
---

# 5.8 · campaign.stats — funnel tracking

MAE-60 — Backlog — priority None — team Maestro V2 — by Tim Keen
https://linear.app/modern-agency-sales/issue/MAE-60/58-campaignstats-funnel-tracking

**Why.** Performance tracking is the campaign's funnel — and it feeds two things: the cockpit's dashboards, and the brain feedback loop (`campaign.learn` distils performance into `internal` claims). Aggregation is a SQL `GROUP BY`, not in-memory (the old version's in-memory aggregation was an overbuild).

**What.** `campaign.stats` — aggregates `campaign_events` into the funnel: page views → opt-ins (rate) → delivered/opened/clicked → downloads … (truncated, use `get_issue` for full description)
