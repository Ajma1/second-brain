---
type: pr
source: github
date: 2026-06-17
url: https://github.com/modernagencysales/maestro-v2-3/pull/9
title: "feat: Solo creator UI · two-mode experience for individual writers"
repo: modernagencysales/maestro-v2-3
number: 9
state: open
author: malaika-mas
---

# feat: Solo creator UI · two-mode experience for individual writers

PR #9 in modernagencysales/maestro-v2-3 — open — by malaika-mas — [https://github.com/modernagencysales/maestro-v2-3/pull/9](https://github.com/modernagencysales/maestro-v2-3/pull/9)

## What this does

Adds a second mode to Maestro for solo creators (founders, execs, coaches) who write their own LinkedIn content. The landing page now lets you choose:

- **"I write my own content"** → solo experience (this PR)
- **"I write for clients"** → ghostwriter experience (existing)

## What's new

**Landing page** (`landing.tsx`)
- Two-path hero: pick solo or ghostwriter before onboarding

**Solo onboarding** (`solo-onboarding.tsx`)
- 3 steps: your info → feed the brain → voice profile ready

**Solo app shell** (`SoloApp.tsx`)
- 6 sidebar items only: Home, Ideas, Write, My Posts, Analytics, Settings
- Maestro AI chat sidebar on all pages except Home

**6 solo pages** (`pages/solo/`)

| Page | What it does |
|------|-------------|
| Home | Centered AI prompt, recent posts, suggested actions |
| Ideas | Idea capture inbox (voice memos, transcripts, saved posts), AI topic generator |
| Write | LinkedIn preview editor, AI draft, voice match, schedule/publish |
| My Posts | Content library, filter tabs, repurpose to carousel/newsletter/thread |
| Analytics | Top posts, engagement trends, "What's working" AI insights, consistency grid |
| Settings | Profile, LinkedIn connection, posting schedule, voice retraining |

## Why solo is different from ghostwriter

No multi-client management, no approvals, no warm leads, no marketplace, no funnels. Just: capture ideas → write → post → learn. 5 sidebar items vs 15+.

## How to test

```bash
cd apps/prototype && pnpm dev
```

Then in browser console:
```js
localStorage.removeItem("maestro-onboarded")
localStorage.removeItem("maestro-mode")
```

Refresh → landing page → click "I write my own content" → onboarding → solo app.

🤖 Generated with [Claude Code](https://claude.com/claude-code)
