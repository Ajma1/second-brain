---
type: issue
source: linear
date: 2026-05-21
url: https://linear.app/modern-agency-sales/issue/MAE-55/53-form-capture-the-lead-store
title: "5.3 · Form capture + the lead store"
identifier: MAE-55
status: Backlog
priority: None
team: Maestro V2
project: Maestro V2 MVP
tags: [linear, issue]
---

# 5.3 · Form capture + the lead store

MAE-55 — Backlog — priority None — team Maestro V2 — by Tim Keen
https://linear.app/modern-agency-sales/issue/MAE-55/53-form-capture-the-lead-store

**Why.** Form capture is the conversion event — it's the whole point of the funnel. We store leads as a proper model (`companies` + `contacts` + `captures`), not a flat table, so the same person opting into two campaigns is one contact with two captures, and leads roll up to companies.

**What.** `POST /<tenant>/<campaign-slug>/capture` — validate against the page's capture schema; resolve/create the company (from the email domain) + the contact… (truncated, use `get_issue` for full description)
