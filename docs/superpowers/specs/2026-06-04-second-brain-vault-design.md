# Second-Brain Vault — Foundation Design

**Date:** 2026-06-04
**Status:** Approved (design), pending implementation
**Subsystem:** #1 of 3 (Vault Foundation)
**Owner:** ajmal@maestrogtm.com

## Context

Build a personal/work "second brain" in Obsidian from scratch. This is the
foundation layer of a 3-part system:

1. **Vault Foundation** (this spec) — structure, templates, conventions, git.
2. **Call Transcript Ingestion** (future spec) — read email from
   `ajmal@maestrogtm.com`, extract transcripts, write notes into the vault.
3. **Scheduled Automations** (future spec) — daily digest, weekly review,
   Unblocked work-context digest, follow-up tracking.

This spec covers ONLY #1. Subsystems #2 and #3 consume the seams defined here.

## Decisions (locked)

| Decision | Choice |
|----------|--------|
| Vault location | `~/Maestro/Hobby/` |
| Version control | git → private GitHub repo `Ajma1/second-brain` |
| Org method | PARA + daily-note capture + knowledge zone |
| Templates | 6, plugin-free (Obsidian core Templates plugin only) |
| Unblocked role | weekly work-context digest (deferred to #3) |
| Mobile sync | none (desk + git history) |
| Commit identity | `ajmal@maestrogtm.com` |

## Folder Skeleton

```
second-brain/
├── 00-Inbox/              # raw capture, unsorted — drop zone
├── 01-Projects/           # active, deadline-bound efforts (PARA P)
├── 02-Areas/              # ongoing responsibilities, no end date (PARA A)
├── 03-Resources/          # reference, topics, knowledge zone (PARA R)
├── 04-Archive/            # done/inactive (PARA Archive)
├── Calls/                 # call transcript notes — #2 writes here
├── People/                # contact notes
├── Daily/                 # daily notes YYYY-MM-DD
├── Reviews/               # weekly reviews + Unblocked digests — #3 writes here
├── Templates/             # the 6 templates
├── Attachments/           # pasted images/files
└── _meta/
    ├── README.md          # vault conventions, how it works
    ├── tags.md            # canonical tag list
    └── automations.md     # registry of what writes where (#2/#3 hook map)
```

Each folder ships with a `.gitkeep` so the empty structure is committed.

## Templates (plugin-free, in `Templates/`)

Plain markdown using only Obsidian core Templates placeholders: `{{title}}`,
`{{date}}`, `{{time}}`. No Templater. Every note carries YAML frontmatter with a
`type:` field so #2/#3 automations can query reliably.

1. **`daily.md`** — `type: daily`
   - frontmatter: `date`
   - sections: `## Capture` · `## Tasks` · `## Calls logged` · `## Notes`
2. **`call.md`** — `type: call`
   - frontmatter: `date, attendees, source, project`
   - sections: `## Summary` · `## Action items` · `## Transcript` · `## Links`
3. **`project.md`** — `type: project`
   - frontmatter: `status, due, area`
   - sections: `## Goal` · `## Next actions` · `## Log` · `## Linked`
4. **`meeting.md`** — `type: meeting`
   - frontmatter: `date, attendees`
   - sections: `## Agenda` · `## Attendees` · `## Decisions` · `## Follow-ups`
5. **`weekly-review.md`** — `type: review`
   - frontmatter: `week`
   - sections: `## Wins` · `## Open loops` · `## Work digest (Unblocked)` ·
     `## Next week`
6. **`person.md`** — `type: person`
   - frontmatter: `org, role`
   - sections: `## Context` · `## Last touch` · `## Open threads`

## Conventions

- **Tags (canonical, in `tags.md`):** `#project/x`, `#area/x`, `#person/x`,
  `#call`, `#followup`, `#open`, `#done`.
- **Links:** wikilinks `[[ ]]`. Call notes link to People + Project. Daily notes
  backlink any call logged that day.
- **Frontmatter typing:** every note has `type:` — drives automation queries.
- **Seams for later subsystems:**
  - `Calls/` + `Daily/` → where #2 (transcript ingestion) writes.
  - `Reviews/` → where #3 (digests/weekly review) writes.
  - `_meta/automations.md` documents the write contract so #2/#3 stay decoupled.

## Obsidian Config (`.obsidian/`)

- Enable core **Templates** plugin, template folder = `Templates/`.
- Enable core **Daily notes**, folder = `Daily/`, format `YYYY-MM-DD`,
  template = `Templates/daily.md`.
- Commit `.obsidian/` config (so settings travel); ignore volatile workspace
  state.

## Git / GitHub

- `git init` in `~/Maestro/Hobby/`.
- `.gitignore`: `.obsidian/workspace*`, `.DS_Store`, `.trash/`, `Attachments/`
  large binaries optional (keep for now).
- Create **private** repo `Ajma1/second-brain` (private — holds personal data
  and future transcripts).
- Initial commit + push. Commit identity `ajmal@maestrogtm.com`.

## Out of Scope (future specs)

- #2 transcript ingestion (email read → parse → note).
- #3 scheduled automations (cron/Trigger.dev, Unblocked weekly digest).
- Mobile sync, plugin-based dataview dashboards.

## Success Criteria

- Repo exists, private, on `Ajma1`, pushed.
- All folders present with `.gitkeep`.
- All 6 templates present, valid markdown, correct frontmatter `type:`.
- `_meta/README.md`, `tags.md`, `automations.md` populated.
- `.obsidian/` configured for Templates + Daily notes.
- Opening the folder as an Obsidian vault works; creating a daily note pulls the
  template.
