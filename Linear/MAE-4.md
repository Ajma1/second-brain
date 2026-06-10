---
type: issue
source: linear
date: 2026-05-21
url: "https://linear.app/modern-agency-sales/issue/MAE-4/0d-prototype-the-grounded-node-primitive"
title: "0d · Prototype the grounded-node primitive"
identifier: "MAE-4"
status: "Done"
assignee: "ajmal@maestrogtm.com"
team: "MAE"
---

# 0d · Prototype the grounded-node primitive

- **Identifier:** MAE-4
- **Status:** Done
- **Assignee:** ajmal@maestrogtm.com
- **Team:** MAE
- **Updated:** 2026-05-21
- **URL:** https://linear.app/modern-agency-sales/issue/MAE-4/0d-prototype-the-grounded-node-primitive

**Why.** The grounded-node primitive is *the* core idea of the product (see Onboarding) — and every spec from 1 to 7 builds on it. If the design is subtly wrong, we find out cheaply *now*, by hand, not after Stage 1 has built on it.

**What.** Hand-build a tiny 3-layer graph (artifact → claim → object), the node schema, the grounding validator (does every citation resolve? does the quote match?), and the `trace` walk. Feel whether the primitive is right. Throwaway — feeds Spec 1.

**Read first.** [Team Onboarding — "The grounded node"](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/team-onboarding.md>) · [Design doc — The grounded-node primitive](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/design-doc.md>) · [Spec 1 — Client Tenant Foundation](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/specs/client-tenant-foundation.md>)

**Done when.** A hand-built 3-layer graph (artifact → claim → object) exists with a working schema, grounding validator, and `trace` walk; the question "does the primitive feel right?" is answered with concrete observations about what to change; and that verdict is folded into Spec 1 as a Decision Log entry.
