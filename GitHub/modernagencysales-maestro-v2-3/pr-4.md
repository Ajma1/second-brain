---
type: pr
source: github
date: 2026-06-11
url: https://github.com/modernagencysales/maestro-v2-3/pull/4
title: "Foundation: Notion shell + headless-first backend hardening + strict quality wall"
repo: modernagencysales/maestro-v2-3
number: 4
state: open
author: kimprobably
---

# Foundation: Notion shell + headless-first backend hardening + strict quality wall

PR #4 in modernagencysales/maestro-v2-3 — open — by kimprobably — [https://github.com/modernagencysales/maestro-v2-3/pull/4](https://github.com/modernagencysales/maestro-v2-3/pull/4)

## What & why

Establishes the **engineering foundation** for maestro-v2-3: a Notion-grade frontend, a hardened headless-first backend, and the strict quality wall that lets agents (and humans) work safely. This is the sanctioned large foundation PR — follow-up work is appropriately-sized (see Follow-ups).

The framework is designed to be **portable** — `docs/framework/MANIFEST.md` is the copy-list for future repos with the same Next.js + Convex + Clerk stack.

## What's in it (6 commits)

- **`feat(convex)` — close systemic IDOR + headless-first hardening.** A security audit confirmed ~110 public functions trusted a client-supplied `userId`. Every public function now gates on `ctx.auth` first-line; MCP dispatch + the Co-Pilot scheduled flow reach gated logic through `internal.*` twins; `storeLinkedInOAuthToken` is internal-only behind a timing-safe callback secret; demo identity is fail-closed. ~150 new auth-rejection / dispatch tests.
- **`feat(web)` — Notion shell + decompose 18 components.** notion-kit v1 workspace shell (`(workspace)` route group, ⌘K, theme toggle, `html.nk-shell` scoped theming so legacy routes are untouched), with cmdk/​hydration/​mobile fixes and Playwright visual-regression baselines. All 18 oversized components decomposed to ≤300 lines; 83 raw buttons → design-system Button; Cmd+S stale-closure fixed.
- **`chore` — strict lint wall + CI gates + headless-first tooling.** Custom ESLint rules (no-hardcoded-colors, notion-kit-first), `@convex-dev` best-practices, react-hooks, no-floating-promises, max-lines 300, eslint-comments require-description. CI: codegen-drift, format, knip, deps-pinned, **cli-parity**, qlty `--all`, Playwright smoke + visual. `check-cli-parity.mjs` generates `docs/cli-surface.md` and fails if the UI references a non-public function.
- **`docs(CLAUDE)` — headless-first North Star + concurrency protocol + Convex best-practices as law.**

## Verification evidence

```
pnpm lint            → ✓ 0 errors (strict wall)
pnpm prettier --check → ✓ clean
pnpm typecheck       → ✓ 4/4 packages
pnpm test            → ✓ 909 convex + 155 web = 1064 passing
pnpm build           → ✓ 4/4 tasks
pnpm knip            → ✓ exit 0
node scripts/check-deps-pinned.mjs  → ✓
node scripts/check-cli-parity.mjs   → ✓ 175 public functions, UI refs valid
```

UI verified in-browser (light + dark) with committed visual-regression baselines for `/today`.

## Risk notes

- **Size:** large by design (foundation). Most of the convex diff is mechanical gating + tests; most of the web diff is behavior-preserving component moves.
- **`explicit-table-ids` rule is OFF** — its autofix is a coupled source+test-fake migration (95 + 31 files) that gets its own PR (see Follow-ups / debt-register).
- Documented `eslint-disable`s only where genuinely justified (LinkedIn brand-faithful preview, PWA theme-color meta, intentional one-shot effects) — each with a stated reason (enforced by `require-description`).
- Two `schema*` source-grep tests forced `schema.ts` into `.prettierignore` (anti-pattern, debt-registered for conversion to schema-introspection).

## Operator actions before this is live

Per `docs/deployment-env.md`: set `CLERK_JWT_ISSUER_DOMAIN`, `LINKEDIN_OAUTH_STATE_SECRET` (same on web + Convex), `OPENROUTER_API_KEY`, `TOKEN_ENCRYPTION_KEY` on the Convex deployment. Install the qlty GitHub App's coverage/diff-coverage gates (token already in GH secrets). Set branch protection to require `checks` / `qlty` / `e2e-smoke`.

## Schema changes

- [x] None / additive only

## Follow-ups (appropriately-sized PRs, tracked in docs/debt-register.md)

1. Returns-validators on ~130 public functions (+ a lint that fails registration without `returns`).
2. `explicit-table-ids` adoption (source + 31 test fakes, coupled migration).
3. ZFC intent-router → Haiku classifier; delete ~1,600 lines dead code.
4. Convert `schema*` source-grep tests to schema-introspection.

🤖 Generated with [Claude Code](https://claude.com/claude-code)


<!-- This is an auto-generated comment: release notes by coderabbit.ai -->

## Summary by CodeRabbit

* **New Features**
  - Added Brain workspace for storing and searching knowledge entries
  - Added Email Sequences builder for creating and managing automated email campaigns
  - Added Lead Magnets tool (quizzes and calculators) for lead generation
  - Added workspace navigation pages: Calendar, Clients, Learn, Method, and Today
  - Enhanced Analytics dashboard with improved data visualization and attribution tracking
  - Improved LinkedIn OAuth integration with enhanced security verification

* **Improvements**
  - Refactored Co-Pilot Home with action cards and activity management
  - Enhanced Data overview surface with comprehensive record visualization
  - Improved onboarding experience with refined voice interview flow
  - Better error handling and user feedback across features
  - Added motion/animation helpers respecting user accessibility preferences

* **Documentation**
  - Added repository guidelines and best practices
  - Enhanced environment variable configuration guidance
  - Added PR checklist template for consistent submissions

* **Chores**
  - Updated CI/CD pipelines with additional code quality checks
  - Added automated dependency management
  - Enhanced test coverage with E2E smoke tests and visual regression baselines
  - Improved code quality tooling with Semgrep and security scanning

<!-- end of auto-generated comment: release notes by coderabbit.ai -->
