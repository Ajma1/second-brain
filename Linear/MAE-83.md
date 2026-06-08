---
type: issue
source: linear
date: 2026-05-22
url: "https://linear.app/modern-agency-sales/issue/MAE-83/611-agent-memory-integration-convex-first-honcho-optional"
title: "6.11 · Agent-memory integration (Convex first, Honcho optional)"
identifier: "MAE-83"
status: "Backlog"
assignee: "ajmal@maestrogtm.com"
team: "MAE"
---

# 6.11 · Agent-memory integration (Convex first, Honcho optional)

- **Identifier:** MAE-83
- **Status:** Backlog
- **Assignee:** ajmal@maestrogtm.com
- **Team:** MAE
- **Updated:** 2026-05-22
- **URL:** https://linear.app/modern-agency-sales/issue/MAE-83/611-agent-memory-integration-convex-first-honcho-optional

**Why.** Agents that operate the product need *stateful memory* — an evolving model of the operator and of sessions. Honcho provides it. The critical constraint: Honcho must **not** become the client brain — the brain is cited, traceable grounded nodes; Honcho's representations are LLM-derived. Two memory systems, two jobs, with a hard boundary.

**What.** `integrations/honcho.ts`; the `memory.{observe,context}` command-core commands; operator + agent peers and sessions mapped onto Honcho; the **two-memory boundary check** + the Engineering Charter invariant.

**Read first.** [Team Onboarding — "This is a hypothesis"](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/team-onboarding.md>) · [Agent Memory Spec (Honcho)](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/specs/agent-memory.md>)

**Done when.** `integrations/honcho.ts` and the `memory.{observe,context}` command-core commands ship (registry-parity), operator + agent peers and sessions are mapped onto Honcho, and the two-memory boundary check + Charter invariant flag an agent writing citation-shaped client knowledge into Honcho (or operator/session memory into the brain).
