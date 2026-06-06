---
type: issue
source: linear
date: 2026-05-21
url: https://linear.app/modern-agency-sales/issue/MAE-79/511-deploy-the-campaign-runtime-service-domain
title: "5.11 · Deploy the campaign-runtime service + domain"
identifier: MAE-79
status: Backlog
priority: None
team: Maestro V2
project: Maestro V2 MVP
tags: [linear, issue]
---

# 5.11 · Deploy the campaign-runtime service + domain

MAE-79 — Backlog — priority None — team Maestro V2 — by Tim Keen
https://linear.app/modern-agency-sales/issue/MAE-79/511-deploy-the-campaign-runtime-service-domain

**Why.** Completeness-check gap: the 75 original issues build all the code but nothing makes it *live*. The runtime serves public opt-in pages — it needs to be deployed, at a real domain, or there is no product.

**What.** Deploy `services/campaign-runtime/` (Railway); set up the public domain + DNS for opt-in pages and the per-client sending subdomains.

**Read first.** [Team Onboarding](<https://github.com/modernagencysales/maestro-agent-plann… (truncated, use `get_issue` for full description)
