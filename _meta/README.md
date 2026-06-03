# Second Brain — Vault Conventions

Personal + work second brain. PARA + daily-note capture + knowledge zone.
Foundation layer of a 3-part system (see `automations.md`).

## Folder map

| Folder | Holds | PARA |
|--------|-------|------|
| `00-Inbox/` | raw capture, unsorted — process to a home | — |
| `01-Projects/` | active, deadline-bound efforts | P |
| `02-Areas/` | ongoing responsibilities, no end date | A |
| `03-Resources/` | reference, topics, knowledge | R |
| `04-Archive/` | done / inactive | Archive |
| `Calls/` | call transcript notes (written by ingestion) | — |
| `People/` | contact notes | — |
| `Daily/` | daily notes `YYYY-MM-DD` | — |
| `Reviews/` | weekly reviews + Unblocked digests | — |
| `Templates/` | the 6 note templates | — |
| `Attachments/` | pasted images / files | — |
| `_meta/` | this folder — conventions + automation registry | — |

## How to use

- **Capture fast** → `00-Inbox/` or the daily note `## Capture`. Sort later.
- **Daily note** is the hub: today's tasks, calls logged, quick notes.
- **Projects vs Areas:** project has a finish line; area is forever (e.g.
  "Health", "Maestro ops"). When a project finishes → move to `04-Archive/`.
- **Everything is typed** via frontmatter `type:` — automations query on it.

## Note types (frontmatter `type:`)

`daily` · `call` · `project` · `meeting` · `review` · `person`

## Tags

See `tags.md` for the canonical list. Use existing tags before inventing new.

## Links

Wikilinks `[[ ]]`. Conventions:
- Call notes link to `[[People/...]]` and the relevant `[[01-Projects/...]]`.
- Daily notes backlink any call logged that day under `## Calls logged`.
- Project notes link their Area and key People.
