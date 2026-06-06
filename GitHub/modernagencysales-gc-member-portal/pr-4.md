---
type: pr
source: github
date: 2026-01-26
url: https://github.com/modernagencysales/gc-member-portal/pull/4
title: "Add LMS admin portal components for cohort and curriculum management"
repo: modernagencysales/gc-member-portal
number: 4
state: closed
author: cyrusagent[bot]
---

# Add LMS admin portal components for cohort and curriculum management

PR #4 in modernagencysales/gc-member-portal — closed — by cyrusagent[bot] — [https://github.com/modernagencysales/gc-member-portal/pull/4](https://github.com/modernagencysales/gc-member-portal/pull/4)

## Summary

Adds the missing `components/admin/lms/` directory with all admin portal components that were developed locally but never committed to the PR. This completes the admin UI portion of the LMS migration from Airtable to Supabase.

## Changes

### Layout Components
- **AdminLmsLayout.tsx** - Main layout wrapper with admin authentication check
- **AdminLmsSidebar.tsx** - Navigation sidebar for LMS admin sections

### Cohorts Management (`cohorts/`)
- **AdminLmsCohortsPage.tsx** - Cohorts list page with CRUD operations
- **LmsCohortModal.tsx** - Modal for creating/editing cohorts
- **LmsDuplicateCohortModal.tsx** - Modal for duplicating cohorts with full curriculum

### Curriculum Editor (`curriculum/`)
- **AdminLmsCurriculumPage.tsx** - Main curriculum editor page
- **WeekEditor.tsx** - Week component with expandable lessons and action items
- **LmsWeekModal.tsx** - Modal for creating/editing weeks
- **LmsLessonModal.tsx** - Modal for creating/editing lessons
- **LmsContentItemModal.tsx** - Modal for creating/editing content items (videos, slides, guides, etc.)
- **LmsActionItemModal.tsx** - Modal for creating/editing action items

## Testing

- ✅ TypeScript: 0 errors
- ✅ Lint: 0 errors (19 pre-existing warnings)
- ✅ Tests: 32 passed, 5 skipped, 0 failed

## Linear Issue

[MOD-57](https://linear.app/modern-agency-sales/issue/MOD-57/fix-the-pr)

---

🤖 Generated with [Claude Code](https://claude.ai/code)
