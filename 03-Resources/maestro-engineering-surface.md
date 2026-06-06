---
type: reference
source: research
date: 2026-06-06
url: https://github.com/orgs/modernagencysales/repositories
title: "Maestro Engineering / Product Surface (inferred from GitHub org)"
tags: [maestro, modernagencysales, github, engineering, product-surface, gtm, fabro, convex, inference]
---

# Maestro Engineering & Product Surface

**Method & confidence note.** This note infers what the Maestro team is building from the **`modernagencysales` GitHub org** (29 repositories enumerated via the GitHub API on 2026-06-06), repo descriptions, and three READMEs I read directly (`maestro-v2`, `maestro-v2-3`, `maestro-factory`). Items grounded in a README or repo description are **High confidence**; items inferred only from a repo name (no description, or archived/private with no readable contents) are flagged **Lower confidence**. Where something is unknown, it is marked "Not found." Most repos are **private**; only 6 are public (`claude-code-training`, `magnetlab`, `maestrogtm-quickstart`, `maestro-os`, `gc-member-portal`, `maestro-funnel`).

Source for repo list: https://github.com/orgs/modernagencysales/repositories

## TL;DR — what the team is actually building

A **LinkedIn-led GTM operating system** ("turn your expertise into attributed pipeline") plus the **agent-driven software factory that builds it**. The org splits cleanly into:

1. The **Maestro product** itself (multiple parallel rebuilds: V1 `mas-platform`, V2 `maestro-v2` / `maestro-v2-MVP2` / `maestro-v2-3`, plus app/site/desktop/design satellites).
2. A **"software factory"** tooling layer — `fabro-maestro` (Rust engine), `maestro-factory` (Fabro workflows), `maestro-os`, `maestro-agent-planning` — that auto-generates the product via orchestrated AI agents.
3. An **earlier "Dynamik" platform** (`dynamik-*`) for agency owners, apparently a predecessor / sibler product line.
4. **Lead-gen, funnel, content, and member-portal** supporting repos (`linkedin-leadmagnet-*`, `maestro-funnel`, `magnetlab`, `gc-member-portal`, `maestrogtm-quickstart`).
5. **Ops/infra glue** (`linear-slack-bridge`, `gtm-docs`, `playbooks`, `gas-city-vault`).

The single best primary source for the product surface is the `maestro-v2` README (see "Product surface" below).

## The Maestro product (core)

High confidence. The product is described in `maestro-v2/README.md` as:

> "Maestro V2 is a LinkedIn-led GTM operating system that turns your expertise into attributed pipeline."

Product promise quoted in-repo: *"It gave me an entire content calendar and content that sounds like me."*

The functional MVP loop documented in `maestro-v2` (listed as working/tested) defines the **product surface**:

1. **Voice DNA onboarding** — extract a founder's authentic voice patterns from inputs.
2. **Brain memory (Global + Local)** — a knowledge graph storing ICP, offer, proof, and voice rules. Described as "Context OS-inspired," with a `SENSE → ORIENT → ACT → DEPOSIT` loop and node lifecycle `emergent → validated → canonical → deprecated`.
3. **Content calendar** — generate a 4-week LinkedIn calendar with on-voice post drafts.
4. **Post publishing** — approve → schedule → publish (approval-gated; via a mock/provider abstraction).
5. **Lead magnets** — interactive quizzes, calculators, PDFs, mini-courses created from published posts.
6. **Lead capture** — quiz submission creates a lead + attributed opportunity.
7. **Follow-up email** — approval-gated warm outreach / nurture sequences.
8. **Attribution analytics** — full content → lead magnet → lead → pipeline journey.

Source: https://github.com/modernagencysales/maestro-v2 (README)

### Product repos and their apparent roles

