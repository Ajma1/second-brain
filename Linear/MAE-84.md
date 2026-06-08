---
type: issue
source: linear
date: 2026-05-22
url: "https://linear.app/modern-agency-sales/issue/MAE-84/612-wire-agents-to-memory-eval"
title: "6.12 · Wire agents to memory.* + eval"
identifier: "MAE-84"
status: "Backlog"
assignee: "ajmal@maestrogtm.com"
team: "MAE"
---

# 6.12 · Wire agents to memory.* + eval

- **Identifier:** MAE-84
- **Status:** Backlog
- **Assignee:** ajmal@maestrogtm.com
- **Team:** MAE
- **Updated:** 2026-05-22
- **URL:** https://linear.app/modern-agency-sales/issue/MAE-84/612-wire-agents-to-memory-eval

**Why.** Agent memory only matters once agents actually use it. This issue wires the agents and proves the memory is useful.

**What.** Wire **Hermes** (its Slack-thread statefulness) and the **cockpit AI-Toolkit editing agent** to Honcho memory via `memory.*`. `agent-memory-quality.yaml` eval (the returned representation is relevant, not invented). `docs/AGENT-MEMORY.md`.

**Read first.** [Team Onboarding](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/team-onboarding.md>) · [Agent Memory Spec (Honcho)](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/specs/agent-memory.md>)

**Done when.** Hermes and the cockpit AI-Toolkit editing agent are wired to Honcho memory via `memory.*`, `memory.context` returns a useful operator representation on the next session, `evals/agent-memory-quality.yaml` passes (representation relevant, not invented), and `docs/AGENT-MEMORY.md` documents the two-memory boundary, the Honcho mapping, and the self-host runbook.
