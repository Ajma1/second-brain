---
type: issue
source: linear
date: 2026-05-22
url: "https://linear.app/modern-agency-sales/issue/MAE-82/610-optional-honcho-fallback-spike"
title: "6.10 · Optional Honcho fallback spike"
identifier: "MAE-82"
status: "Backlog"
assignee: "ajmal@maestrogtm.com"
team: "MAE"
---

# 6.10 · Optional Honcho fallback spike

- **Identifier:** MAE-82
- **Status:** Backlog
- **Assignee:** ajmal@maestrogtm.com
- **Team:** MAE
- **Updated:** 2026-05-22
- **URL:** https://linear.app/modern-agency-sales/issue/MAE-82/610-optional-honcho-fallback-spike

**Why.** Honcho is the agents' memory layer. We self-host it (keeps agent memory in infra we own, consistent with the rest of the product). Step one is to deploy it.

**What.** Deploy Honcho's open-source core — Postgres + the background reasoning workers — as a service alongside the campaign-runtime. The reasoning worker uses one of our model providers.

**Read first.** [Team Onboarding](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/team-onboarding.md>) · [Agent Memory Spec (Honcho)](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/specs/agent-memory.md>)

**Done when.** Honcho's open-source core (Postgres + background reasoning workers) is deployed and reachable as a service alongside the campaign-runtime, with the reasoning worker wired to one of our model providers, and a workspace + peer + session can be created against it.
