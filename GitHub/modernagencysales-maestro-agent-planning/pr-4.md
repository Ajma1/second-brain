---
type: pr
source: github
date: 2026-06-10
url: https://github.com/modernagencysales/maestro-agent-planning/pull/4
title: "docs: mirror factory infrastructure index for agent planning"
repo: modernagencysales/maestro-agent-planning
number: 4
state: closed
author: kimprobably
---

# docs: mirror factory infrastructure index for agent planning

PR #4 in modernagencysales/maestro-agent-planning — closed — by kimprobably — [https://github.com/modernagencysales/maestro-agent-planning/pull/4](https://github.com/modernagencysales/maestro-agent-planning/pull/4)

## Summary
Mirrors two infra-discovery docs into this repo so agents starting plans here see them before they reach maestro-os:
- \`docs/factory/INFRASTRUCTURE-INDEX.md\` — master map (10 layers)
- \`docs/factory/convex/INDEX.md\` — Convex-specific subset

Both files carry a header noting the source-of-truth path and the rule: update at source, re-copy here.

## Why
Companion to \`kimprobably/maestro-os#22\`. That PR creates the canonical docs at \`knowledge/INFRASTRUCTURE-INDEX.md\` and \`workflows/factory/convex/INDEX.md\` in maestro-os. Since planning happens here, the index needs to surface here too.

## Test plan
- [ ] Docs-only — no code/config/workflow changes
- [ ] Both files render as expected on GitHub
- [ ] Re-mirror procedure: \`cp\` from maestro-os, re-add the header note

🤖 Generated with [Claude Code](https://claude.com/claude-code)

<!-- This is an auto-generated comment: release notes by coderabbit.ai -->

## Summary by CodeRabbit

* **Documentation**
  * Added comprehensive Infrastructure Index documentation covering task mappings, organizational layers, conventions, and promotion guidelines.
  * Added Convex factory documentation index detailing sub-workflow structure, composition rules, coding standards, and promotion criteria.

<!-- review_stack_entry_start -->

[![Review Change Stack](https://storage.googleapis.com/coderabbit_public_assets/review-stack-in-coderabbit-ui.svg)](https://app.coderabbit.ai/change-stack/modernagencysales/maestro-agent-planning/pull/4?utm_source=github_walkthrough&utm_medium=github&utm_campaign=change_stack)

<!-- review_stack_entry_end -->

<!-- end of auto-generated comment: release notes by coderabbit.ai -->
