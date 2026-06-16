---
type: pr
source: github
date: 2026-06-16
url: https://github.com/modernagencysales/sldl-unified/pull/2
title: "feat: add installable release packaging"
repo: modernagencysales/sldl-unified
number: 2
state: closed
author: kimprobably
---

# feat: add installable release packaging

PR #2 in modernagencysales/sldl-unified — closed — by kimprobably — [https://github.com/modernagencysales/sldl-unified/pull/2](https://github.com/modernagencysales/sldl-unified/pull/2)

## Summary
- add prebuilt macOS arm64 archive packaging scripts with install/uninstall flows
- add GitHub Actions release workflow for tag/workflow-dispatch artifacts
- add sldl-cli configure for writing local runtime credentials to .env.local or Keychain
- document packaging and release checklist

## Test Plan
- cargo test -p sldl-cli
- cargo fmt --check
- ./scripts/verify-rust.sh
- CONVEX_DEPLOYMENT= npm run convex:check
- npx tsc --noEmit
- ./scripts/package/build-archive.sh
- shasum -a 256 -c dist/sldl-unified-macos-arm64-v0.1.0.sha256
- unpack/install/config/status/uninstall smoke from the generated archive using a temp HOME and install prefix