| Repo | Visibility | Stated purpose (description/README) | Role |
|---|---|---|---|
| `mas-platform` | private | (no description) — referenced by `maestro-v2` as the **V1** read-only reference for "proven monorepo patterns, Supabase/RLS, integration client structure, MCP architecture, service/repository boundaries." | **Maestro V1** (the original platform). High confidence it is V1 from the cross-reference. |
| `maestro-v2` | private | "Maestro V2 — greenfield rebuild, built by maestro-factory." Functional MVP. Stack: Next.js 15, Supabase/Postgres, Claude + Vercel AI SDK, Tailwind, pnpm + Turborepo, Vitest. | **V2 greenfield rebuild.** High confidence. |
| `maestro-v2-MVP2` | private | "Maestro V2 MVP2 built by the Maestro Fabro factory." | V2 iteration MVP2. High confidence. |
| `maestro-v2-3` | private | "This is the Maestro V2 MVP03." README: "Agent-first, **Convex-native** GTM co-pilot for LinkedIn content, Voice DNA, lead magnets, attribution, email, and MCP-native workflows." Stack: Next.js 15, **Convex** backend (Agent/RAG/Workflow/Workpool components), **Clerk** auth, Anthropic, OpenAI, LinkedIn, ConnectSafely, **Resend**. | V2 iteration MVP03. High confidence. Note the stack shift Supabase→Convex + Clerk vs. `maestro-v2`. |
| `maestro-app` | private | "Maestro App — **Convex + React + TanStack**. Blueprint pipeline rebuild, built via Fabro factory." (README not found / not readable.) | The app at **app.maestrogtm.com** (inference, Lower–Medium confidence — no README readable). Convex/React/TanStack stack. |
| `maestro-site` | private | "Maestro marketing site — your agent team for inbound leads." | **maestrogtm.com** marketing site. High confidence. Note tagline variant: "your agent team for inbound leads." |
| `maestro-design` | private | "Maestro V2 design system, prototypes, brand kit, and research — for agent consumption." | Design system / brand kit (agent-consumable). High confidence. |
| `maestro-desktop` | private | "Maestro Desktop — invisible meeting capture for content generation via **recall.ai**." | Desktop app: meeting capture → content. High confidence. |

**Naming observation (Medium confidence):** there are at least **four parallel Maestro codebases** (`mas-platform` V1, `maestro-v2`, `maestro-v2-MVP2`, `maestro-v2-3`) plus `maestro-app`. The READMEs show a **rebuild-in-flight** with an unsettled backend choice — `maestro-v2` uses Supabase, while `maestro-v2-3` and `maestro-app` use **Convex**. This indicates active platform re-architecture toward Convex + agent-first/MCP-native design as of May–June 2026.

## The software factory (how they build it)

High confidence. A distinctive theme: the team builds the product using an **AI-agent "software factory."**

| Repo | Visibility | Purpose | Role |
|---|---|---|---|
| `fabro-maestro` | public (Rust, MIT) | "⚒️ The open source dark software factory for expert engineers." | **Fabro** — the core factory engine (Rust). High confidence. |
| `maestro-factory` | private | "Maestro Software Factory — Fabro-orchestrated workflows." README: four workflows `spec → build → deploy` + `retro`; build stage runs **6 parallel reviewers** (QA, Security, Architecture, Migration, Performance, Observability). Targets `maestro-v2`. Fabro server on Railway. | Fabro workflow definitions that generate Maestro. High confidence. |
| `maestro-os` | public | "Maestro OS workflow harness and Fabro automation experiments." | Workflow harness / automation experiments. High confidence. |
| `maestro-agent-planning` | private | "Maestro agent planning workspace for Miles: working specs, plans, Linear/Fabro handoffs, and agent-owned scratch docs." | Agent planning/handoff workspace. High confidence. ("Miles" appears to be a named agent — Lower confidence on identity.) |
| `ascii-art` | private | "A reference project for a software factory to build." | Factory test/reference target. High confidence. |
| `claude-code-training` | public | "Interactive training platform for learning Claude Code — build real projects with AI assistance." | Training tool (internal/marketing). High confidence. |

Factory stack signals (from `maestro-factory` README): LLM providers Anthropic/OpenAI/Gemini/OpenRouter; MCPs for GitHub, Linear, Supabase, Vercel, Railway, Trigger; sandbox `ubuntu:24.04 + Node20`; CI runs `fabro validate` on every push.

Source: https://github.com/modernagencysales/maestro-factory (README)

## Dynamik platform (predecessor / sibling line)

High confidence on roles from descriptions; **relationship to Maestro is inference (Medium)** — likely an earlier or parallel product for agency owners, created Jan 2026.

| Repo | Purpose |
|---|---|
| `dynamik-platform` | "Dynamik Platform — **Next.js dashboard for agency owners**." |
| `dynamik-api` | "Dynamik Platform API — **Express/TypeScript backend with Trigger.dev jobs**." |
| `dynamik-template` | "Dynamik Template — **React/Vite frontend deployed per-tenant**." |

The per-tenant template + dashboard + API shape suggests a **multi-tenant agency SaaS**. Whether "Dynamik" was rebranded into Maestro is **Not found.**

## GTM engine / orchestration (earlier internal platform)

Created Jan–Feb 2026; several share an identical timestamp suggesting a single scaffolding event. Inference confidence Medium (descriptions present but terse; contents not read).

