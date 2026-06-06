---
type: issue
source: github
date: 2026-05-22
url: https://github.com/modernagencysales/maestro-v2/issues/7
title: "Tighten local-scoped RLS for brain sources and nodes"
repo: modernagencysales/maestro-v2
number: 7
state: open
author: Ajma1
---

# Tighten local-scoped RLS for brain sources and nodes

Issue #7 in modernagencysales/maestro-v2 — open — by Ajma1 — [https://github.com/modernagencysales/maestro-v2/issues/7](https://github.com/modernagencysales/maestro-v2/issues/7)

Follow-up from PR #6: tighten the temporary MVP RLS policies brain_sources_local_scoped and brain_nodes_local_scoped once Supabase Auth app_metadata carries workspace_id. Target policy predicate: workspace_id = (auth.jwt() ->> 'workspace_id')::uuid for local workspace rows, with service-role/admin paths handled explicitly.
