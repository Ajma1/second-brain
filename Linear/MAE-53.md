---
type: issue
source: linear
date: 2026-05-21
url: https://linear.app/modern-agency-sales/issue/MAE-53/51-convex-runtime-tables-rollups-and-public-serving-path
title: "5.1 · Convex runtime tables, rollups, and public serving path"
identifier: MAE-53
status: Backlog
priority: None
team: Maestro V2
project: Maestro V2 MVP
tags: [linear, issue]
---

# 5.1 · Convex runtime tables, rollups, and public serving path

MAE-53 — Backlog — priority None — team Maestro V2 — by Tim Keen
https://linear.app/modern-agency-sales/issue/MAE-53/51-convex-runtime-tables-rollups-and-public-serving-path

**Why.** The runtime is an always-on public service — a stranger can hit a client's opt-in page at any moment. Its state therefore *cannot* live on a sleepy per-tenant Daytona volume (you can't wake a sandbox on the conversion path). All runtime state is Neon.

**What.** `services/campaign-runtime/` (a standalone Railway service) + `db/migrations/campaign-runtime/` — the tables: `companies`, `contacts`, `captures`, `campaign_pages`, `campaign_ev… (truncated, use `get_issue` for full description)
