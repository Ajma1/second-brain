---
type: issue
source: linear
date: 2026-05-30
url: "https://linear.app/modern-agency-sales/issue/MAE-87/gate-may-30-factory-fit-gono-go"
title: "GATE · May 30 — factory-fit go/no-go"
identifier: "MAE-87"
status: "Backlog"
assignee: "malaika@modernagencysales.com"
team: "MAE"
---

# GATE · May 30 — factory-fit go/no-go

- **Identifier:** MAE-87
- **Status:** Backlog
- **Assignee:** malaika@modernagencysales.com
- **Team:** MAE
- **Updated:** 2026-05-30
- **URL:** https://linear.app/modern-agency-sales/issue/MAE-87/gate-may-30-factory-fit-gono-go

**Why.** The entire 6 Jul schedule rides on one assumption: the factory can build this product. The factory is *proven* — but on a self-contained iPhone app. This product is a multi-tenant platform with a novel primitive (the grounded node), multiple deployed services, and third-party integrations — a different shape. That assumption must be tested early, while there's still room to react, not discovered failing in late June.

**What.** On **May 30**, the project owner runs a hard go/no-go. Inputs: the `0c` factory-fit spike result ([MAE-3](https://linear.app/modern-agency-sales/issue/MAE-3/0c-run-the-existing-factory-on-a-gtm-toy-spec)) and the state of Stage 0's decomposition. The gate question — **can the factory cleanly build something of this product's shape?** Possible outcomes: GO (continue to Stage 1 as planned); or NO-GO → re-scope, change the build mechanism, or move the date. Record the decision and reasoning in the Decision Log either way.

**Read first.** [Staged project — May 30 hard go/no-go gate](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/staged-linear-project.md>) · [Spec 0 — Modular Software Factory](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/specs/modular-software-factory.md>)

**Done when.** On May 30 a GO or NO-GO decision is made by the project owner, grounded in the `0c` spike result and Stage 0 progress, and the decision plus its reasoning is appended to the Decision Log. A NO-GO additionally produces a concrete re-plan (re-scope / new build mechanism / new date).
