---
type: "reference"
source: "agent"
author: "mcp"
date: "2026-06-10"
updatedAt: "2026-06-10T19:14:24.612Z"
---

# MAE — Factory, Cockpit & Methodology build

---
title: MAE — Factory, Cockpit & Methodology build
status: superseded
archived: 2026-06-10
source: linear-archive (MAE P/factory/numbered)
tags: [roadmap, superseded, factory, cockpit, methodology]
---

# Factory + Cockpit + Methodology build — superseded plan

> SUPERSEDED 2026-06-10. The product spine. Intent kept; issue decomposition archived.
> See [[2026-06 Six-Week Plan — Executive Summary]].

- **P1–P6 · Foundations** — planning repo; Fabro-gated coding repo; architect decisions
  (factory's home; thesys spike & pricing); provision Convex/auth/Daytona/env; upgrade Fabro
  to parent/child-runs.
- **factory.* (0.1–0.9) · The coding factory** — draft-spec → review-spec → architecture →
  work-packages → implement → code-review → simplify → release, each a Fabro workflow + evals;
  the build composition + 3-layer eval scoreboard. Phase-0 spikes: run on a GTM toy spec,
  Tiptap+AI-Toolkit, agent memory (Convex-first / Honcho fallback).
- **Grounding & brain (1.x, 2.x)** — grounded-node library + schema, grounding validator, the
  index, trace.node; claim schema, brain-extract/learn Fabro workflows, dedup/fingerprint,
  topic summaries, brain evals.
- **Research (3.x)** — capture habit, brain-research fanout, research.* commands + evals.
- **Campaigns (4.x, 5.x)** — object + campaign-manifest schemas, campaign-plan + generation
  prompts, thesys render path, visual generation, fingerprint/regeneration; Convex runtime,
  opt-in pages, form capture + lead store, lead-magnet delivery, Resend sending subdomain,
  scheduled sender, webhooks/suppression/unsub, campaign.stats funnel, thank-you pages.
- **Cockpit (6.x)** — apps/cockpit scaffold + magic-link auth, command-core HTTP binding,
  tenant routing, generative shell (C1) + table fallback, generative screens, the trace
  (provenance) view, Tiptap+AI-toolkit editing, deploy.
- **Edit-learning loop (7.x)** — Convex edit capture, edit-learn Fabro + inference prompt, the
  taste/defect router, eval-case review queue + promotion, cockpit surfacing. (The moat.)
