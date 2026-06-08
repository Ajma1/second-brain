# Self-Updating Brain Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** A `/brain-sync` playbook, run by one daily Anthropic-cloud Claude Code routine, that mirrors new Linear + GitHub activity into vault notes, folds Slack into digests, writes daily + weekly notes, and commits/pushes itself — keeping the vault live with no human in the loop.

**Architecture:** One slash-command playbook (`.claude/commands/brain-sync.md`) executed unattended in the cloud. It reads JSON config + per-source timestamp watermarks from `_meta/state/`, fetches items newer than each watermark (Linear MCP, `gh` CLI, Slack Web API via `$SLACK_TOKEN`), writes notes idempotently (create-if-absent, never overwrite human edits), advances watermarks only on success, writes a daily digest (+ weekly review on Mondays), then commits and pushes to `origin/master`. A single scheduled routine fires it daily at 06:00 America/New_York.

**Tech Stack:** Claude Code slash commands + scheduled routines (cloud cron); Linear MCP; GitHub `gh` CLI; Slack Web API (`curl` + `$SLACK_TOKEN`); git; JSON state files; macOS `date` for install-time seeding.

---

## File structure

| Path | Responsibility |
|------|----------------|
| `_meta/state/sources.json` | Config: Linear team, GitHub repos + gist watch-list, Slack channels. Hand-editable. |
| `_meta/state/linear.json` | Watermark: `{ "since": ISO }`. |
| `_meta/state/github.json` | Watermark: `{ "since": ISO }`. |
| `_meta/state/slack.json` | Watermark: `{ "channels": { "<id>": { "oldest": "<unix-ts>" } } }`. |
| `_meta/state/run-log.md` | Append-only health log, one line per run. |
| `.claude/commands/brain-sync.md` | The playbook. Built up section-by-section across tasks 2–8. |
| `_meta/automations.md` | Flip subsystem #3 → built (Task 10). |
| `Templates/weekly-review.md` | Created if absent (Task 7 needs it). |

