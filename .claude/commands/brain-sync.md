---
description: Daily ingestion — mirror Linear + GitHub, digest Slack, write daily/weekly notes, commit + push.
argument-hint: "[preflight]"
allowed-tools: Bash, Read, Write, Edit, Glob, Grep, mcp__linear__list_issues, mcp__linear__get_issue
---

You are the unattended daily ingestion agent for this Obsidian second-brain vault. Working dir is the vault root. Follow this playbook top to bottom. Be deterministic and idempotent. NEVER overwrite a human-edited note — create-if-absent or append only.

**Run mode:** if `$ARGUMENTS` contains `preflight`, run ONLY the Preflight section, print the connector report, and STOP without writing notes, watermarks, or committing.

## 0. Load state

Read these files:
- `_meta/state/sources.json` — what to watch.
- `_meta/state/linear.json`, `_meta/state/github.json`, `_meta/state/slack.json` — watermarks.

Capture run identifiers once (Bash): `RUN_TS=$(date -u +%Y-%m-%dT%H:%M:%SZ)`, `RUN_DAY=$(date +%Y-%m-%d)`, `RUN_DOW=$(date +%u)` (1=Mon).

## 1. Preflight (always runs; sole action in preflight mode)

Probe each connector. Record each as `ok` or `failed: <reason>`. A failed probe DISABLES that source for this run (skip it; do not error the whole run).

- **Linear:** call `mcp__linear__list_issues` with `team` from sources.json, `limit: 1`. ok if it returns without error.
- **GitHub:** run `gh auth status`. ok if exit 0.
- **Slack:** if `$SLACK_TOKEN` is unset → `failed: no token` (expected until provided). Else run `curl -s -H "Authorization: Bearer $SLACK_TOKEN" https://slack.com/api/auth.test` and check `.ok == true`.

Print:
```
PREFLIGHT (RUN_TS)
  linear: <ok|failed: …>
  github: <ok|failed: …>
  slack:  <ok|failed: …>
```
If run mode is `preflight`, STOP here.

## 2. Linear (1:1 mirror) — skip if preflight failed

1. Read `since` from `_meta/state/linear.json`.
2. Call `mcp__linear__list_issues` with `team` from sources.json, `updatedAt: "<since>"`, `orderBy: updatedAt`, `limit: 100`.
3. For each returned issue, target `Linear/<identifier>.md` (e.g. `Linear/MAE-218.md`).
   - If the file exists → skip (idempotent; never overwrite).
   - Else call `mcp__linear__get_issue` for the full description and write:

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
5. Hold `linear_new_since` = max `updatedAt` across returned issues (or keep old `since` if none). Do NOT write the watermark here — only in section 7.

## 3. GitHub (1:1 mirror) — skip if preflight failed

1. Read `since` from `_meta/state/github.json`; use its date portion `YYYY-MM-DD` as `<since-date>`.
2. For each `repo` in sources.json `github.repos`:
   - PRs: `gh pr list --repo <repo> --state all --search "updated:>=<since-date>" --json number,title,state,author,updatedAt,createdAt,url,body --limit 100`
   - Issues: `gh issue list --repo <repo> --state all --search "updated:>=<since-date>" --json number,title,state,author,updatedAt,createdAt,url,body --limit 100`
   - Folder = `GitHub/<owner>-<repo>` (e.g. `modernagencysales/maestro-v2` → `GitHub/modernagencysales-maestro-v2`).
   - PR → `<folder>/pr-<number>.md`; issue → `<folder>/issue-<number>.md`. If file exists → skip. Else write:

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

3. **Gists:** for each url in `github.gists`, derive the gist id; target `03-Resources/gist-<id-short>.md`. If absent, `gh gist view <id> --raw` (or curl the `/raw` URL) and write with frontmatter `type: reference, source: github-gist, url: <url>`.
4. Track `count_github` = files created. Hold `github_new_since` = max `updatedAt` seen.

## 4. Slack (digest-first) — skip if preflight failed (no token = skip cleanly)

For each channel in sources.json `slack.channels`:
1. Read its `oldest` from `_meta/state/slack.json` (`channels[<id>].oldest`).
2. `curl -s -H "Authorization: Bearer $SLACK_TOKEN" "https://slack.com/api/conversations.history?channel=<id>&oldest=<oldest>&limit=200"`
3. If `.ok != true` → mark slack `failed: <error>`, skip (do not advance watermark).
4. Collect messages; newest `ts` seen = `slack_new_oldest[<id>]`. Do NOT mirror every message:
   - Summarize notable activity into a few bullets for the daily digest (decisions, launches, asks, blockers).
   - Promote only a genuinely notable thread to a standalone note `03-Resources/slack/<channel-name>-<YYYY-MM-DD>-<short-slug>.md` (frontmatter `type: reference, source: slack, channel:, url:`; tag `#followup` if it implies an owed action). Create-if-absent.
5. Track `count_slack` = standalone notes created. Stash bullets as `slack_digest_bullets`.

## 5. Daily digest

Target `Daily/<RUN_DAY>.md`. If it exists with a human `## Capture` section, do NOT overwrite it — only replace/insert the `## Sync digest` and `## Sync status` headed blocks (append if absent). If absent, create from `Templates/daily.md` if present, else a minimal daily note. Write:

```markdown
## Sync digest (<RUN_TS>)

- Linear: <count_linear> new issue(s)<, identifiers if any>
- GitHub: <count_github> new PR/issue/gist(s)<, list if any>
- Slack: <slack_digest_bullets, or "no token / no activity">

## Sync status

- linear: <ok N | failed: …>
- github: <ok M | failed: …>
- slack:  <ok K | failed: …>
```

If a source has failed 3+ consecutive runs (check tail of run-log.md), add: `> ⚠️ <source> has failed N runs — check the connector.`

## 6. Weekly review — only when RUN_DOW == 1 (Monday)

Target `Reviews/<ISO-year>-W<week>.md` (compute `date +%G-W%V`). If it exists → skip. Else create from `Templates/weekly-review.md` and fill:
- **Themes:** synthesize from the last 7 daily notes' `## Sync digest` blocks.
- **Shipped / merged:** GitHub PRs moved to merged/closed in the last 7 days.
- **Open follow-ups:** grep the vault for open `#followup` tags.
Concise synthesis, not a dump.

## 7. Finalize — advance watermarks, log, commit, push

1. Advance watermarks ONLY for sources that succeeded:
   - linear: write `{ "since": "<linear_new_since>" }` to `_meta/state/linear.json`.
   - github: write `{ "since": "<github_new_since>" }` to `_meta/state/github.json`.
   - slack: for each ok channel, set `channels[<id>].oldest = <slack_new_oldest[id]>` in `_meta/state/slack.json`.
   A failed source keeps its old watermark.
2. Append to `_meta/state/run-log.md`: `<RUN_TS> | linear=<count_linear> github=<count_github> slack=<count_slack> | <status notes>`
3. Commit + push:
   ```bash
   git add -A
   git commit -m "brain-sync: <RUN_DAY> (linear=<count_linear> gh=<count_github> slack=<count_slack>)" || echo "nothing to commit"
   git push origin master
   ```
   If push fails, append `push-failed` to the run-log line and leave the commit local.
4. Print a final one-line summary matching the run-log line.
