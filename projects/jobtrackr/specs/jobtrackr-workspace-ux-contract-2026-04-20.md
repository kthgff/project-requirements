# JobTrakr Workspace UX Contract

## Purpose

Define one shared interaction contract for the mock jobs workspace work tied to T-005, T-009, and T-013 so Priya can implement active-filter context, empty-result recovery, and dashboard-to-jobs navigation without drifting across screens or breakpoints.

## Scope

This contract covers:
- dashboard shell and jobs workspace behavior with mock data
- filter-summary visibility after filters are applied
- empty-result and no-data recovery actions
- row selection, detail access, and return-to-dashboard behavior
- responsive expectations for desktop, tablet, and mobile

This contract does not lock final API payloads beyond the frontend states needed for mock-driven implementation.

## Goals

1. Make active filter state obvious without forcing users to reopen filter controls.
2. Prevent blank-table confusion when no jobs match.
3. Keep dashboard and jobs views feeling like one workspace, not disconnected pages.
4. Preserve scan context when moving between the table and a selected job.

## Canonical Workspace States

### 1. Default loaded state
- Jobs table is visible with rows.
- Filter controls are collapsed or inline, depending on breakpoint.
- Results summary shows total visible jobs and current sort.
- No recovery banner is shown.

### 2. Filtered state with matches
- Active filters appear in a persistent summary region above the table.
- Summary includes chips or badges for:
  - search query
  - status filters
  - source filters
  - location filter if set
- Each filter chip can be removed individually.
- A `Clear all filters` action is always visible in the summary region.
- Results copy should explain the narrowed set, for example: `Showing 8 jobs matching 3 filters`.

### 3. Filtered empty state
- Replace the table body with an empty-result panel, not a blank shell.
- Preserve the active-filter summary above the empty state.
- Empty-result panel must include:
  - headline explaining no jobs matched the current filters
  - one sentence pointing to the likely cause, such as over-narrow filtering
  - primary action: `Clear all filters`
  - secondary action: `Back to dashboard`
- If a search query is active, mention it explicitly in the empty-state copy.

### 4. No jobs yet state
- Used only when mock data is absent, not when filters caused zero matches.
- Messaging should explain that jobs will appear after inbox scan or seed data setup.
- Suggested action can point to setup or refresh, not filter reset.

### 5. Row selected state
- Selected row remains visually distinct in the table.
- Selection treatment must survive sort changes and non-destructive filter edits when the selected row still exists in the visible set.
- Opening detail should not fully disorient the user from the current table context.
- If a filter or search change removes the selected row from the visible result set, clear the active selection immediately and show helper copy explaining that the previously selected job no longer matches the current results.
- If the selected row becomes visible again after filters are relaxed within the same workspace session, restore the last selected row automatically on desktop and tablet, and restore it on mobile when returning from detail or filter controls.

## Filter Summary Contract

### Placement
- Desktop and tablet: directly below page header and above table controls.
- Mobile: directly below the primary page title, before the results list.

### Required content
- visible label such as `Active filters`
- chips for each active filter value
- total results count
- `Clear all filters` action

### Behavior rules
- Summary is hidden only when no filters are active.
- Removing a chip immediately updates results and the summary count.
- `Clear all filters` returns the workspace to its default loaded state and removes empty-result messaging.
- Summary wraps cleanly across lines on smaller screens.

## Empty-Result Recovery Contract

### Primary recovery
`Clear all filters`
- clears every active filter in one action
- returns to default jobs workspace state
- keeps user on the current page

### Secondary recovery
`Back to dashboard`
- returns the user to the top-level dashboard shell state
- preserves the fact that the jobs view was entered from dashboard navigation
- should not require browser back to recover

### Optional tertiary recovery
`Edit filters`
- allowed on mobile if filters are tucked behind a sheet or drawer
- opens the filter controls without clearing current values

## Dashboard to Jobs Navigation Contract

