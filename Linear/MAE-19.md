---
type: issue
source: linear
date: 2026-05-21
url: "https://linear.app/modern-agency-sales/issue/MAE-19/08-factoryrelease-workflow-evals"
title: "0.8 · factory.release workflow + evals"
identifier: "MAE-19"
status: "Backlog"
assignee: "ajmal@maestrogtm.com"
team: "MAE"
---

# 0.8 · factory.release workflow + evals

- **Identifier:** MAE-19
- **Status:** Backlog
- **Assignee:** ajmal@maestrogtm.com
- **Team:** MAE
- **Updated:** 2026-05-21
- **URL:** https://linear.app/modern-agency-sales/issue/MAE-19/08-factoryrelease-workflow-evals

**Why.** The final release review + handoff is the last gate before code is considered done. As its own workflow with its own eval, nothing ships unreviewed, and "is our release review catching things?" becomes measurable.

**What.** Decompose `Final Release Review · Final Gate · Publish Handoff · Memory Summary` into `factory.release`. Workflow-level eval + per-model-output evals.

**Read first.** [Team Onboarding — "The factory"](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/team-onboarding.md>) · [Spec 0 — Modular Software Factory](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/specs/modular-software-factory.md>)

**Done when.** `factory.release.fabro` runs standalone with the final release review, final gate, publish handoff, and memory summary, and has workflow-level + per-model-output evals under `evals/factory/`.
