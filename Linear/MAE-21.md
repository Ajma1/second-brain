---
type: issue
source: linear
date: 2026-05-21
url: https://linear.app/modern-agency-sales/issue/MAE-21/11-grounded-node-library-and-schema
title: "1.1 · Grounded-node library & schema"
identifier: MAE-21
status: Backlog
priority: None
team: Maestro V2
project: Maestro V2 MVP
tags: [linear, issue]
---

# 1.1 · Grounded-node library & schema

MAE-21 — Backlog — priority None — team Maestro V2 — by Tim Keen
https://linear.app/modern-agency-sales/issue/MAE-21/11-grounded-node-library-and-schema

**Why.** The grounded node is the *one* primitive the entire product is made of — claims, campaign objects, campaigns are all grounded nodes (see Onboarding). This library is where the node's shape lives. Get it right and every layer above inherits it for free; get it wrong and everything downstream is wrong.

**What.** `core/node.ts` — the flat `<id>.md` node schema (frontmatter: id, type, citations, status, timestamps, `human_edited`) and the … (truncated, use `get_issue` for full description)