All `_meta/state/*` is committed (it IS the automation's memory). Nothing here is in `.gitignore`.

---

## Pre-flight for the implementer

- [ ] **Step 0: Confirm working dir + clean tree**

Run: `cd /Users/ajmal/Maestro/Hobby && git status --short`
Expected: only the untracked `.playwright-mcp/`, `*.png` files from before — no staged changes. The new spec commit `603ab57` is already in history (`git log -1 --oneline`).

---

### Task 1: State + config scaffolding

**Files:**
- Create: `_meta/state/sources.json`
- Create: `_meta/state/linear.json`
- Create: `_meta/state/github.json`
- Create: `_meta/state/slack.json`
- Create: `_meta/state/run-log.md`

- [ ] **Step 1: Write `sources.json`**

Seed the GitHub repo list from the existing `GitHub/` folder names (strip the `modernagencysales-` / owner prefix back to `owner/repo`). Create `_meta/state/sources.json`:

```json
{
  "linear": { "team": "Maestro V2" },
  "github": {
    "repos": [
      "modernagencysales/maestro-v2",
      "modernagencysales/maestro-app",
      "modernagencysales/maestro-desktop",
      "modernagencysales/maestro-factory",
      "modernagencysales/maestro-agent-planning",
      "modernagencysales/gtm-system",
      "modernagencysales/gc-member-portal",
      "modernagencysales/magnetlab",
      "modernagencysales/mas-platform",
      "modernagencysales/linkedin-leadmagnet-admin"
    ],
    "gists": [
      "https://gist.github.com/kimprobably/24ed1a33700bac09e19a2372ae1289c1"
    ]
  },
  "slack": {
    "channels": [
      { "id": "C0B4GF3EY2Y", "name": "Maestro-v2" }
    ]
  }
}
```

- [ ] **Step 2: Seed the watermarks at the current gap edge (Jun 6)**

The vault was last ingested 2026-06-06, so seed Linear/GitHub `since` there to close the gap forward on first run. Seed Slack `oldest` to 7 days before today (no Slack history exists yet; avoid flooding).

Run:
```bash
cd /Users/ajmal/Maestro/Hobby
printf '{\n  "since": "2026-06-06T00:00:00Z"\n}\n' > _meta/state/linear.json
printf '{\n  "since": "2026-06-06T00:00:00Z"\n}\n' > _meta/state/github.json
SLACK_OLDEST=$(date -u -v-7d +%s)
printf '{\n  "channels": {\n    "C0B4GF3EY2Y": { "oldest": "%s" }\n  }\n}\n' "$SLACK_OLDEST" > _meta/state/slack.json
```

- [ ] **Step 3: Write `run-log.md` header**

Create `_meta/state/run-log.md`:

```markdown
# brain-sync run log

One line per run: `YYYY-MM-DD HH:MM | linear=N github=M slack=K | <status notes>`.
Sources that fail a run keep their watermark and retry next run.

---
```

- [ ] **Step 4: Verify all JSON parses**

Run:
```bash
cd /Users/ajmal/Maestro/Hobby
for f in sources linear github slack; do python3 -c "import json;json.load(open('_meta/state/$f.json'));print('$f OK')"; done
```
Expected:
```
sources OK
linear OK
github OK
slack OK
```

- [ ] **Step 5: Commit**

```bash
cd /Users/ajmal/Maestro/Hobby
git add _meta/state/
git commit -m "brain-sync: state + config scaffolding"
```

---

### Task 2: Playbook skeleton + preflight

**Files:**
- Create: `.claude/commands/brain-sync.md`

- [ ] **Step 1: Write the command file with frontmatter, run-mode arg, and preflight section**

Create `.claude/commands/brain-sync.md`. This is the playbook the cloud agent executes. Built incrementally — this task lays the skeleton + preflight; later tasks append source sections.

````markdown
---
description: Daily ingestion — mirror Linear + GitHub, digest Slack, write daily/weekly notes, commit + push.
argument-hint: "[preflight]"
allowed-tools: Bash, Read, Write, Edit, Glob, Grep, mcp__linear__list_issues, mcp__linear__get_issue
---

You are the unattended daily ingestion agent for this Obsidian second-brain vault.
Working dir is the vault root. Follow this playbook top to bottom. Be deterministic
and idempotent. NEVER overwrite a human-edited note — create-if-absent or append only.

**Run mode:** if `$ARGUMENTS` contains `preflight`, run ONLY the Preflight section
below, print the connector report, and STOP without writing notes, watermarks, or
committing.

## 0. Load state

Read these files:
- `_meta/state/sources.json` — what to watch.
- `_meta/state/linear.json`, `_meta/state/github.json`, `_meta/state/slack.json` — watermarks.

Capture the run timestamp once: `RUN_TS=$(date -u +%Y-%m-%dT%H:%M:%SZ)` and the
local date `RUN_DAY=$(date +%Y-%m-%d)` and weekday `RUN_DOW=$(date +%u)` (1=Mon).

## 1. Preflight (always runs; sole action in preflight mode)

Probe each connector. Record each as `ok` or `failed: <reason>`. A failed probe
DISABLES that source for this run (skip it; do not error the whole run).

- **Linear:** call `mcp__linear__list_issues` with `team` from sources.json, `limit: 1`.
  ok if it returns without error.
- **GitHub:** run `gh auth status` (Bash). ok if exit 0.
- **Slack:** if `$SLACK_TOKEN` is unset → `failed: no token` (expected until provided).
  Else run `curl -s -H "Authorization: Bearer $SLACK_TOKEN" https://slack.com/api/auth.test`
  and check `.ok == true`.

Print a report:
```
PREFLIGHT (RUN_TS)
  linear: <ok|failed: …>
  github: <ok|failed: …>
  slack:  <ok|failed: …>
```
If run mode is `preflight`, STOP here.
````

- [ ] **Step 2: Verify the command is discovered**

Run: `cd /Users/ajmal/Maestro/Hobby && ls .claude/commands/brain-sync.md && head -5 .claude/commands/brain-sync.md`
Expected: file exists, frontmatter `description:` line shows.

- [ ] **Step 3: Run preflight mode and observe the report**

In Claude Code (this session), invoke: `/brain-sync preflight`
Expected output: a `PREFLIGHT` block. `linear: ok`, `github: ok`, `slack: failed: no token` (token not yet provided). No files written, no commit. Confirm `git status --short` shows no new changes from the run.

- [ ] **Step 4: Commit**

```bash
cd /Users/ajmal/Maestro/Hobby
git add .claude/commands/brain-sync.md
git commit -m "brain-sync: playbook skeleton + preflight"
```

---

### Task 3: Linear ingestion section

**Files:**
- Modify: `.claude/commands/brain-sync.md` (append section 2)

- [ ] **Step 1: Append the Linear section to the playbook**

Append to `.claude/commands/brain-sync.md` (after the Preflight section):

````markdown
## 2. Linear (1:1 mirror) — skip if preflight failed

1. Read `since` from `_meta/state/linear.json`.
2. Call `mcp__linear__list_issues` with `team` from sources.json, `updatedAt: "<since>"`,
   `orderBy: updatedAt`, `limit: 100`. (Linear accepts an ISO timestamp for `updatedAt`.)
3. For each returned issue, target path `Linear/<identifier>.md` (e.g. `Linear/MAE-218.md`).
   - If the file already exists → skip (idempotent; never overwrite).
   - Else call `mcp__linear__get_issue` for the full description and write the note:

```markdown
---
type: project
source: linear
id: <identifier>
title: "<title>"
status: <status>
priority: <priority name>
assignee: <assignee or unassigned>
team: <team>
created: <createdAt>
updated: <updatedAt>
url: <url>
tags: [linear, "#project/maestro-v2"]
---

# <identifier> — <title>

<description markdown>

## Links
- Linear: <url>
```

4. Track `count_linear` = notes created (not skipped).
5. New watermark = the max `updatedAt` across all returned issues (or keep old `since`
   if none returned). Do NOT write it yet — only commit watermarks in section 6 after
   success, so a later failure doesn't strand a half-advanced cursor. Hold it in memory
   as `linear_new_since`.
````

- [ ] **Step 2: Run a real sync (Linear only path) and verify the gap closes**

Invoke `/brain-sync` (full mode). Because Slack has no token and you haven't added GitHub yet, expect Linear to ingest. After the run:

Run: `cd /Users/ajmal/Maestro/Hobby && ls Linear/MAE-218.md Linear/MAE-219.md Linear/MAE-220.md`
Expected: all three exist (the issues created Jun 7–8 that were missing).

Run: `head -12 Linear/MAE-218.md`
Expected: frontmatter with `id: MAE-218`, `source: linear`, title "Brain v1 — Per-Client Knowledge Layer".

- [ ] **Step 3: Verify idempotency — second run creates nothing**

Invoke `/brain-sync` again. Then:
Run: `cd /Users/ajmal/Maestro/Hobby && git status --short Linear/`
Expected: no new `Linear/MAE-*.md` files (all existing → skipped). `count_linear` reported as 0 in the run output.

- [ ] **Step 4: Commit the new notes**

```bash
cd /Users/ajmal/Maestro/Hobby
git add .claude/commands/brain-sync.md Linear/
git commit -m "brain-sync: Linear ingestion + close 2-day gap (MAE-218..220)"
```

---

### Task 4: GitHub ingestion section

**Files:**
- Modify: `.claude/commands/brain-sync.md` (append section 3)

- [ ] **Step 1: Append the GitHub section**

Append to `.claude/commands/brain-sync.md`:

````markdown
## 3. GitHub (1:1 mirror) — skip if preflight failed

1. Read `since` from `_meta/state/github.json`.
2. For each `repo` in sources.json `github.repos`:
   - PRs: `gh pr list --repo <repo> --state all --search "updated:>=<since-date>" --json number,title,state,author,updatedAt,createdAt,url,body --limit 100`
     (use the date portion of `since`, `YYYY-MM-DD`).
   - Issues: `gh issue list --repo <repo> --state all --search "updated:>=<since-date>" --json number,title,state,author,updatedAt,createdAt,url,body --limit 100`
   - Folder name = `GitHub/<owner>-<repo>` matching existing convention
     (e.g. `modernagencysales/maestro-v2` → `GitHub/modernagencysales-maestro-v2`).
   - For each PR → `GitHub/<owner>-<repo>/pr-<number>.md`; each issue → `.../issue-<number>.md`.
     - If file exists → skip.
     - Else write:

```markdown
---
type: project
source: github
repo: <owner>/<repo>
kind: <pr|issue>
number: <number>
title: "<title>"
state: <state>
author: <author login>
created: <createdAt>
updated: <updatedAt>
url: <url>
tags: [github]
---

# <owner>/<repo> #<number> — <title>

<body markdown>

## Links
- GitHub: <url>
```

3. **Gists:** for each url in `github.gists`, derive a slug from the gist id; target
   `03-Resources/gist-<slug>.md`. If absent, `gh gist view <id> --raw` (or `curl` the
   `/raw` URL) and write the content with frontmatter `type: reference, source: github-gist, url:`.
4. Track `count_github` = files created. Hold `github_new_since` = max `updatedAt` seen.
````

- [ ] **Step 2: Run and verify GitHub ingest**

Invoke `/brain-sync`. Then:
Run: `cd /Users/ajmal/Maestro/Hobby && ls 03-Resources/gist-*.md`
Expected: a `gist-*.md` file for the kimprobably Brain v1 spec exists.

Run: `cd /Users/ajmal/Maestro/Hobby && grep -l "source: github" GitHub/*/*.md | head`
Expected: at least the gist, plus any PRs/issues updated since Jun 6 (may be zero PRs if none updated — that is fine; the gist proves the path).

- [ ] **Step 3: Verify idempotency**

Invoke `/brain-sync` again.
Run: `cd /Users/ajmal/Maestro/Hobby && git status --short GitHub/ 03-Resources/`
Expected: no new files.

- [ ] **Step 4: Commit**

```bash
cd /Users/ajmal/Maestro/Hobby
git add .claude/commands/brain-sync.md GitHub/ 03-Resources/
git commit -m "brain-sync: GitHub PR/issue/gist ingestion"
```

---

### Task 5: Slack ingestion section (digest-first, token-gated)

**Files:**
- Modify: `.claude/commands/brain-sync.md` (append section 4)

- [ ] **Step 1: Append the Slack section**

Append to `.claude/commands/brain-sync.md`:

````markdown
## 4. Slack (digest-first) — skip if preflight failed (no token = skip cleanly)

For each channel in sources.json `slack.channels`:
1. Read its `oldest` from `_meta/state/slack.json` (`channels[<id>].oldest`).
2. `curl -s -H "Authorization: Bearer $SLACK_TOKEN" \
     "https://slack.com/api/conversations.history?channel=<id>&oldest=<oldest>&limit=200"`
3. If `.ok != true` → mark slack `failed: <error>`, skip (do not advance watermark).
4. Collect messages (newest `ts` seen = `slack_new_oldest[<id>]`). Do NOT mirror every
   message. Instead:
   - Summarize the channel's notable activity into a few bullet points for the daily
     digest (section 5): decisions, launches, asks, blockers.
   - Promote only a genuinely notable thread to a standalone note
     `03-Resources/slack/<channel-name>-<YYYY-MM-DD>-<short-slug>.md`
     (frontmatter `type: reference, source: slack, channel:, url:`, tag `#followup`
     if it implies an owed action). Create-if-absent.
5. Track `count_slack` = standalone notes created. Stash the bullet summary text in
   memory as `slack_digest_bullets` for section 5.
````

- [ ] **Step 2: Verify graceful skip without token**

Invoke `/brain-sync` (no `SLACK_TOKEN` in env).
Expected: run output shows `slack: failed: no token`, Slack section skipped, no `03-Resources/slack/` writes, run still completes. `slack.json` unchanged.

Run: `cd /Users/ajmal/Maestro/Hobby && git status --short _meta/state/slack.json`
Expected: no change.

- [ ] **Step 3: (Conditional) verify with a token, if the operator has provided one**

Only if `SLACK_TOKEN` is available: `SLACK_TOKEN=xoxb-… ` invoke `/brain-sync`. Expect `slack: ok` and a digest bullet section populated. If no token yet, mark this step done with note "deferred until token provisioned" — the path is already proven by the graceful-skip test.

- [ ] **Step 4: Commit**

```bash
cd /Users/ajmal/Maestro/Hobby
git add .claude/commands/brain-sync.md
git commit -m "brain-sync: Slack digest-first ingestion (token-gated)"
```

---

### Task 6: Daily digest writer

**Files:**
- Modify: `.claude/commands/brain-sync.md` (append section 5)

- [ ] **Step 1: Append the daily digest section**

Append to `.claude/commands/brain-sync.md`:

````markdown
## 5. Daily digest

Target `Daily/<RUN_DAY>.md`. If it exists and has a human `## Capture` section, do
NOT overwrite it — instead replace/insert only the `## Sync digest` and `## Sync status`
sections (find-and-replace those headed blocks; append if absent). If the file does
not exist, create it from `Templates/daily.md` if present, else a minimal daily note.

Write:

```markdown
## Sync digest (<RUN_TS>)

- Linear: <count_linear> new issue(s)<, list identifiers if any>
- GitHub: <count_github> new PR/issue/gist(s)<, list if any>
- Slack: <bullets from slack_digest_bullets, or "no token / no activity">

## Sync status

- linear: <ok N | failed: …>
- github: <ok M | failed: …>
- slack:  <ok K | failed: …>
```

If a source has failed for 3+ consecutive runs (check the tail of run-log.md), add a
loud line: `> ⚠️ <source> has failed N runs — check the connector.`
````

- [ ] **Step 2: Run and verify the digest**

Invoke `/brain-sync`. Then:
Run: `cd /Users/ajmal/Maestro/Hobby && cat Daily/$(date +%Y-%m-%d).md`
Expected: contains `## Sync digest` (with Linear count) and `## Sync status` (linear ok, github ok, slack failed: no token).

- [ ] **Step 3: Verify it preserves a human Capture section**

Run:
```bash
cd /Users/ajmal/Maestro/Hobby
printf '\n## Capture\n\n- my hand-written note KEEP-ME\n' >> Daily/$(date +%Y-%m-%d).md
```
Invoke `/brain-sync` again. Then:
Run: `cd /Users/ajmal/Maestro/Hobby && grep "KEEP-ME" Daily/$(date +%Y-%m-%d).md`
Expected: `KEEP-ME` still present (human content not clobbered).

- [ ] **Step 4: Commit**

```bash
cd /Users/ajmal/Maestro/Hobby
git add .claude/commands/brain-sync.md Daily/
git commit -m "brain-sync: daily digest + sync-status writer"
```

---

### Task 7: Weekly review (Monday branch)

**Files:**
- Create: `Templates/weekly-review.md` (if absent)
- Modify: `.claude/commands/brain-sync.md` (append section 6)

- [ ] **Step 1: Create the weekly-review template if missing**

Run: `cd /Users/ajmal/Maestro/Hobby && ls Templates/weekly-review.md 2>/dev/null || echo MISSING`
If `MISSING`, create `Templates/weekly-review.md`:

```markdown
---
type: review
week: "<YYYY-Www>"
date: "<YYYY-MM-DD>"
tags: [review]
---

# Weekly Review — <YYYY-Www>

## Themes this week

## Shipped / merged

## Open follow-ups (#followup)

## Notes
```

- [ ] **Step 2: Append the weekly section to the playbook**

Append to `.claude/commands/brain-sync.md`:

````markdown
## 6. Weekly review — only when RUN_DOW == 1 (Monday)

Target `Reviews/<ISO-year>-W<week>.md` (e.g. `Reviews/2026-W24.md`; compute with
`date +%G-W%V`). If it exists → skip (idempotent; one review per week).
Else create from `Templates/weekly-review.md` and fill:
- **Themes:** synthesize from the last 7 daily notes' `## Sync digest` blocks.
- **Shipped / merged:** GitHub PRs that moved to merged/closed in the last 7 days.
- **Open follow-ups:** grep the vault for `#followup` tags still open.
Keep it concise. This is synthesis, not a dump.
````

- [ ] **Step 3: Verify the Monday branch produces a review (forced)**

The weekday gate keys on `RUN_DOW`. To test off-Monday, temporarily invoke with an explicit instruction: run `/brain-sync` and in the same session tell the agent "treat RUN_DOW as 1 for this run only." Then:
Run: `cd /Users/ajmal/Maestro/Hobby && ls Reviews/$(date +%G)-W$(date +%V).md`
Expected: the weekly file exists with the template sections filled.

(On real Mondays this happens automatically; the forced test only proves the writer.)

- [ ] **Step 4: Commit**

```bash
cd /Users/ajmal/Maestro/Hobby
git add .claude/commands/brain-sync.md Templates/weekly-review.md Reviews/
git commit -m "brain-sync: Monday weekly-review branch"
```

---

### Task 8: Commit/push + watermark advance + run-log

**Files:**
- Modify: `.claude/commands/brain-sync.md` (append section 7)

- [ ] **Step 1: Append the finalize section**

Append to `.claude/commands/brain-sync.md`:

````markdown
## 7. Finalize — advance watermarks, log, commit, push

1. **Advance watermarks** ONLY for sources that succeeded this run:
   - linear: write `{ "since": "<linear_new_since>" }` to `_meta/state/linear.json`.
   - github: write `{ "since": "<github_new_since>" }` to `_meta/state/github.json`.
   - slack: for each ok channel, set `channels[<id>].oldest = <slack_new_oldest[id]>`
     in `_meta/state/slack.json`.
   A failed source keeps its old watermark (auto-retry next run).
2. **Append run-log line** to `_meta/state/run-log.md`:
   `<RUN_TS> | linear=<count_linear> github=<count_github> slack=<count_slack> | <status notes>`
3. **Commit + push:**
   ```bash
   git add -A
   git commit -m "brain-sync: <RUN_DAY> (linear=<count_linear> gh=<count_github> slack=<count_slack>)" || echo "nothing to commit"
   git push origin master
   ```
   If `git push` fails (auth/network), append `push-failed` to the run-log line and
   leave the commit local — next run's push carries it.
4. Print a final one-line summary matching the run-log line.
````

- [ ] **Step 2: Full end-to-end run + verify finalize**

Invoke `/brain-sync`. Then:
Run: `cd /Users/ajmal/Maestro/Hobby && tail -3 _meta/state/run-log.md && cat _meta/state/linear.json && git log -1 --oneline`
Expected: a run-log line with counts; `linear.json` `since` advanced past 2026-06-06 (to the newest issue's updatedAt, ≈ Jun 8); latest commit message starts `brain-sync: <today>`.

- [ ] **Step 3: Verify watermark stops re-ingestion**

Invoke `/brain-sync` once more. Then:
Run: `cd /Users/ajmal/Maestro/Hobby && tail -1 _meta/state/run-log.md`
Expected: `linear=0 github=0` (watermark advanced; nothing new). Confirms steady-state is a clean no-op.

- [ ] **Step 4: Commit (if the playbook edit isn't already committed by the run)**

```bash
cd /Users/ajmal/Maestro/Hobby
git add .claude/commands/brain-sync.md
git commit -m "brain-sync: finalize — watermark advance, run-log, push" || echo "already committed by run"
git push origin master
```

---

### Task 9: Schedule the daily cloud routine

**Files:** none (creates a Claude Code routine)

- [ ] **Step 1: Create the routine via the schedule skill**

Invoke the `schedule` skill to create a routine:
- **Name:** `brain-sync-daily`
- **Cron:** `0 6 * * *`
- **Timezone:** `America/New_York`
- **Command:** `/brain-sync`
- **Repo:** this vault (`Ajma1/second-brain`, branch `master`)

- [ ] **Step 2: Verify the routine is registered**

List routines (schedule skill / `CronList`).
Expected: `brain-sync-daily` present, next-run timestamp ~tomorrow 06:00 ET.

- [ ] **Step 3: Trigger one cloud run manually and read the result**

Trigger `brain-sync-daily` once now (schedule skill run-now). After it completes:
Run: `cd /Users/ajmal/Maestro/Hobby && git pull --ff-only && tail -2 _meta/state/run-log.md`
Expected: a run-log line authored by the cloud run (proves the routine has repo + connector access in the cloud — the rollout's key risk). If Linear/GitHub show `failed` in the cloud, the connector isn't available headless → note it for follow-up; local runs still work.

---

### Task 10: Flip automations.md #3 → built

**Files:**
- Modify: `_meta/automations.md`

- [ ] **Step 1: Update the registry table row**

In `_meta/automations.md`, change subsystem #3's status from `⏳ planned` to `✅ built` and replace the planned write-contract block with the real one.

Edit the table row:
```
| 3 | **Scheduled Automations** | ✅ built | `Linear/`, `GitHub/`, `03-Resources/`, `Daily/`, `Reviews/`, `_meta/state/` |
```

Replace the `### #3` block with:
```markdown
### #3 — Scheduled Automations (built)
- **Trigger:** daily cloud cron (routine `brain-sync-daily`, 06:00 America/New_York),
  command `/brain-sync`. Weekly review on Mondays via the same command.
- **Reads:** Linear MCP, GitHub `gh`, Slack Web API (`$SLACK_TOKEN`); watermarks in
  `_meta/state/{linear,github,slack}.json`; config in `_meta/state/sources.json`.
- **Writes:** `Linear/MAE-#.md`, `GitHub/<owner>-<repo>/{pr,issue}-#.md`,
  `03-Resources/` (gists, notable Slack threads), `Daily/YYYY-MM-DD.md`,
  `Reviews/YYYY-Www.md` (Mon), `_meta/state/run-log.md`.
- **Idempotency:** create-if-absent notes; never overwrite human edits; watermark-gated
  fetch; watermark advances only on per-source success.
- **Spec/plan:** `docs/superpowers/specs/2026-06-08-self-updating-brain-design.md`,
  `docs/superpowers/plans/2026-06-08-self-updating-brain.md`.
```

- [ ] **Step 2: Verify**

Run: `cd /Users/ajmal/Maestro/Hobby && grep -A1 "Scheduled Automations" _meta/automations.md | head -2`
Expected: shows `✅ built`.

- [ ] **Step 3: Commit + push**

```bash
cd /Users/ajmal/Maestro/Hobby
git add _meta/automations.md
git commit -m "brain-sync: mark subsystem #3 built in automations registry"
git push origin master
```

---

## Definition of done

- `/brain-sync preflight` reports connector health without writing.
- A full `/brain-sync` closes the current gap (MAE-218..220 + Brain v1 gist) and a
  second run is a clean `linear=0 github=0` no-op.
- Daily note has `## Sync digest` + `## Sync status`; human `## Capture` survives re-runs.
- `brain-sync-daily` routine exists and a manual cloud trigger writes a run-log line
  and pushes to `origin/master`.
- Slack degrades gracefully without a token and activates when `$SLACK_TOKEN` is set.
- `_meta/automations.md` #3 = built.

## Operator follow-ups (outside this plan)

- Provision `SLACK_TOKEN` (`channels:history`, `groups:history`) into the routine env;
  confirm Slack flips to `ok` in the next run-log line.
- Sync the vault locally via `git pull` or the Obsidian Git plugin so cloud-written
  notes appear in Obsidian.
