---
type: issue
source: linear
date: 2026-05-22
url: https://linear.app/modern-agency-sales/issue/MAE-83/611-agent-memory-integration-convex-first-honcho-optional
title: "6.11 · Agent-memory integration (Convex first, Honcho optional)"
identifier: MAE-83
status: Backlog
priority: None
team: Maestro V2
project: Maestro V2 MVP
tags: [linear, issue]
---

# 6.11 · Agent-memory integration (Convex first, Honcho optional)

MAE-83 — Backlog — priority None — team Maestro V2 — by Tim Keen
https://linear.app/modern-agency-sales/issue/MAE-83/611-agent-memory-integration-convex-first-honcho-optional

**Why.** Agents that operate the product need *stateful memory* — an evolving model of the operator and of sessions. Honcho provides it. The critical constraint: Honcho must **not** become the client brain — the brain is cited, traceable grounded nodes; Honcho's representations are LLM-derived. Two memory systems, two jobs, with a hard boundary.

**What.** `integrations/honcho.ts`; the `memory.{observe,context}` command-core commands; operator +… (truncated, use `get_issue` for full description)