| Repo | Purpose |
|---|---|
| `gtm-conductor` | "GTM Conductor — **Multi-channel delivery orchestration**." (delivery/sequencing engine) |
| `gtm-system` | (no description) — Lower confidence; presumably the core GTM platform/system. |
| `gtm-docs` | "GTM Platform documentation site (**Docusaurus**)." |
| `sldl-unified` | (no description) — **Not interpretable from name.** Lower confidence. Possibly "self-liquidating … "/"SLDL" internal acronym. Treat as unknown. |
| `playbooks` | "playbooks" — GTM playbooks content. Lower confidence on specifics. |

## Lead-gen, funnels, content & member experience

| Repo | Visibility | Purpose |
|---|---|---|
| `linkedin-leadmagnet-backend` | private | (no description) — backend for a LinkedIn lead-magnet product. Medium confidence (name-based, but pairs with admin). |
| `linkedin-leadmagnet-admin` | private | (no description) — admin UI for the LinkedIn lead-magnet product. Medium confidence. |
| `magnetlab` | public (archived) | (no description) — likely an earlier **lead-magnet builder** experiment ("magnet" + "lab"). Lower confidence; archived. |
| `maestro-funnel` | public (HTML) | "Marketing funnel for Maestro products — landing pages for the Quickstart, 30-Day LinkedIn Sprint, cohort, plus 3 OTO upsell pages. Plain HTML/CSS/JS, **ThriveCart**-wired. Deployed to maestro-funnel.vercel.app." |
| `maestrogtm-quickstart` | public (PLpgSQL) | "AI coach that walks you through building a real **cold-email machine** — **Supabase + Deepline + AgentMail**, wired by an agent on your machine. `npx @maestrogtm/quickstart`." |
| `gc-member-portal` | public (archived) | "**Growth Collective** Member Portal + Bootcamp LMS — React + TypeScript + **Airtable**." |

These show a commercial motion: a **funnel (ThriveCart), a paid Quickstart CLI, a LinkedIn Sprint/cohort, and a "Growth Collective" community/LMS** ("bootcamp"). High confidence on each repo's stated purpose; their grouping as the go-to-market/monetization layer is Medium-confidence inference.

## Ops / infra / knowledge glue

| Repo | Purpose | Confidence |
|---|---|---|
| `linear-slack-bridge` | (no description) — bridges **Linear ↔ Slack** (consistent with the `modernagencysales2` Slack workspace and Linear MCP usage). | Medium (name-based; default branch `master`). |
| `gtm-docs` | Docusaurus docs site (also listed above). | High |
| `gas-city-vault` | "Gas City Obsidian vault — second brain for the king + polecats." Internal **Obsidian** knowledge vault. | High on what it is; the "king/polecats" naming is internal slang — **Not interpreted.** |
| `playbooks` | GTM playbooks (also listed above). | Lower |

## Inferred tech-stack summary (from READMEs/descriptions)

High confidence, drawn from the three READMEs read:

- **Frontend:** Next.js 15 (App Router), React, TanStack (in `maestro-app`), Tailwind CSS.
- **Backend / data:** **Convex** (newer line: `maestro-v2-3`, `maestro-app`) vs **Supabase/Postgres** (older `maestro-v2`, `maestrogtm-quickstart`). Express + Trigger.dev in `dynamik-api`.
- **Auth:** Clerk (`maestro-v2-3`) / Supabase Auth (`maestro-v2`).
- **AI:** Anthropic Claude + OpenAI (+ Gemini/OpenRouter in the factory), Vercel AI SDK; **MCP-native** tool design throughout.
- **Email/integrations:** Resend, LinkedIn, ConnectSafely (`maestro-v2-3`); recall.ai (`maestro-desktop`); AgentMail/Deepline (`maestrogtm-quickstart`).
- **Build/infra:** pnpm + Turborepo monorepos, Vitest; Railway (Fabro server), Vercel (funnel/sites), GitHub + Linear MCPs; **Fabro (Rust)** as the agent orchestration engine.
- **Commerce/content:** ThriveCart (funnel), Airtable (member portal LMS), Docusaurus (docs), Obsidian (internal vault).

## Open questions / "Not found"

- Exact mapping of which Maestro codebase backs **app.maestrogtm.com** today (`maestro-app` vs `maestro-v2-3` vs `maestro-v2`). Multiple in-flight rebuilds; **Not definitively found.**
- Meaning of `sldl-unified` and the `gtm-system` internals — **Not found.**
- Whether **Dynamik** was rebranded/absorbed into Maestro — **Not found.**
- Identity of named agent "Miles" and internal slang ("king/polecats," "Gas City," "dark software factory") — internal, **not interpreted.**

*All claims above are sourced from the GitHub org repo list (https://github.com/orgs/modernagencysales/repositories) and the READMEs of `maestro-v2`, `maestro-v2-3`, and `maestro-factory`, read 2026-06-06. Repos with no description are explicitly flagged Lower confidence and inferred from name only.*
