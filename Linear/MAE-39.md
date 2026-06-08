---
type: issue
source: linear
date: 2026-05-21
url: "https://linear.app/modern-agency-sales/issue/MAE-39/31-artifact-schema-redditinstagram-actors"
title: "3.1 · Artifact schema + Reddit/Instagram actors"
identifier: "MAE-39"
status: "Done"
assignee: "ajmal@maestrogtm.com"
team: "MAE"
---

# 3.1 · Artifact schema + Reddit/Instagram actors

- **Identifier:** MAE-39
- **Status:** Done
- **Assignee:** ajmal@maestrogtm.com
- **Team:** MAE
- **Updated:** 2026-05-21
- **URL:** https://linear.app/modern-agency-sales/issue/MAE-39/31-artifact-schema-redditinstagram-actors

**Why.** A brain built only from documents the agency uploads is thin. Research feeds it *real buyer signal* from the web — and that signal arrives as scraped **artifacts** (layer 0) that claims then cite. This issue builds the acquisition layer.

**What.** The scraped-artifact schema (id, origin, source URL, `provenance_hint`, acquired-at); wire Apify Reddit + Instagram actors into the Apify client; Firecrawl as the fallback. (LinkedIn scraping is reused from mas-platform.)

**Read first.** [Team Onboarding](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/team-onboarding.md>) · [Spec 3 — Brain Research](<https://github.com/modernagencysales/maestro-agent-planning/blob/main/docs/operator/specs/active/gtm-client-lead-loop/specs/brain-research.md>)

**Done when.** The scraped-artifact schema (id, origin, source URL, `provenance_hint`, acquired-at) is checked in, the Apify Reddit + Instagram actors and the Firecrawl fallback are wired into the Apify client, and a test confirms each produces a valid artifact with a resolvable URL locator.
