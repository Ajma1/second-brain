---
type: issue
source: linear
date: 2026-05-21
url: "https://linear.app/modern-agency-sales/issue/MAE-36/26-brain-learnfabro-internal-feedback-distillation"
title: "2.6 · brain-learn.fabro — internal-feedback distillation"
identifier: "MAE-36"
status: "Backlog"
assignee: "ajmal@maestrogtm.com"
team: "MAE"
---

# 2.6 · brain-learn.fabro — internal-feedback distillation

- **Identifier:** MAE-36
- **Status:** Backlog
- **Assignee:** ajmal@maestrogtm.com
- **Team:** MAE
- **Updated:** 2026-05-21
- **URL:** https://linear.app/modern-agency-sales/issue/MAE-36/26-brain-learnfabro-internal-feedback-distillation

**Why.** This is a self-improving loop at the *product* level: a campaign runs, its performance (open rates, conversions, which hook won) gets distilled into `internal` brain claims, and the *next* campaign generation grounds its choices in what actually worked. The brain learns from results.

**What.** `brain-learn.fabro` — invoked by campaign-runtime with a performance summary; an LLM judge distils notable signals into grounded `internal` claims (each citing the campaign that produced it). No invented metrics; grounding-validated.

**Read first.** [Team Onboarding — "Self-improving loops"](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/team-onboarding.md>) · [Spec 2 — The Brain](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/specs/brain.md>)

**Done when.** `brain-learn.fabro` takes a campaign performance summary and emits grounded `internal` claims each citing the campaign that produced it, with no invented metrics, all grounding-validated — proven by running a fixture performance summary through it.
