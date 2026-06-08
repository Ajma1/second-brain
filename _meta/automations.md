# Automation Registry

Tracks what writes where, so subsystems stay decoupled. Update this whenever an
automation's write contract changes.

## The 3-part system

| # | Subsystem | Status | Writes to |
|---|-----------|--------|-----------|
| 1 | **Vault Foundation** | ✅ built | — (this structure) |
| 2 | **Call Transcript Ingestion** | ⏳ planned | `Calls/`, `Daily/` |
| 3 | **Scheduled Automations** | ⏳ planned | `Reviews/`, `Daily/` |

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

### #3 — Scheduled Automations (planned)
- **Weekly review:** writes `Reviews/YYYY-Www.md` from `Templates/weekly-review.md`.
  - `## Work digest (Unblocked)` filled from Unblocked query (recent PRs /
    decisions / threads tied to active projects).
- **Daily digest (optional):** rolls open `#followup` / `#open` items into the
  daily note.
- **Trigger:** cron / scheduled agent.

## Conventions automations must honor
- Always set frontmatter `type:`.
- Never overwrite a human-edited section; append or fill only empty placeholders.
- Use canonical tags from `tags.md`.
