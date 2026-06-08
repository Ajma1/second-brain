---
type: issue
source: linear
date: 2026-05-21
url: "https://linear.app/modern-agency-sales/issue/MAE-69/67-tiptap-ai-toolkit-editing"
title: "6.7 · Tiptap + AI-Toolkit editing"
identifier: "MAE-69"
status: "Backlog"
assignee: "ajmal@maestrogtm.com"
team: "MAE"
---

# 6.7 · Tiptap + AI-Toolkit editing

- **Identifier:** MAE-69
- **Status:** Backlog
- **Assignee:** ajmal@maestrogtm.com
- **Team:** MAE
- **Updated:** 2026-05-21
- **URL:** https://linear.app/modern-agency-sales/issue/MAE-69/67-tiptap-ai-toolkit-editing

**Why.** Operators need to edit content — and the AI Toolkit makes that agent-assisted (rewrite, tighten, adjust tone). But editing must not become a back door around grounding: so the toolkit is for *local tweaks* (not wholesale generation — that stays `campaign.generate`), and **on save the grounding validator re-runs** — an agent-introduced claim that cites nothing blocks the save.

**What.** The Tiptap editor + the AI Toolkit (agent-assisted, track-changes the operator accepts); grounding re-validation on save.

**Read first.** [Team Onboarding](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/team-onboarding.md>) · [Spec 6 — Cockpit](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/specs/cockpit.md>)

**Done when.** The Tiptap editor + AI Toolkit produce agent-assisted track-changes the operator accepts, an edit round-trips to the Daytona volume preserving `human_edited`, grounding re-runs on save so an ungrounded agent-introduced unit blocks the save, and a second concurrent save gets a 409.
