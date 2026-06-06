---
type: pr
source: github
date: 2026-01-26
url: https://github.com/modernagencysales/gtm-system/pull/3
title: "feat: Add drag-and-drop auto-scheduling to content pipeline kanban"
repo: modernagencysales/gtm-system
number: 3
state: closed
author: kimprobably
---

# feat: Add drag-and-drop auto-scheduling to content pipeline kanban

PR #3 in modernagencysales/gtm-system — closed — by kimprobably — [https://github.com/modernagencysales/gtm-system/pull/3](https://github.com/modernagencysales/gtm-system/pull/3)

## Summary

Implements drag-and-drop functionality for the content pipeline kanban board with automatic scheduling. Users can now drag posts from the "Written" column to the "Scheduled" column, and the system will automatically schedule them for the next available working day (Monday-Friday) that doesn't have any posts already scheduled.

## Changes

### New Files
- `src/lib/utils/scheduling.ts` - Scheduling utility functions for working day calculations
- `src/test/scheduling.test.ts` - 21 unit tests for scheduling logic

### Modified Files
- `src/components/content-pipeline/kanban/KanbanCard.tsx` - Added draggable support with HTML5 drag events
- `src/components/content-pipeline/kanban/KanbanColumn.tsx` - Added drop zone handling with visual feedback
- `src/components/content-pipeline/kanban/KanbanBoard.tsx` - Added drag-and-drop orchestration logic
- `src/components/content-pipeline/kanban/index.ts` - Export DragData type
- `src/components/content-pipeline/ContentPipelinePage.tsx` - Added auto-schedule and move post handlers

## Implementation Details

1. **Drag-and-Drop**: Uses native HTML5 drag-and-drop API (no external libraries needed)
2. **Auto-Scheduling Logic**:
   - Finds the next working day (Mon-Fri) starting from tomorrow
   - Skips days that already have scheduled posts
   - Schedules posts for 9:00 AM by default
3. **Visual Feedback**:
   - Cards show reduced opacity when being dragged
   - Columns highlight with a blue ring when a draggable item hovers over them
   - "Auto-schedule" indicator appears when hovering over the Scheduled column

## Testing

- 21 unit tests added for scheduling utilities (all passing)
- 62 total tests passing
- TypeScript compilation clean
- ESLint clean on modified files

## Linear Issue

[MOD-31](https://linear.app/modern-agency-sales/issue/MOD-31/content-pipeline-kanban)
