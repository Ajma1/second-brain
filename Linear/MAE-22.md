---
type: issue
source: linear
date: 2026-05-21
url: https://linear.app/modern-agency-sales/issue/MAE-22/12-the-grounding-validator
title: "1.2 · The grounding validator"
identifier: MAE-22
status: Backlog
priority: None
team: Maestro V2
project: Maestro V2 MVP
tags: [linear, issue]
---

# 1.2 · The grounding validator

MAE-22 — Backlog — priority None — team Maestro V2 — by Tim Keen
https://linear.app/modern-agency-sales/issue/MAE-22/12-the-grounding-validator

**Why.** The grounding rule — every node cites something real one layer down, and a cited quote actually appears in the cited lines — is the product's quality floor *and* its moat. The validator enforces it mechanically. This is the single most important piece of code in Stage 1: if generated content can cite nothing and survive, the "everything traces to a real source" promise is dead.

**What.** `core/grounding.ts` — a shared function: every `… (truncated, use `get_issue` for full description)
