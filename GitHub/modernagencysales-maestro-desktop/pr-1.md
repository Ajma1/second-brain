---
type: pr
source: github
date: 2026-03-30
url: https://github.com/modernagencysales/maestro-desktop/pull/1
title: "feat: implement desktop OAuth login with secure token storage"
repo: modernagencysales/maestro-desktop
number: 1
state: closed
author: kimprobably
---

# feat: implement desktop OAuth login with secure token storage

PR #1 in modernagencysales/maestro-desktop — closed — by kimprobably — [https://github.com/modernagencysales/maestro-desktop/pull/1](https://github.com/modernagencysales/maestro-desktop/pull/1)

## Summary

- Adds complete OAuth login flow: local HTTP callback server on random port, browser-based Supabase OAuth, and token exchange via `/api/auth/desktop-exchange`
- Stores access + refresh tokens encrypted in macOS Keychain via Electron `safeStorage`
- Auto-refreshes tokens every 50 minutes (tokens expire at 60min), with graceful handling of transient failures vs revoked tokens
- Wires `AUTH_START_LOGIN` and `AUTH_LOGOUT` IPC channels to the auth service, and re-emits auth state on renderer load to solve initialization race
- Includes 35 tests across token store, OAuth server, and auth service

## Test plan

- [x] `pnpm typecheck` passes (both node and web targets)
- [x] `pnpm test` — 35 tests pass
- [x] `pnpm lint` — clean
- [ ] Manual smoke test: `pnpm dev` → tray appears → login opens browser → callback returns to app

🤖 Generated with [Claude Code](https://claude.com/claude-code)
