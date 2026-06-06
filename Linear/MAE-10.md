---
type: issue
source: linear
date: 2026-05-21
url: https://linear.app/modern-agency-sales/issue/MAE-10/p4-provision-convex-project-auth-daytona-and-env-vars
title: "P4 · Provision Convex project, auth, Daytona, and env vars"
identifier: MAE-10
status: Backlog
priority: None
team: Maestro V2
project: Maestro V2 MVP
tags: [linear, issue]
---

# P4 · Provision Convex project, auth, Daytona, and env vars

MAE-10 — Backlog — priority None — team Maestro V2 — by Tim Keen
https://linear.app/modern-agency-sales/issue/MAE-10/p4-provision-convex-project-auth-daytona-and-env-vars

**Why.** The product needs its data store (Neon Postgres) and its sandbox provider (Daytona) provisioned, plus the shared API keys set. Note: trailing whitespace / newlines in env vars has caused real production incidents before — it must be verified, not assumed.

**What.** Provision the shared Neon project and Daytona. Set the Maestro-level env vars: `THESYS_API_KEY`, `RESEND_API_KEY`, `RESEND_WEBHOOK_SECRET`, and the **image-model API key** f… (truncated, use `get_issue` for full description)
