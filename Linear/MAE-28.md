---
type: issue
source: linear
date: 2026-05-21
url: https://linear.app/modern-agency-sales/issue/MAE-28/18-client-onboarding-workflow-client-commands
title: "1.8 · Client onboarding workflow + client.* commands"
identifier: MAE-28
status: Backlog
priority: None
team: Maestro V2
project: Maestro V2 MVP
tags: [linear, issue]
---

# 1.8 · Client onboarding workflow + client.* commands

MAE-28 — Backlog — priority None — team Maestro V2 — by Tim Keen
https://linear.app/modern-agency-sales/issue/MAE-28/18-client-onboarding-workflow-client-commands

**Why.** A tenant (one agency client) is the unit of the product — own vault, own Daytona volume, own config. Onboarding has to provision one end to end and be *idempotent*: provisioning can fail halfway (Daytona hiccups), and re-running must resume cleanly, not double-create.

**What.** `onboard-client.fabro` (validate → reserve slug via atomic mkdir → vault tree → Daytona volume → Neon registry row → seed knowledge stubs → smoke) + the `client… (truncated, use `get_issue` for full description)
