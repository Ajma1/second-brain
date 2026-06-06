---
type: issue
source: linear
date: 2026-05-21
url: https://linear.app/modern-agency-sales/issue/MAE-56/54-lead-magnet-delivery
title: "5.4 · Lead magnet delivery"
identifier: MAE-56
status: Backlog
priority: None
team: Maestro V2
project: Maestro V2 MVP
tags: [linear, issue]
---

# 5.4 · Lead magnet delivery

MAE-56 — Backlog — priority None — team Maestro V2 — by Tim Keen
https://linear.app/modern-agency-sales/issue/MAE-56/54-lead-magnet-delivery

**Why.** Once a prospect opts in, they need the lead magnet. We serve the interactive hosted web object as the primary deliverable, with a downloadable export as a secondary option.

**What.** `GET .../magnet/<token>` serves the lead magnet web object (resolving the per-lead token); `GET .../magnet/<token>/export` serves the PDF. Each records a `downloaded` event.

**Read first.** [Team Onboarding](<https://github.com/modernagencysales/maestro-a… (truncated, use `get_issue` for full description)
