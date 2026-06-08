---
type: issue
source: linear
date: 2026-05-22
url: "https://linear.app/modern-agency-sales/issue/MAE-77/0g-spike-agent-memory-convex-first-honcho-fallback"
title: "0g · Spike agent memory: Convex first, Honcho fallback"
identifier: "MAE-77"
status: "Backlog"
assignee: "ajmal@maestrogtm.com"
team: "MAE"
---

# 0g · Spike agent memory: Convex first, Honcho fallback

- **Identifier:** MAE-77
- **Status:** Backlog
- **Assignee:** ajmal@maestrogtm.com
- **Team:** MAE
- **Updated:** 2026-05-22
- **URL:** https://linear.app/modern-agency-sales/issue/MAE-77/0g-spike-agent-memory-convex-first-honcho-fallback

**Why.** Honcho is the agents' memory layer (Stage 6) — new to us, and self-hosted. Spike it before Stage 6 commits, so we know the integration shape and the operational load.

**What.** Stand up Honcho's open-source core; model an operator peer + an agent peer and a session; write interaction messages; query a representation (`chat`/get-context). Throwaway — feeds the agent-memory spec.

**Read first.** [Team Onboarding](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/team-onboarding.md>) · [Agent Memory Spec (Honcho)](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/specs/agent-memory.md>)

**Done when.** Self-hosted Honcho's open-source core is running, an operator peer + agent peer + session have been modelled and a representation queried back, the integration-shape and operational-load verdict is recorded, and that finding is folded into the agent-memory spec as a Decision Log entry.
