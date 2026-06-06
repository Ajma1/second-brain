---
type: issue
source: linear
date: 2026-05-21
url: https://linear.app/modern-agency-sales/issue/MAE-23/13-the-index
title: "1.3 · The index"
identifier: MAE-23
status: Backlog
priority: None
team: Maestro V2
project: Maestro V2 MVP
tags: [linear, issue]
---

# 1.3 · The index

MAE-23 — Backlog — priority None — team Maestro V2 — by Tim Keen
https://linear.app/modern-agency-sales/issue/MAE-23/13-the-index

**Why.** The vault is just files — the index is what makes it *queryable* without a database (SilverBullet proves this pattern works). Its reverse map (cited id → who cites it) is what makes `trace` and drift detection possible. The index is a derived cache: the files are always the source of truth.

**What.** `core/index.ts` — the derived `.index.json` per node directory; the reverse map; `grounding_stale` marking (a node citing something since… (truncated, use `get_issue` for full description)
