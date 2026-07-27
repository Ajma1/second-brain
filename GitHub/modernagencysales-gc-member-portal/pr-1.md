---
type: pr
source: github
date: 2026-01-22
url: https://github.com/modernagencysales/gc-member-portal/pull/1
title: "feat: Add bulk edit functionality for AI tools"
repo: modernagencysales/gc-member-portal
number: 1
state: closed
author: timkeeeeeen
---

# feat: Add bulk edit functionality for AI tools

PR #1 in modernagencysales/gc-member-portal — closed — by timkeeeeeen — [https://github.com/modernagencysales/gc-member-portal/pull/1](https://github.com/modernagencysales/gc-member-portal/pull/1)

## Summary

Adds the ability to select multiple AI tools at once and update their model and max tokens settings in bulk, addressing the need to avoid manually editing each tool individually.

- Add selection checkboxes to the AI tools table with "select all" functionality
- Add new BulkEditModal component for choosing which fields to update (model and/or max tokens)
- Add `bulkUpdateAITools` service function using Supabase's `.in()` filter
- Add `useBulkUpdateAIToolsMutation` hook with proper cache invalidation
- Add "Max Tokens" column to the AI tools table for better visibility

## Implementation

- **BulkEditModal**: A new modal component that allows users to toggle which fields they want to update (model, max tokens) and set the values. Only checked fields are included in the update.
- **Service Layer**: Added `bulkUpdateAITools()` function that uses Supabase's `.in('id', toolIds)` to update multiple records in a single query.
- **State Management**: Selection state is tracked using a `Set<string>` of tool IDs, with handlers for individual selection and select-all functionality.
- **UI/UX**: Selected rows are highlighted, and a purple "Edit X Tools" button appears when tools are selected.
- **Input Validation**: maxTokens input is clamped to valid range (256-64,000) and service guards against empty inputs.

## Test Plan

- [x] TypeScript compilation passes
- [x] All 32 tests pass
- [x] Production build succeeds
- [x] Linting passes (no errors)

## PR Review Feedback Addressed

- Increased max token limit from 4,096 to 64,000
- Added input clamping for maxTokens to valid range (256-64,000)
- Added guard for empty toolIds or empty updates in bulkUpdateAITools service
- Applied same fixes to AIToolModal for consistency

---

Closes [MOD-23](https://linear.app/modern-agency-sales/issue/MOD-23/ai-chat-update-all)
