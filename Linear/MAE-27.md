---
type: issue
source: linear
date: 2026-05-21
url: https://linear.app/modern-agency-sales/issue/MAE-27/17-tenant-scope-lint-component-ownership-lint
title: "1.7 · Tenant Scope Lint + Component Ownership Lint"
identifier: MAE-27
status: Backlog
priority: None
team: Maestro V2
project: Maestro V2 MVP
tags: [linear, issue]
---

# 1.7 · Tenant Scope Lint + Component Ownership Lint

MAE-27 — Backlog — priority None — team Maestro V2 — by Tim Keen
https://linear.app/modern-agency-sales/issue/MAE-27/17-tenant-scope-lint-component-ownership-lint

**Why.** Standards are enforced by code, not by vigilance. Two lints are the mechanical guards: the **Tenant Scope Lint** ensures no Fabro workflow can touch another tenant's vault files; the **Component Ownership Lint** ensures no component reads another component's Neon tables directly (it must call that component's command). Together they keep tenants isolated and the platform modular.

**What.** Both lints, wired into `maestro verify` / CI, … (truncated, use `get_issue` for full description)
