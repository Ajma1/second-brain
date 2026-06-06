---
type: pr
source: github
date: 2026-05-22
url: https://github.com/modernagencysales/maestro-factory/pull/7
title: "fix(validate): emit schema-valid JSON from validate.sh and check in factory-validate.sh"
repo: modernagencysales/maestro-factory
number: 7
state: closed
author: kimprobably
---

# fix(validate): emit schema-valid JSON from validate.sh and check in factory-validate.sh

PR #7 in modernagencysales/maestro-factory — closed — by kimprobably — [https://github.com/modernagencysales/maestro-factory/pull/7](https://github.com/modernagencysales/maestro-factory/pull/7)

## Summary

Fixes two latent schema contract gaps in factory validation scripts:

### `scripts/validate.sh`
- Restructures JSON output to match `validation.schema.json`: `{ decision, checks: { lint, typecheck, unit_tests, build }, failed_commands, duration_seconds }`. Previous output had flat top-level keys (`lint`, `typecheck`, etc.) instead of a nested `checks` object and was missing the `decision` field — making every build run emit schema-invalid JSON.
- Adds a factory-self guard: when `pnpm` is absent or no `pnpm` project exists at repo root, emits all-skipped/pass JSON and exits 0, preventing false validation failures in the factory's own self-improve runs.

### `scripts/factory-validate.sh`
- Adds schema contract check for `docs/factory/validation.json` when it exists after a build run, verifying it matches `validation.schema.json` shape (`decision`, `checks`, `failed_commands`, `duration_seconds`).

## Validation
```
bash scripts/factory-validate.sh --quick  # passes
bash scripts/validate.sh                   # emits schema-valid JSON (all-skipped in factory-self context)
```

## Files changed
- `scripts/validate.sh` — schema-aligned JSON output + factory-self guard
- `scripts/factory-validate.sh` — validation.json schema contract check

Part of self-improvement run `01KS7R1S22WPN83MVZX7WETFX9`.

<!-- This is an auto-generated comment: release notes by coderabbit.ai -->

## Summary by CodeRabbit

* **New Features**
  * Validation now produces structured output in a standardized format
  * Factory context is automatically detected and skips validation checks when appropriate
  * Validation results are consistently formatted and validated against a schema

* **Documentation**
  * Added validation result documentation

<!-- review_stack_entry_start -->

[![Review Change Stack](https://storage.googleapis.com/coderabbit_public_assets/review-stack-in-coderabbit-ui.svg)](https://app.coderabbit.ai/change-stack/modernagencysales/maestro-factory/pull/7?utm_source=github_walkthrough&utm_medium=github&utm_campaign=change_stack)

<!-- review_stack_entry_end -->

<!-- end of auto-generated comment: release notes by coderabbit.ai -->
