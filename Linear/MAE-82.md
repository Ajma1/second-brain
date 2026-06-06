---
type: issue
source: linear
date: 2026-05-22
url: https://linear.app/modern-agency-sales/issue/MAE-82/610-optional-honcho-fallback-spike
title: "6.10 · Optional Honcho fallback spike"
identifier: MAE-82
status: Backlog
priority: None
team: Maestro V2
project: Maestro V2 MVP
tags: [linear, issue]
---

# 6.10 · Optional Honcho fallback spike

MAE-82 — Backlog — priority None — team Maestro V2 — by Tim Keen
https://linear.app/modern-agency-sales/issue/MAE-82/610-optional-honcho-fallback-spike

**Why.** Honcho is the agents' memory layer. We self-host it (keeps agent memory in infra we own, consistent with the rest of the product). Step one is to deploy it.

**What.** Deploy Honcho's open-source core — Postgres + the background reasoning workers — as a service alongside the campaign-runtime. The reasoning worker uses one of our model providers.

**Read first.** [Team Onboarding](<https://github.com/modernagencysales/maestro-agent-plann… (truncated, use `get_issue` for full description)
