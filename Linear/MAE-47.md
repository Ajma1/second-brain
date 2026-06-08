---
type: issue
source: linear
date: 2026-05-21
url: "https://linear.app/modern-agency-sales/issue/MAE-47/43-campaign-generatefabro-grounding-integration"
title: "4.3 · campaign-generate.fabro + grounding integration"
identifier: "MAE-47"
status: "Backlog"
assignee: "ajmal@maestrogtm.com"
team: "MAE"
---

# 4.3 · campaign-generate.fabro + grounding integration

- **Identifier:** MAE-47
- **Status:** Backlog
- **Assignee:** ajmal@maestrogtm.com
- **Team:** MAE
- **Updated:** 2026-05-21
- **URL:** https://linear.app/modern-agency-sales/issue/MAE-47/43-campaign-generatefabro-grounding-integration

**Why.** This is the generation engine — brain → a whole campaign. The grounding rule is enforced hard here: every generated unit must cite a real brain claim, uncited units are dropped, and if a *required* object (lead magnet, opt-in page, delivery email) loses all its units the run fails. That's how a campaign stays provably grounded.

**What.** `campaign-generate.fabro` — fanout object generation, grounding validation, required-object hard-fail, >30%-drop hard-fail. Writes the objects + the campaign manifest.

**Read first.** [Team Onboarding](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/team-onboarding.md>) · [Spec 4 — Campaign Generation](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/specs/campaign-generate.md>)

**Done when.** `campaign-generate.fabro` (+ toml runner) generates objects + a campaign manifest for the reference tenant where every unit carries an inline `<!-- grounds: cl-… -->`, an adversarial ungrounded unit is dropped at grounding, a required object losing all units hard-fails the run, `trace.node` walks the campaign down to brain claims, and `maestro verify workflow-quality` passes with a leaks-tenant fixture failing.
