---
type: issue
source: linear
date: 2026-05-21
url: "https://linear.app/modern-agency-sales/issue/MAE-71/71-convex-edit-capture-edits-table"
title: "7.1 · Convex edit capture + edits table"
identifier: "MAE-71"
status: "Backlog"
assignee: "ajmal@maestrogtm.com"
team: "MAE"
---

# 7.1 · Convex edit capture + edits table

- **Identifier:** MAE-71
- **Status:** Backlog
- **Assignee:** ajmal@maestrogtm.com
- **Team:** MAE
- **Updated:** 2026-05-21
- **URL:** https://linear.app/modern-agency-sales/issue/MAE-71/71-convex-edit-capture-edits-table

**Why.** Every time a human edits generated content, that's a *signal* — it tells us where the generator fell short. Edit Learning is the loop that captures it. Step one: capture every edit with a before/after diff. (This was a key concept in the v1 we shipped.)

**What.** The `edits` Neon table; diff computation; the `edit.log` command, wired into the Spec 1 node-write path so it fires on every `human_edited` transition (cockpit or CLI).

**Read first.** [Team Onboarding — "Self-improving loops"](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/team-onboarding.md>) · [Spec 7 — Edit Learning](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/specs/edit-learning.md>)

**Done when.** The `edits` table + migration + RLS policy are checked in, `edit.log` is wired into the Spec 1 node-write path so editing a node (cockpit or CLI) inserts an `edits` row with a correct unified diff and sets `human_edited: true`, and a cross-tenant `edits` query is RLS-refused.
