---
type: pr
source: github
date: 2026-06-16
url: https://github.com/modernagencysales/sldl-unified/pull/1
title: "feat: port sldl to Rust and Convex backend"
repo: modernagencysales/sldl-unified
number: 1
state: closed
author: kimprobably
---

# feat: port sldl to Rust and Convex backend

PR #1 in modernagencysales/sldl-unified — closed — by kimprobably — [https://github.com/modernagencysales/sldl-unified/pull/1](https://github.com/modernagencysales/sldl-unified/pull/1)

## Summary
- Replaces the runtime Python/Airtable implementation with a Rust workspace (`sldl-core`, `sldl-cli`, `sldl-menubar`) backed by Convex.
- Adds Convex schema/functions for tracks, run history, settings, HTTP endpoints, generated guidelines, and fixture tests.
- Adds one-time Airtable CSV/JSON export importer into Convex; Airtable is migration-only and not a runtime dependency.
- Ports source watchers, downloader, yt-dlp fallback, Music.app importer, orchestrator, schedule/menu snapshot logic, installer, and launchd plist templating.
- Archives the old Python app under `archive/python-app/` after parity checks.

## Verification
- `npm run convex:check`
- `npm run test:convex-fixture`
- `npm run test:airtable-import`
- `npx tsc --noEmit`
- `./scripts/verify-rust.sh`
- `SLDL_PROJECT_DIR="$PWD" cargo run -p sldl-cli -- config print`
- `SLDL_PROJECT_DIR="$PWD" cargo run -p sldl-menubar -- --status`
- `plutil -lint com.sldl.menubar.plist`

## Notes
- Visual Aqua menu bar click smoke and real deployed Convex credential smoke still require an interactive GUI session / deployment credentials.
