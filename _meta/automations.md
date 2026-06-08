# Automation Registry

Tracks what writes where, so subsystems stay decoupled. Update this whenever an
automation's write contract changes.

## The 3-part system

| # | Subsystem | Status | Writes to |
|---|-----------|--------|-----------|
| 1 | **Vault Foundation** | ✅ built | — (this structure) |
| 2 | **Call Transcript Ingestion** | ⏳ planned | `Calls/`, `Daily/` |
| 3 | **Scheduled Automations** | ✅ built | `Linear/`, `GitHub/`, `03-Resources/`, `Daily/`, `Reviews/`, `_meta/state/` |

Spec docs: `docs/superpowers/specs/`.

## Write contracts

### #2 — Call Transcript Ingestion (planned)
- **Trigger:** new transcript email to `ajmal@maestrogtm.com`.
- **Reads:** Gmail/source inbox.
- **Writes:**
  - one note per call in `Calls/` using `Templates/call.md`
    (fills Summary, Action items, Transcript, Links).
  - appends a backlink under `## Calls logged` in that day's `Daily/` note.
- **Frontmatter set:** `type: call`, `date`, `attendees`, `source`, `project`.

### #3 — Scheduled Automations (built)
- **Trigger:** daily cloud routine `brain-sync-daily`
  (`trig_018RgMeTeas37jLRyykeW289`, cron `0 10 * * *` = 06:00 America/New_York),
  command `/brain-sync`. Weekly review produced by the same command on Mondays.
- **Playbook:** `.claude/commands/brain-sync.md`.
- **Reads:** Linear MCP, GitHub `gh`, Slack Web API (`$SLACK_TOKEN`); config in
  `_meta/state/sources.json`; watermarks in `_meta/state/{linear,github,slack}.json`.
- **Writes:** `Linear/MAE-#.md`, `GitHub/<owner>-<repo>/{pr,issue}-#.md`,
  `03-Resources/` (gists, notable Slack threads), `Daily/YYYY-MM-DD.md`,
  `Reviews/YYYY-Www.md` (Mondays), `_meta/state/run-log.md`.
- **Idempotency:** create-if-absent notes; never overwrite human edits;
  watermark-gated fetch; per-source watermark advances only on success. One
  source failing never fails the run.
- **Spec/plan:** `docs/superpowers/specs/2026-06-08-self-updating-brain-design.md`,
  `docs/superpowers/plans/2026-06-08-self-updating-brain.md`.
- **Operator follow-ups (cloud runs):**
  - **GitHub re-auth required** — the cloud routine returned
    `github_repo_access_denied`; re-authorize GitHub at https://claude.ai/code/routines
    so the remote agent can check out the repo. Local runs already work.
  - **Linear/Slack in cloud** — Linear MCP and Slack are not attached to the
    routine's connectors; until provisioned, cloud runs degrade to GitHub +
    digests and mark the others `failed` in the run-log (by design).
  - **Slack token** — provide a fresh `SLACK_TOKEN` (`channels:history`); the
    one found locally was `token_expired`.

## Conventions automations must honor
- Always set frontmatter `type:`.
- Never overwrite a human-edited section; append or fill only empty placeholders.
- Use canonical tags from `tags.md`.
