---
type: pr
source: github
date: 2026-02-24
url: https://github.com/modernagencysales/gtm-system/pull/4
title: "MOD-33: Add Webflow Forms API support and webhook handler"
repo: modernagencysales/gtm-system
number: 4
state: closed
author: kimprobably
---

# MOD-33: Add Webflow Forms API support and webhook handler

PR #4 in modernagencysales/gtm-system — closed — by kimprobably — [https://github.com/modernagencysales/gtm-system/pull/4](https://github.com/modernagencysales/gtm-system/pull/4)

## Summary

Adds Webflow Forms API support to the GTM system, enabling native Webflow form submissions to replace Tally as the form provider. This reduces external dependencies while maintaining the same lead processing pipeline.

## Changes

### Added
- **Webflow Forms API methods** in `src/lib/integrations/webflow.ts`:
  - `listForms()` - List all forms on the Webflow site
  - `getForm(formId)` - Get details of a specific form
  - `listFormSubmissions(formId)` - List submissions for a form
  - `getFormSubmission(formId, submissionId)` - Get a specific submission
- **New types**: `WebflowForm` and `WebflowFormSubmission` interfaces
- **Webflow webhook endpoint** at `/api/webhooks/webflow/route.ts`:
  - Handles form submissions from Webflow
  - Same processing pipeline as Tally: Supabase → Loops (email) → Clay (enrichment)
  - Supports hidden fields for campaign tracking (`campaign_id`, `lead_magnet_slug`)

### Changed
- Updated `.env.local.example` with:
  - `WEBFLOW_LANDING_PAGES_COLLECTION_ID` for collection configuration
  - `LEAD_MAGNET_FORM_PROVIDER` to choose between Webflow or Tally
  - Documentation for webhook configuration
- Exported new Webflow types from `src/lib/integrations/index.ts`

## Implementation Approach

The Webflow webhook handler mirrors the existing Tally lead magnet webhook but handles Webflow's form payload format. Both endpoints use the same downstream processing:
1. Store lead in Supabase
2. Add contact to Loops mailing list
3. Trigger welcome email flow
4. Send transactional lead magnet email
5. Forward to Clay for ICP enrichment

## Testing

- ✅ TypeScript compilation passes
- ✅ All 41 unit tests passing
- ✅ Verified Webflow API connection with provided API key
- ✅ Discovered site ID (`69406978199aa8bd306d4aff`) and existing collections via API
- ✅ Listed existing Webflow forms on the site

## Configuration

To use Webflow forms instead of Tally:
1. Set `WEBFLOW_API_KEY`, `WEBFLOW_SITE_ID`, `WEBFLOW_DOMAIN` in `.env.local`
2. Set `LEAD_MAGNET_FORM_PROVIDER=webflow`
3. Configure Webflow form webhook to point to `/api/webhooks/webflow?tenant_id=YOUR_TENANT_ID`
4. Add hidden fields to forms: `campaign_id`, `lead_magnet_slug`

## Linear Issue

[MOD-33: webflow connection](https://linear.app/modern-agency-sales/issue/MOD-33/webflow-connection)
