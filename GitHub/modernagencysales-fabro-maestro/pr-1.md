---
type: pr
source: github
date: 2026-05-21
url: https://github.com/modernagencysales/fabro-maestro/pull/1
title: "Formalize Railway CLI backend hardening"
repo: modernagencysales/fabro-maestro
number: 1
state: open
author: timkeeeeeen
---

# Formalize Railway CLI backend hardening

PR #1 in modernagencysales/fabro-maestro — open — by timkeeeeeen — [https://github.com/modernagencysales/fabro-maestro/pull/1](https://github.com/modernagencysales/fabro-maestro/pull/1)

## Summary
- formalizes CLI backend execution as an explicit external-sandbox runtime policy while preserving Railway-compatible defaults
- keeps Codex non-git service-local execution behind that policy and adds a CliDefault escape hatch for tests/future runners
- contract-tests Codex auth.json materialization and adds procps to the runtime image for daemon process inspection
- documents when service/container-local CLI execution is acceptable versus Docker/Daytona isolation

## Verification
- cargo test -p fabro-workflow runtime_policy --lib
- cargo test -p fabro-workflow cli_command_for_codex --lib
- cargo test -p fabro-workflow codex_auth_materialization_command_writes_private_auth_file_from_base64_env --lib
- cargo test -p fabro-workflow execute_preserves_failed_outcome_when_unconditional_edge_reaches_exit --lib
- cargo test -p fabro-core --lib
- cargo test -p fabro-workflow handler::llm::cli::tests --lib
- cargo test -p fabro-workflow handler::llm::launch_env::tests --lib
- git diff --check
