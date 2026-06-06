---
type: issue
source: linear
date: 2026-05-21
url: https://linear.app/modern-agency-sales/issue/MAE-54/52-public-opt-in-page-route-render
title: "5.2 · Public opt-in page route + render"
identifier: MAE-54
status: Backlog
priority: None
team: Maestro V2
project: Maestro V2 MVP
tags: [linear, issue]
---

# 5.2 · Public opt-in page route + render

MAE-54 — Backlog — priority None — team Maestro V2 — by Tim Keen
https://linear.app/modern-agency-sales/issue/MAE-54/52-public-opt-in-page-route-render

**Why.** The opt-in page is what a prospect sees — it has to load *instantly*. So it's served from Neon (a pre-rendered cache), never waking a Daytona sandbox. It's owned static HTML (not a thesys artifact) because a landing page needs speed, SEO, and no third-party-SDK dependency.

**What.** `GET /<tenant>/<campaign-slug>` — serves the rendered page from `campaign_pages` in Neon; the page is rendered from the opt-in-page object's MDX via `remar… (truncated, use `get_issue` for full description)
