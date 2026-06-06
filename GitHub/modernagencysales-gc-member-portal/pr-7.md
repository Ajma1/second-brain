---
type: pr
source: github
date: 2026-03-11
url: https://github.com/modernagencysales/gc-member-portal/pull/7
title: "feat: gate profile rewrite behind admin approval + deep links (MOD-159)"
repo: modernagencysales/gc-member-portal
number: 7
state: closed
author: aeonflusk
---

# feat: gate profile rewrite behind admin approval + deep links (MOD-159)

PR #7 in modernagencysales/gc-member-portal — closed — by aeonflusk — [https://github.com/modernagencysales/gc-member-portal/pull/7](https://github.com/modernagencysales/gc-member-portal/pull/7)

## Summary\n\n- **Gate ProfileRewriteCard** behind deliverable status — profile rewrite content only shows on Dashboard when the deliverable is in `review`, `approved`, or `completed` status (not before admin QA)\n- **Inline preview + modal** in DeliverableCard for profile_rewrite deliverables — shows first headline + truncated about section with \"View Full Rewrite\" button that opens a full modal\n- **Deep link support** via `?tab=deliverables&deliverable=<id>` query params — auto-switches to Deliverables tab and auto-expands the targeted card\n- **Add `automation_type`** to `DfyDeliverable` type and Supabase SELECT query\n\n### Companion PR\n- **gtm-system PR #10** — Updates Slack notification URL to deep link directly to the deliverable\n\n## Files Changed\n- `services/dfy-service.ts` — Added `automation_type` to interface and query\n- `components/client-portal/ClientDashboard.tsx` — Gated ProfileRewriteCard, accepts deliverables prop\n- `components/client-portal/ClientPortalPage.tsx` — Deep link query params, passes deliverables to dashboard\n- `components/client-portal/ProfileRewriteModal.tsx` — New modal component\n- `components/client-portal/DeliverableCard.tsx` — Inline preview, modal, expanded prop\n\n## Test Plan\n- [ ] Profile rewrite NOT visible on Dashboard when deliverable is `pending` or `in_progress`\n- [ ] Profile rewrite IS visible on Dashboard when deliverable is `review`/`approved`/`completed`\n- [ ] DeliverableCard shows inline preview for profile_rewrite in review status\n- [ ] \"View Full Rewrite\" button opens modal with full content\n- [ ] URL `?tab=deliverables&deliverable=<id>` navigates to correct tab and expands card\n- [ ] Slack notification deep link works end-to-end (requires gtm-system PR #10)\n\nCloses MOD-159
