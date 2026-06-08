---
type: issue
source: linear
date: 2026-05-21
url: "https://linear.app/modern-agency-sales/issue/MAE-88/49-thank-you-page-object-conversion-focused-next-step-generation"
title: "4.9 · Thank-you page object — conversion-focused next-step generation"
identifier: "MAE-88"
status: "Backlog"
assignee: "ajmal@maestrogtm.com"
team: "MAE"
---

# 4.9 · Thank-you page object — conversion-focused next-step generation

- **Identifier:** MAE-88
- **Status:** Backlog
- **Assignee:** ajmal@maestrogtm.com
- **Team:** MAE
- **Updated:** 2026-05-21
- **URL:** https://linear.app/modern-agency-sales/issue/MAE-88/49-thank-you-page-object-conversion-focused-next-step-generation

**Why.** The post-opt-in moment is the **highest-intent point in the whole funnel** — the prospect just said yes. The campaign slice previously ended at "lead magnet delivered + nurture sequence," with nothing converting that fresh opt-in into the next step (book a call, watch a VSL, a tripwire offer). The thank-you page is what turns a captured opt-in into an actual *funnel* rather than a dead end. It is a conversion asset, not a courtesy page — it must be generated to convert.

**What.** Add the `thankyou_page` object subtype (id prefix `ty-`) to the campaign generator: the `next_step` CTA block (`cta_kind`: book_call | watch | offer | follow; `cta_label`; `cta_url`), `magnet_access` (email vs inline), and conversion-focused generation. The campaign-plan step deliberately designs the next step for the offer; the generation prompt grounds the page's framing in brain claims. `thankyou_page` is a **required object** (grounding hard-fails if it loses all units). `campaign.generate` gains a `nextStep` input.

**Read first.** [Team Onboarding](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/team-onboarding.md>) · [Spec 4 — Campaign Generation](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/specs/campaign-generate.md>) (Objects → `thankyou_page`)

**Done when.** `campaign.generate` produces a `thankyou_page` object with a `next_step` block matching the `nextStep` input (or a campaign-plan-proposed CTA); its copy is grounded in brain claims (`<!-- grounds: cl-… -->` units) and coherent with the offer; the campaign manifest cites it; `trace.node` walks it to claims; and `evals/campaign-generate-quality.yaml` grades the next-step CTA for conversion focus, not just politeness.
