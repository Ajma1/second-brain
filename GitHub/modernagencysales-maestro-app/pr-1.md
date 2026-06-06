---
type: pr
source: github
date: 2026-05-24
url: https://github.com/modernagencysales/maestro-app/pull/1
title: "factory: Wave 4 — Fabro plan tree for the maestro-app build"
repo: modernagencysales/maestro-app
number: 1
state: open
author: kimprobably
---

# factory: Wave 4 — Fabro plan tree for the maestro-app build

PR #1 in modernagencysales/maestro-app — open — by kimprobably — [https://github.com/modernagencysales/maestro-app/pull/1](https://github.com/modernagencysales/maestro-app/pull/1)

## Summary

77-file Fabro-runnable plan tree wiring the 16 reusable Convex factory sub-workflows (shipped in \`kimprobably/maestro-os#22\`) to build maestro-app end-to-end.

## Structure

\`\`\`
.fabro/workflows/
├── maestro-app-build/    parent orchestrator (chains 4 phases as child workflows)
├── foundation/           13 tables + 4 utilities (16 plan files)
├── core/                 4 wrappers + 7 prompts + 10 actions + intake + workflow orchestration (23 plan files)
├── interface/            6 components + 9 routes + marketing-site update (16 plan files)
└── production/           cron + kill switch + deploy + bootstrap + prod E2E (8 plan files)

evals/factory/convex/fixtures/tim-keen-intake.json   # pipeline eval fixture
RESUME.md                                            # 395-line operator briefing
\`\`\`

## Validation

All 5 \`workflow.fabro\` graphs pass \`./bin/maestro verify dot-syntax\`:
- maestro-app-build ✓
- foundation ✓
- core ✓
- interface ✓
- production ✓

## How to trigger

See \`RESUME.md\`. TL;DR: install Fabro CLI, set Convex + Anthropic + Apify + Resend + Turnstile + OpenRouter env vars, then \`fabro run .fabro/workflows/maestro-app-build/workflow.toml\`. Foundation + core + interface phases are autonomous; production phase has a \`shape=hexagon\` human-deploy-gate that requires operator CONFIRM.

## How produced

4 parallel general-purpose subagents owned one phase each. I wrote the parent + 2 sample sub-plans first to lock the pattern, then dispatched.

## Companion PRs
- \`kimprobably/maestro-os#22\` — the 16 reusable sub-workflows the plan tree invokes
- \`modernagencysales/maestro-agent-planning#4\` — mirrored infrastructure index

## Test plan
- [ ] Read RESUME.md
- [ ] Verify env vars present on Convex prod deployment
- [ ] Trigger \`fabro run\` and monitor the foundation phase first (lowest blast radius)
- [ ] Cost projection from foundation's eval rollup informs ceiling for the human-deploy-gate

🤖 Generated with [Claude Code](https://claude.com/claude-code)

<!-- This is an auto-generated comment: release notes by coderabbit.ai -->
## Summary by CodeRabbit

* **New Features**
  * End-to-end blueprint pipeline (scrape → enrich → generate posts) with 60-day post schedule and “blueprint ready” magic-link email.
  * Public intake form with LinkedIn URL validation and Turnstile CAPTCHA.
  * Admin UI for prospect management (list, detail, retry/regenerate).
  * New UI components: Blueprint progress strip, Scores, Lead Magnets, Profile Rewrite, Posts list, and loading Skeletons.
  * AI-driven enrichments for company research, knowledge base, authority scores, lead magnets, profile optimization, and strategy.

* **Chores**
  * Added tooling, evals, linters, and workflow scaffolding to support reliable CI and reviews.

<!-- review_stack_entry_start -->

[![Review Change Stack](https://storage.googleapis.com/coderabbit_public_assets/review-stack-in-coderabbit-ui.svg)](https://app.coderabbit.ai/change-stack/modernagencysales/maestro-app/pull/1?utm_source=github_walkthrough&utm_medium=github&utm_campaign=change_stack)

<!-- review_stack_entry_end -->
<!-- end of auto-generated comment: release notes by coderabbit.ai -->
