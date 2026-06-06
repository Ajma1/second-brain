---
type: pr
source: github
date: 2026-05-22
url: https://github.com/modernagencysales/maestro-factory/pull/3
title: "feat(factory): strengthen consolidated-review schema and validate retro preflight"
repo: modernagencysales/maestro-factory
number: 3
state: closed
author: kimprobably
---

# feat(factory): strengthen consolidated-review schema and validate retro preflight

PR #3 in modernagencysales/maestro-factory — closed — by kimprobably — [https://github.com/modernagencysales/maestro-factory/pull/3](https://github.com/modernagencysales/maestro-factory/pull/3)

## Summary

Closes three maturity gaps identified in the self-improvement plan.

### 1. `consolidated-review.schema.json` — align with §12.7 contract

Adds severity-bucketed finding lists (`blockers`, `high`, `medium`, `low`, `nits`), `duplicate_findings_removed` and `false_positive_findings_removed` counters, `fix_plan` array, and `"fail"` to the decision enum.

### 2. `scripts/factory-validate.sh` — preflight `maestro-retro.toml`

Full-mode validation now preflights `maestro-retro.toml` so retro workflow breakage is caught in CI.

### 3. `output-contracts.md` — close artifact contract gaps

Build Phase artifact list now includes `change-summary.md`, per-reviewer JSON paths, and `consolidated-findings.{md,json}`.

## Validation

```
bash scripts/factory-validate.sh --quick
# validation_passed_quick ✓
```

## Risk

LOW — schema-only + documentation-only changes. No workflows or scripts changed behavior.

<!-- This is an auto-generated comment: release notes by coderabbit.ai -->

## Summary by CodeRabbit

* **New Features**
  * Findings now organized by severity levels (blockers, high, medium, low, nits)
  * Added metrics tracking duplicate and false positive findings removed
  * New structured fix plan with actionable instructions
  * Expanded decision options in review output

* **Documentation**
  * Updated output contracts documentation with latest artifact specifications

<!-- review_stack_entry_start -->

[![Review Change Stack](https://storage.googleapis.com/coderabbit_public_assets/review-stack-in-coderabbit-ui.svg)](https://app.coderabbit.ai/change-stack/modernagencysales/maestro-factory/pull/3?utm_source=github_walkthrough&utm_medium=github&utm_campaign=change_stack)

<!-- review_stack_entry_end -->

<!-- end of auto-generated comment: release notes by coderabbit.ai -->