### From dashboard shell into jobs workspace
- primary jobs CTA enters the jobs workspace with the default sort and no filters unless a saved context is intentionally passed
- breadcrumb or back affordance should make the dashboard relationship obvious

### From jobs workspace back to dashboard
- user can return via explicit `Back to dashboard` action in the page chrome or empty state
- returning to dashboard should not silently discard workspace state unless the user used `Clear all filters`
- returning to dashboard should preserve the last workspace snapshot in session state: active filters, sort, visible count, and last selected job id if that job still exists in the underlying result set

### Re-entering jobs workspace
- if user returns within the same session, preserving prior filters and selected row is required for desktop/tablet when the selected row still matches the current result set
- on mobile, preserving filters is required, preserving selected row is required when returning from detail within the same session and preferred when returning from dashboard navigation if the selected job still exists cleanly in the list pattern
- if the last selected job no longer matches current filters on re-entry, keep filters intact, clear the selection, and show a small inline message near the results summary rather than restoring stale detail state

## Responsive Interaction Expectations

### Desktop
- filters may appear inline
- active-filter summary can sit on one or two rows
- selected row plus detail panel can coexist if a drawer is used

### Tablet
- keep summary visible without pushing the table too far below the fold
- detail interaction may use a narrower drawer or dedicated panel
- filter chips should wrap before truncating important values

### Mobile
- results should shift to cards or a compact stacked row pattern if the table becomes unreadable
- active-filter summary must remain visible above the results list after returning from filter controls
- empty-result actions must be full-width or otherwise thumb-friendly
- returning from job detail should land the user near the previously selected result

## Row-Selection Continuity Rules

### Preserve selection
- Sorting changes must preserve the selected job by id, even if its row position changes.
- Non-destructive filter edits must preserve the selected job by id when that job remains in the visible result set.
- Returning from detail to the list must scroll the user near the selected row or card.

### Clear selection
- Search or filter changes that remove the selected job from the visible result set must clear the active selection immediately.
- Clearing selection because a row disappeared must not reset active filters or sort.
- The workspace should show helper copy such as `Your previously selected job is hidden by the current filters.` with an optional secondary action to clear filters.

### Restore selection
- If the user relaxes filters and the most recently selected job becomes visible again during the same session, restore that selection automatically.
- Automatic restoration should only target the most recent selected job id, not an arbitrary nearby row.
- Using `Clear all filters` may restore the last selected row if it still exists in the full mock dataset; otherwise load the default unselected state.

## Visual and Content Guidance

### Active-filter summary copy
- preferred label: `Active filters`
- example helper copy: `Showing 4 jobs matching your current filters`

### Empty-result copy
- headline: `No jobs match these filters`
- body: `Try clearing one or more filters, or head back to the dashboard to start from the full list.`

### Selection treatment
- use border, background, or icon change in addition to color
- preserve accessibility contrast and focus visibility

## Accessibility Requirements

- filter chips must be keyboard reachable and removable without pointer-only affordances
- clear and back actions need descriptive labels
- empty-result messaging must be announced as content changes when filters update the table to zero results
- selected state cannot rely on color alone

## Implementation Notes for Mock-Driven Frontend Work

- Mock state should include enough metadata to render active filters, visible count, empty-result reason, and selected-row identity.
- Frontend stories for T-005, T-009, and T-013 should use the same state names from this contract to avoid duplicate logic.
- Recovery actions can be mock handlers for now, but labels and transitions should match this contract exactly enough for QA walkthroughs.

## Downstream Tasks Enabled

- T-005 protected jobs dashboard web shell
- T-009 table interaction and shared dashboard UX polish
- T-013 active-filter context and empty-result recovery controls
- T-023 canonical list-to-detail contract examples

Selection examples that bridge this workspace contract to the detail-view contract are documented in `specs/jobtrackr-list-detail-contract-examples-2026-04-20.md`.

## Open Questions

1. Should mobile use a card list for all job rows, or only below a specific width threshold?
2. Should `Back to dashboard` preserve the last active summary card on the dashboard, or simply return to the top of the page?
