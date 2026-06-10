---
type: pr
source: github
date: 2026-06-10
url: https://github.com/modernagencysales/maestro-agent-planning/pull/2
title: "[codex] Build transcript extraction engine"
repo: modernagencysales/maestro-agent-planning
number: 2
state: open
author: kimprobably
---

# [codex] Build transcript extraction engine

PR #2 in modernagencysales/maestro-agent-planning — open — by kimprobably — [https://github.com/modernagencysales/maestro-agent-planning/pull/2](https://github.com/modernagencysales/maestro-agent-planning/pull/2)

## Summary
- Adds a modular transcript extraction engine for parsing, speaker resolution, atomic observations, topic resolution, routing, deterministic evals, and review queues.
- Adds Convex durable state and Convex Workflow orchestration for extraction runs, review queues, topic clusters, and eval results.
- Adds workflow mode selection: deterministic remains the default, while `openrouter_model` calls the internal OpenRouter action and persists only validated atomic observations through the existing routing/review path.
- Adds grounded topic synthesis support for promoted transcript claims, support packs, derived summaries, and topic positions.
- Adds the review/promotion contract: append-only review decisions, unified review queue projection, candidate review ledger, promotion receipts, speaker identity mappings, operational state enrichment, and topic cluster review decisions.
- Adds the transcript operator report CLI and golden redacted-call report. The report previews review queues, Brain claim/citation IDs, memory/operational promotion targets, topic rollups, and speaker mapping prompts while confirming Brain truth lives in Convex `groundedNodes`, `groundedNodeCitations`, and `groundedNodeEdges`.
- Updates Fabro verification workflows and adds transcript-specific, review-contract, and operator-report coverage.

## Validation
- `npx vitest run src/transcriptReviewReport.test.ts src/reviewContract.test.ts convex/transcriptReview.test.ts convex/transcriptPromotion.test.ts convex/topicIntelligence.test.ts convex/transcriptExtraction.test.ts` — 6 files, 27 tests passed
- `npm test` — 35 files, 136 tests passed
- `npm run typecheck`
- `npm run transcript:review-report -- fixtures/transcripts/redacted-maestro-extraction-call.txt --tenant maestro --client maestro-internal --transcript redacted-maestro-extraction-call --out ../../docs/operator/reports/2026-05-24-redacted-maestro-transcript-review-report.md`
- `fabro validate workflows/factory/transcript-extraction-engine-v1.fabro`
- `fabro validate workflows/factory/convex-runtime-bakeoff-verify.fabro`
- `fabro validate workflows/factory/grounded-topic-synthesis-v1.fabro`
- Railway Fabro `ConvexRuntimeBakeoffVerify`: `01KSDVFKH3H2YVSN8KD544TDV0` succeeded
- Railway Fabro `ConvexRuntimeBakeoffVerify`: `01KSDBHD4QBRT6FJ3BQNDR7BNA` succeeded
- Railway Fabro `GroundedTopicSynthesisV1`: `01KSBZ5QQBQX564SC2QY03SVNP` succeeded
- Daytona cleanup after latest verification: 0 sandboxes, 0 volumes

## Notes
- PR remains draft.
- Brain is already Convex-first in this prototype; no migration was needed.
- Candidate status is a read model; latest approval in `reviewDecisions` is the promotion authority.
- Brain receipts carry citation IDs and source hash; memory and operational receipts carry candidate/source hashes without claiming citation authority.
- `npm run convex:codegen` regenerates bindings but its own Convex typecheck still trips over existing `node:crypto` imports in `src/topicIntelligence.ts` and `src/transcriptLanding.ts`; the repo-level `npm run typecheck` passes with the project `tsconfig`.
