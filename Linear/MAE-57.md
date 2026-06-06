---
type: issue
source: linear
date: 2026-05-21
url: https://linear.app/modern-agency-sales/issue/MAE-57/55-resend-integration-sending-subdomain-provisioning
title: "5.5 · Resend integration + sending subdomain provisioning"
identifier: MAE-57
status: Backlog
priority: None
team: Maestro V2
project: Maestro V2 MVP
tags: [linear, issue]
---

# 5.5 · Resend integration + sending subdomain provisioning

MAE-57 — Backlog — priority None — team Maestro V2 — by Tim Keen
https://linear.app/modern-agency-sales/issue/MAE-57/55-resend-integration-sending-subdomain-provisioning

**Why.** Emails have to actually *send* — and land in the inbox. Each client sends from their own subdomain so email reputation is isolated per client (one client's behaviour can't tank another's deliverability). DKIM/SPF are auto-provisioned because we control the parent DNS zone.

**What.** `integrations/resend.ts` — send via a Maestro-managed Resend account; provision a per-client sending subdomain (`<slug>.send.<maestro-domain>`) with DKIM/S… (truncated, use `get_issue` for full description)
