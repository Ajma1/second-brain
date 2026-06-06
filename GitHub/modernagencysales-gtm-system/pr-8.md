---
type: pr
source: github
date: 2026-02-24
url: https://github.com/modernagencysales/gtm-system/pull/8
title: "feat: Lead Source Auto-Refill System"
repo: modernagencysales/gtm-system
number: 8
state: closed
author: kimprobably
---

# feat: Lead Source Auto-Refill System

PR #8 in modernagencysales/gtm-system — closed — by kimprobably — [https://github.com/modernagencysales/gtm-system/pull/8](https://github.com/modernagencysales/gtm-system/pull/8)

## Summary

Implements a pluggable lead source system with automatic campaign refill capabilities:

- **4 Lead Source Adapters**: Prospeo, BlitzAPI, LinkedIn Engagement (Apify), CSV Upload
- **Smart Source Selection**: Performance-based scoring with 80/20 exploitation/exploration
- **Auto-Refill**: Scheduled task monitors PlusVibe campaigns, triggers refill when below threshold
- **Performance Tracking**: Attributes replies back to lead sources for ROI measurement

## Key Changes

### Database
- `lead_sources` - Pluggable source configurations
- `lead_source_runs` - Run tracking for each fetch
- `lead_source_performance` - Weekly performance rollups
- `campaign_refill_config` - Per-campaign refill settings

### API Endpoints
- `GET/POST /api/cold-email/sources` - List/create sources
- `GET/PATCH/DELETE /api/cold-email/sources/[id]` - CRUD operations
- `POST /api/cold-email/sources/[id]/test` - Validate source config
- `POST /api/cold-email/sources/[id]/fetch` - Manual fetch trigger
- `GET/POST /api/cold-email/refill-configs` - Refill config management
- `POST /api/cold-email/refill-configs/[id]/trigger` - Manual refill trigger

### Trigger.dev Tasks
- `check-campaign-levels` - Scheduled every 15 min
- `refill-campaign` - Refills a campaign from best source
- `fetch-from-source` - Fetches leads from a specific source

## Test plan

- [ ] Run migrations on staging
- [ ] Create a test lead source (Prospeo or CSV)
- [ ] Test manual fetch via API
- [ ] Create a refill config for a test campaign
- [ ] Verify scheduled task triggers correctly
- [ ] Check performance attribution on reply webhook

🤖 Generated with [Claude Code](https://claude.com/claude-code)
<!-- devin-review-badge-begin -->

---

<a href="https://app.devin.ai/review/kimprobably/gtm-system/pull/8">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://static.devin.ai/assets/gh-open-in-devin-review-dark.svg?v=1">
    <img src="https://static.devin.ai/assets/gh-open-in-devin-review-light.svg?v=1" alt="Open with Devin">
  </picture>
</a>
<!-- devin-review-badge-end -->
