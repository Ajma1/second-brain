---
type: issue
source: linear
date: 2026-05-21
url: https://linear.app/modern-agency-sales/issue/MAE-71/71-convex-edit-capture-edits-table
title: "7.1 · Convex edit capture + edits table"
identifier: MAE-71
status: Backlog
priority: None
team: Maestro V2
project: Maestro V2 MVP
tags: [linear, issue]
---

# 7.1 · Convex edit capture + edits table

MAE-71 — Backlog — priority None — team Maestro V2 — by Tim Keen
https://linear.app/modern-agency-sales/issue/MAE-71/71-convex-edit-capture-edits-table

**Why.** Every time a human edits generated content, that's a *signal* — it tells us where the generator fell short. Edit Learning is the loop that captures it. Step one: capture every edit with a before/after diff. (This was a key concept in the v1 we shipped.)

**What.** The `edits` Neon table; diff computation; the `edit.log` command, wired into the Spec 1 node-write path so it fires on every `human_edited` transition (cockpit or CLI).

**Rea… (truncated, use `get_issue` for full description)
