---
type: pr
source: github
date: 2026-01-28
url: https://github.com/modernagencysales/gtm-system/pull/2
title: "MOD-25: Add database migrations for AI chat system"
repo: modernagencysales/gtm-system
number: 2
state: open
author: kimprobably
---

# MOD-25: Add database migrations for AI chat system

PR #2 in modernagencysales/gtm-system — open — by kimprobably — [https://github.com/modernagencysales/gtm-system/pull/2](https://github.com/modernagencysales/gtm-system/pull/2)

## Summary
Adds SQL migration files for the AI chat system database tables and seeds the initial AI tools.

## Changes
- **Migration 1**: `supabase/migrations/20260122025325_ai_chat_tables.sql`
  - Creates `ai_tools` table for storing available AI assistant tools with configuration and parameter schemas
  - Creates `chat_conversations` table for user chat sessions with metadata and message counts
  - Creates `chat_messages` table for individual messages with support for tool calls
  - Includes Row Level Security policies
  - Adds triggers for `updated_at` timestamps and automatic message count updates

- **Migration 2**: `supabase/migrations/20260122025337_seed_ai_tools.sql`
  - Seeds 8 AI tools:
    1. `generate_content` - Content generation for LinkedIn posts, DMs, emails
    2. `search_leads` - Search and filter leads by various criteria
    3. `get_analytics` - Retrieve metrics and analytics data
    4. `schedule_post` - Schedule LinkedIn posts with LeadShark automation
    5. `enrich_lead` - Enrich leads using Clay integration
    6. `create_campaign` - Create new lead generation campaigns
    7. `check_integrations` - Check status of connected services
    8. `extract_ideas` - Extract content ideas from transcripts

- **TypeScript Types**: Added `AITool`, `ChatConversation`, and `ChatMessage` interfaces to `src/lib/types/database.ts`

## Testing
- TypeScript type checking passes
- All 41 unit tests passing
- No new lint issues introduced

## Migration Instructions
To run these migrations in Supabase:
1. Go to Supabase Dashboard → SQL Editor
2. Copy and run `supabase/migrations/20260122025325_ai_chat_tables.sql`
3. Copy and run `supabase/migrations/20260122025337_seed_ai_tools.sql`
4. Verify tables created: `ai_tools`, `chat_conversations`, `chat_messages`

## Linear Issue
[MOD-25](https://linear.app/modern-agency-sales/issue/MOD-25/run-database-migrations-for-ai-chat-system)
