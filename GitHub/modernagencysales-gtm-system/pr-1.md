---
type: pr
source: github
date: 2026-01-28
url: https://github.com/modernagencysales/gtm-system/pull/1
title: "Run database migrations for production"
repo: modernagencysales/gtm-system
number: 1
state: open
author: kimprobably
---

# Run database migrations for production

PR #1 in modernagencysales/gtm-system — open — by kimprobably — [https://github.com/modernagencysales/gtm-system/pull/1](https://github.com/modernagencysales/gtm-system/pull/1)

## Summary

Creates comprehensive database migrations for the production Supabase instance, enabling all required tables and extensions for the GTM Automation System.

## Changes

### New Migration Files
- **`20260122_000_core_tables.sql`** - Core tables migration (337 lines)
  - Creates 8 core tables: `tenants`, `credentials`, `installations`, `campaigns`, `leads`, `events`, `metrics`, `system_health`
  - Enables pgvector extension for embedding similarity search
  - Configures RLS policies for multi-tenant isolation
  - Creates 22 indexes for query performance
  - Adds updated_at triggers for all tables
  - Seeds a demo tenant for testing

- **`20260122_001_lead_magnet_jobs.sql`** - Lead magnet job queue (37 lines)
  - Creates `lead_magnet_jobs` table for async job processing
  - Includes indexes and RLS policies

### New Utilities
- **`scripts/run-migrations.ts`** - CLI migration runner (276 lines)
  - Runs migrations in correct dependency order
  - Supports `--dry-run` and `--file` flags
  - Checks existing tables before running
  - Provides manual execution instructions

- **`src/app/api/database/migrate/route.ts`** - Migration API endpoint (427 lines)
  - GET: Returns migration status with list of all 23 required tables
  - POST: Provides SQL for manual migration with step-by-step instructions

## Migration Order

Migrations must be run in this order due to foreign key dependencies:

1. `20260122_000_core_tables.sql` (core tables)
2. `20250111_content_pipeline_tables.sql` (content pipeline)
3. `20250110_lead_magnet_tables.sql` (lead magnet deployments/optins)
4. `20260122_001_lead_magnet_jobs.sql` (job queue)
5. `20250111_scraped_linkedin_posts.sql` (scraped posts)
6. `20250111_add_new_idea_fields.sql` (content_ideas alterations)
7. `20260111_add_content_pillars.sql` (content_ideas alterations)
8. `20260111_idea_embeddings.sql` (vector embeddings)
9. `20260111_business_context.sql` (business context)
10. `20260111_viral_posts.sql` (viral posts + scrape_runs)
11. `20260111_week_plans.sql` (weekly scheduling)

## Test Plan

- [ ] Run migrations against staging Supabase instance
- [ ] Verify all 23 tables are created
- [ ] Check pgvector extension is enabled (`SELECT * FROM pg_extension WHERE extname = 'vector'`)
- [ ] Test RLS policies are active
- [ ] Verify demo tenant is seeded
- [ ] Test API endpoint at `/api/database/migrate` returns correct status

## How to Run Migrations

**Option 1: Supabase CLI**
```bash
supabase db push
```

**Option 2: SQL Editor**
1. Go to Supabase Dashboard > SQL Editor
2. Run each migration file in order

**Option 3: Direct PostgreSQL**
```bash
psql $DATABASE_URL -f supabase/migrations/20260122_000_core_tables.sql
```

**Option 4: API Endpoint**
```bash
# Check status
curl https://your-app.com/api/database/migrate

# Get migration SQL
curl -X POST https://your-app.com/api/database/migrate
```

## Related

- Linear Issue: [MOD-18](https://linear.app/modern-agency-sales/issue/MOD-18/run-database-migrations-for-production)

---

🤖 Generated with [Claude Code](https://claude.com/claude-code)
