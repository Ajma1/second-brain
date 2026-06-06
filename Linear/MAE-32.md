---
type: issue
source: linear
date: 2026-05-21
url: https://linear.app/modern-agency-sales/issue/MAE-32/22-brain-extractfabro-extraction-prompt
title: "2.2 · brain-extract.fabro + extraction prompt"
identifier: MAE-32
status: Backlog
priority: None
team: Maestro V2
project: Maestro V2 MVP
tags: [linear, issue]
---

# 2.2 · brain-extract.fabro + extraction prompt

MAE-32 — Backlog — priority None — team Maestro V2 — by Tim Keen
https://linear.app/modern-agency-sales/issue/MAE-32/22-brain-extractfabro-extraction-prompt

**Why.** Extraction — turning a document into cited claims via a cheap model — is the shared primitive both source ingestion (Spec 2) and research (Spec 3) use. Running it **per-artifact, in parallel, on a cheap model** is the cost-control layer: you never feed thousands of raw scraped comments to an expensive long-context model. (We tested generic prose-compression — scaledown — and it dropped signals; claim-extraction is the right primitive.)
… (truncated, use `get_issue` for full description)
