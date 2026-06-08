---
type: project
source: github
repo: modernagencysales/mas-platform
kind: issue
number: 1282
title: "DB scope audit: ? non-exempt scoped table(s) with NULL workspace_id"
state: OPEN
author: github-actions[bot]
created: 2026-06-07T11:15:52Z
updated: 2026-06-07T11:15:52Z
url: https://github.com/modernagencysales/mas-platform/issues/1282
tags: [github]
---

# modernagencysales/mas-platform #1282 — DB scope audit: ? non-exempt scoped table(s) with NULL workspace_id

Nightly DB scope audit detected **? non-exempt** scoped table(s) with NULL `workspace_id` rows.

**Run:** https://github.com/modernagencysales/mas-platform/actions/runs/27090949619

**Summary** (output of `public.assert_workspace_id_backfill()`):

```json
(no summary captured)
```

**Action required:**
1. Identify the writer inserting NULL `workspace_id` for each violating table.
2. Either fix the writer (preferred) or open a one-shot backfill migration.
3. If the table is intentionally allowed to carry NULL during a migration window, add it to `public._workspace_scope_audit_exemptions` in the same PR.

See [docs/architecture/scoped-tables.md](../tree/main/docs/architecture/scoped-tables.md) for the full runbook.

## Links
- GitHub: https://github.com/modernagencysales/mas-platform/issues/1282
