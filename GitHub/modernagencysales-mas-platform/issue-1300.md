---
type: issue
source: github
date: 2026-06-25
url: https://github.com/modernagencysales/mas-platform/issues/1300
title: "DB scope audit: ? non-exempt scoped table(s) with NULL workspace_id"
repo: modernagencysales/mas-platform
number: 1300
state: open
author: github-actions[bot]
---

# DB scope audit: ? non-exempt scoped table(s) with NULL workspace_id

Issue #1300 in modernagencysales/mas-platform — open — by github-actions[bot] — [https://github.com/modernagencysales/mas-platform/issues/1300](https://github.com/modernagencysales/mas-platform/issues/1300)

Nightly DB scope audit detected **? non-exempt** scoped table(s) with NULL `workspace_id` rows.

**Run:** https://github.com/modernagencysales/mas-platform/actions/runs/28167936178

**Summary** (output of `public.assert_workspace_id_backfill()`):

```json
(no summary captured)
```

**Action required:**
1. Identify the writer inserting NULL `workspace_id` for each violating table.
2. Either fix the writer (preferred) or open a one-shot backfill migration.
3. If the table is intentionally allowed to carry NULL during a migration window, add it to `public._workspace_scope_audit_exemptions` in the same PR.

See [docs/architecture/scoped-tables.md](../tree/main/docs/architecture/scoped-tables.md) for the full runbook.
