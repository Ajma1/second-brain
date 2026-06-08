# Self-Updating Brain — Design

**Date:** 2026-06-08
**Status:** Approved, pending implementation plan
**Subsystem:** #3 Scheduled Automations (see `_meta/automations.md`)

## Goal

The vault updates itself every day on cloud cron — no human in the loop, no
dependence on any interactive Claude Code session. It mirrors new Linear issues
and GitHub activity into notes, folds Slack channel activity into digests, writes
a dated daily journal entry, and produces a weekly review every Monday. It must
keep running unattended and grow with the business each day.

This replaces the manual MCP backfills that produced the current vault state
(commits like "Add 100 Linear issues … via Linear MCP backfill"). After this
ships, the vault is live, not a one-time snapshot.

## Why this is needed

As of 2026-06-08 the vault is stale by ~2 days:

- **Slack** `Maestro-v2` (`C0B4GF3EY2Y`) — never pulled; only a stub in
  `03-Resources/slack-references.md`.
- **Linear** — last ingested `MAE-217` (Jun 6). Missing `MAE-218` (Brain v1),
  `MAE-219` (Braintrust × Convex), `MAE-220` (Onboarding Content Cockpit).
- **GitHub** — folder loaded Jun 6; misses later work (e.g. the Brain v1 spec
  gist dated 2026-06-07).

Nothing auto-pulls. Subsystems #2 and #3 in `automations.md` are still `planned`.

## Architecture

```
Anthropic cloud cron (daily 06:00 America/New_York)
        │  runs
        ▼
  /brain-sync   ← .claude/commands/brain-sync.md (the playbook)
        │  reads
        ├── _meta/state/sources.json     (config: repos, channels, team)
        ├── _meta/state/linear.json       (watermark)
        ├── _meta/state/github.json       (watermark, per repo)
        └── _meta/state/slack.json        (watermark, per channel)
        │  writes
        ├── Linear/MAE-#.md               (1:1 mirror)
        ├── GitHub/<repo>/{pr,issue}-#.md (1:1 mirror)
        ├── 03-Resources/...              (gists, notable Slack threads)
        ├── Daily/YYYY-MM-DD.md           (daily digest)
        ├── Reviews/YYYY-Www.md           (Mondays only)
        └── _meta/state/run-log.md        (one line per run)
        │  then
        └── git commit + push  → github.com:Ajma1/second-brain.git (master)
```

One scheduled routine total. The weekly review is a Monday branch **inside**
`/brain-sync`, not a second routine.

The vault already has a GitHub remote (`origin` →
`git@github.com:Ajma1/second-brain.git`, upstream `master`), so the cloud routine
can clone, commit, and push. The operator syncs locally via `git pull` or the
Obsidian Git plugin.

## Components

### 1. Playbook command — `.claude/commands/brain-sync.md`
The full instruction set the cloud agent runs. Self-contained: preflight, fetch
per source, write notes, advance watermarks, write digest, weekly branch, commit.
Tight and explicit because the run is unattended and model-driven.

### 2. Config — `_meta/state/sources.json`
```json
{
  "linear": { "team": "Maestro V2" },
  "github": {
    "repos": ["modernagencysales/maestro-v2", "modernagencysales/maestro-app", "..."],
    "gists": ["https://gist.github.com/kimprobably/24ed1a33700bac09e19a2372ae1289c1"]
  },
  "slack": { "channels": [ { "id": "C0B4GF3EY2Y", "name": "Maestro-v2" } ] }
}
```
The authoritative list of what to watch. Repo list seeded from the existing
`GitHub/` folder. Editable by hand to add sources.

### 3. Watermarks — `_meta/state/{linear,github,slack}.json`
The "what's new" memory. Each stores the last-seen cursor:
- `linear.json` — max `createdAt` ISO timestamp ingested.
- `github.json` — per-repo last `updatedAt` ISO timestamp.
- `slack.json` — per-channel last message `ts`.

Advanced **only** for sources whose fetch + write succeeded. A failed source keeps
its old watermark and retries next run → no gaps, no duplicates.

### 4. Run-log — `_meta/state/run-log.md`
Append one line per run: date, per-source status + counts, errors. Makes a silent
Slack failure visible. Read it to audit the automation's health over time.

## Flow (each run)

