---
type: issue
source: linear
date: 2026-05-21
url: https://linear.app/modern-agency-sales/issue/MAE-26/16-convex-tenantmembershipevent-tables-auth-helpers
title: "1.6 · Convex tenant/membership/event tables + auth helpers"
identifier: MAE-26
status: Backlog
priority: None
team: Maestro V2
project: Maestro V2 MVP
tags: [linear, issue]
---

# 1.6 · Convex tenant/membership/event tables + auth helpers

MAE-26 — Backlog — priority None — team Maestro V2 — by Tim Keen
https://linear.app/modern-agency-sales/issue/MAE-26/16-convex-tenantmembershipevent-tables-auth-helpers

**Why.** This is the Neon half of the two-store model. Tenant isolation here is *logical* (one shared database, every row tagged `tenant_slug`) — so it must be enforced hard: the DataScope accessor injects the tenant filter on every query, and Row-Level Security is the database-level backstop, so even a buggy query physically cannot return another client's rows.

**What.** The Neon connection module; the tenant-scoped DataScope accessor; the RLS… (truncated, use `get_issue` for full description)
