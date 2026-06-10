---
type: pr
source: github
date: 2026-06-10
url: https://github.com/modernagencysales/maestro-agent-planning/pull/3
title: "[codex] Add Convex workflow learning and anti-ai-slop feedback lints"
repo: modernagencysales/maestro-agent-planning
number: 3
state: closed
author: kimprobably
---

# [codex] Add Convex workflow learning and anti-ai-slop feedback lints

PR #3 in modernagencysales/maestro-agent-planning — closed — by kimprobably — [https://github.com/modernagencysales/maestro-agent-planning/pull/3](https://github.com/modernagencysales/maestro-agent-planning/pull/3)

## Summary
- Adds the Convex workflow-learning prototype for ingesting Codex build chains and surfacing redacted training patterns.
- Publishes one workflow chain across adjacent multi-session Codex work, including interleaved workflows, instead of one chain per session.
- Fails closed when the workflow owner allowlist env is unset, deletes stale redaction findings/artifacts on re-ingest, and stores sanitized home-relative display paths.
- Adds anti-ai-slop phrase signals to the Joni editor as sentence-level lints while preventing broad/common one-word signals from becoming hard display blockers.

## Validation
- `npx vitest run src/codexWorkflowLearning.test.ts convex/codexWorkflowLearning.test.ts` in `prototypes/convex-runtime-bakeoff` (18 tests)
- `npm test -- --run` in `prototypes/convex-runtime-bakeoff` (21 files / 78 tests)
- `npm run codex:workflow:test` in `prototypes/convex-runtime-bakeoff` (4 node tests)
- `npm run typecheck` in `prototypes/convex-runtime-bakeoff`
- `npm run build` in `prototypes/convex-runtime-bakeoff`
- `npm test -- --run` in `prototypes/joni-convex-blocknote-editor-demo` (18 files / 104 tests)
- `npm run typecheck` in `prototypes/joni-convex-blocknote-editor-demo`
- `npm run build` in `prototypes/joni-convex-blocknote-editor-demo`

Note: the Joni demo Vite build still reports the existing large-chunk warning.

<!-- This is an auto-generated comment: release notes by coderabbit.ai -->
## Summary by CodeRabbit

* **New Features**
  * Codex Workflow Learning UI: chain list, search/filters, detail view, metrics and dashboard.
  * Local ingestion CLI: fixture/sqlite modes, dry-run, watch, identity options, and robust secret redaction.
  * Automated curation: scheduled background scoring and publication of redaction-safe chains and lessons.
  * Prompt-lesson extraction and publication gating based on redaction risk.
  * Editor: anti–AI-slop detection and stronger profile guidance.

* **Documentation**
  * Expanded prototype README with setup, ingestion, dashboard, and verification walkthroughs.

* **Tests**
  * Broad new and expanded tests covering ingestion, redaction, scoring, grouping, dashboard, CLI, and end-to-end flows.

<!-- review_stack_entry_start -->

[![Review Change Stack](https://storage.googleapis.com/coderabbit_public_assets/review-stack-in-coderabbit-ui.svg)](https://app.coderabbit.ai/change-stack/modernagencysales/maestro-agent-planning/pull/3?utm_source=github_walkthrough&utm_medium=github&utm_campaign=change_stack)

<!-- review_stack_entry_end -->
<!-- end of auto-generated comment: release notes by coderabbit.ai -->