1. **Preflight.** Probe each connector: Linear MCP reachable? `gh auth status` ok?
   `SLACK_TOKEN` present and `auth.test` ok? Record result per source. A failed
   probe disables that source for this run (skip, don't error the run).
2. **Fetch.** For each enabled source, query items newer than its watermark.
3. **Write notes.** Follow vault conventions: frontmatter `type:`, wikilinks
   `[[ ]]`, canonical tags from `_meta/tags.md`. **Idempotent and append-only** —
   create file if absent, never overwrite a human-edited file (honors
   `automations.md`).
4. **Advance watermarks** only for sources that fully succeeded.
5. **Daily digest** → `Daily/YYYY-MM-DD.md`: counts, highlights, and a
   `## Sync status` block listing per-source result (failures called out loud).
6. **Weekly (Monday only)** → `Reviews/YYYY-Www.md`: week's themes, merged work,
   open `#followup` items, from `Templates/weekly-review.md`.
7. **Commit + push.** Message: `brain-sync: YYYY-MM-DD (N linear, M gh, K slack)`.
   Append the run-log line.

## Source handling

| Source | Mode | Fetch | Writes | Watermark |
|--------|------|-------|--------|-----------|
| Linear | 1:1 mirror | MCP `list_issues`, `updatedAt >= wm`, team-scoped | `Linear/MAE-#.md` | max `updatedAt` |
| GitHub | 1:1 mirror | `gh` per repo, PRs+issues `updated:>= wm-date`; gist watch-list | `GitHub/<repo>/{pr,issue}-#.md`; gists → `03-Resources/` | single global `updatedAt` (day-granularity search; idempotent file-guard absorbs re-fetch) |
| Slack | digest-first | Web API `conversations.history` since last `ts` per channel | key threads → daily digest; notable threads → standalone note + `#followup` | per-channel `ts` |

Slack is digest-first by design: mirroring every message as a note is noise. The
agent summarizes channel activity into the daily digest and only promotes notable
threads to standalone notes.

## Slack token

Slack OAuth via the claude.ai connector may be absent in headless/cloud runs, so
Slack does **not** rely on it. Instead the routine uses a stored `SLACK_TOKEN`
(user or bot token with `channels:history`, `groups:history` for private
channels) hitting the Slack Web API directly. The operator provisions this token
into the routine's environment.

Until the token lands, the Slack step skips gracefully — v1 ships **Linear +
GitHub live**, and Slack switches on the moment the token is present. No code
change needed to enable it.

## Safety & idempotency

- One source failing never fails the whole run — log it, continue, leave its
  watermark unadvanced (auto-retry next run).
- Notes are create-if-absent / append-only; human-edited files are never
  overwritten.
- Watermark-gated fetch guarantees no duplicate notes and no skipped items.
- Every run commits with a parseable message and pushes to `origin/master`.

## Scheduling

- **One routine**, daily **06:00 America/New_York**, command `/brain-sync`.
- Weekly review is produced by the same command when the run day is Monday.

## Rollout

1. **Preflight-only first run.** Before trusting it daily, run once in
   preflight-only mode (probe + report, no writes) to prove which connectors
   actually work inside the cloud routine — the real risk. Fix any gaps.
2. Enable full daily writes once preflight is green for Linear + GitHub.
3. Add `SLACK_TOKEN`; confirm Slack turns on at the next run via the run-log.

## Out of scope (v1 / YAGNI)

- Call transcript ingestion (subsystem #2) — separate spec.
- Backfilling the current 2-day gap by hand — the first real run closes it
  forward via watermarks; no manual backfill.
- Multi-workspace Slack, GitHub org-wide auto-discovery of repos (repo list is
  explicit config), and per-item LLM enrichment beyond the digest.

## Open items folded with defaults

- **Run time/TZ:** default daily 06:00 America/New_York (changeable in the routine).
- **Slack channels:** seed with `Maestro-v2` (`C0B4GF3EY2Y`); operator extends
  `sources.json`.
- **GitHub repos:** seed from existing `GitHub/` folder contents.

## Update `_meta/automations.md`

On implementation, flip subsystem #3 from `planned` to `built` and document the
write contract: trigger (daily cloud cron), reads (Linear/GitHub/Slack +
watermarks), writes (the note/digest/review/run-log paths above), frontmatter and
tag conventions honored.
