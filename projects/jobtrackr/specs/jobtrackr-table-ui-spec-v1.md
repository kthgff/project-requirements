# JobTrakr Table UI Spec v1

## Purpose

Define the MVP table UI for reviewing tracked jobs in JobTrakr.

If this preserved table spec conflicts with the canonical detail-view or list-to-detail continuity docs, follow `specs/jobtrackr-detail-view-contract-2026-04-20.md` and `specs/jobtrackr-list-detail-contract-examples-2026-04-20.md`.

## Goal

Allow the user to scan all tracked jobs quickly, identify likely good-fit opportunities through fit signals, and update workflow status with minimal friction.

## Table Overview

The jobs table is the primary dashboard component in MVP.

It should:
- display all tracked jobs for the authenticated user from the database
- prioritize fast scanning
- highlight good-fit jobs
- support searching, filtering, and sorting
- provide access to full job detail

---

## Columns

### Required columns
1. `Fit`
2. `Match %`
3. `Company`
4. `Title`
5. `Location`
6. `Source`
7. `Date Found`
8. `Status`

### Optional future columns
- Salary
- Work Mode
- Last Seen

---

## Column Requirements

### Fit
- show a numeric `match rating` from 0 to 100 plus a simple fit-priority indicator derived from the canonical fit contract
- support three fit availability states:
  - analyzed with a computed rating and fit indicator
  - analyzed but below the fit-priority threshold
  - unavailable or pending because resume or job data is incomplete
- numeric rating should be sortable
- fit-priority state should remain visually obvious for fast triage
- fit treatment must not introduce or imply a workflow status

### Company
- display normalized company name
- support text truncation if needed

### Title
- display job title
- clicking title opens detail view

### Location
- display normalized location text
- may show `Remote` where appropriate

### Source
- display source platform, such as LinkedIn or Indeed

### Date Found
- display discovered date in readable format
- support sorting

### Status
- display current workflow status
- support status update interaction in MVP if feasible

---

## Table Interactions

### Row click behavior
PM recommendation:
- clicking a row or title opens a right-side detail drawer
- the selected state is keyed by job id and should stay aligned with the canonical list-to-detail continuity examples
- mobile may switch to a full-page detail route, but section order and edit behavior must remain the same as the drawer

### Sorting behavior
At minimum support sorting by:
- Match %
- Date Found
- Company
- Status
- Match rating

Default sort:
- Date Found descending

### Filtering behavior
At minimum support:
- text search across database-backed job records
- status filter
- fit filter
- source filter
- optional match-rating sort without needing a separate filter in MVP

### Pagination or virtualization
PM recommendation:
- simple pagination is acceptable for MVP
- only introduce virtualization if performance requires it

---

## Status Interaction

### MVP recommendation
Allow status update from the shared detail experience first.

Optional if cheap:
- inline status dropdown in table, but only if it stays behaviorally aligned with the canonical detail contract

Reason:
- keeps initial table simpler
- reduces accidental edits
- preserves one clear source of truth for notes, tags, and detail editing behavior

---

## Visual Hierarchy

The table should make these things easy to notice first:
1. fit indicators and match percentage
2. title and company
3. current status
4. recency

### Recommended treatment
- fit-priority jobs use a badge or colored icon
- match rating appears as a compact score, such as `84/100`, near the fit state
- title text should be visually stronger than metadata
- muted styling for lower-priority metadata like source/date

---

## Empty and Edge States

### No jobs yet
Show:
- explanation that jobs will appear after inbox scanning
- clear next step if Gmail or resume setup is incomplete

### No filter matches
Show:
- message that no jobs match current filters
- clear filters action

### Missing fit data
Show:
- neutral placeholder such as `Pending` or `No resume`
- no misleading numeric score when analysis has not run yet

### Long text
- truncate in cells
- expose full content in the canonical detail experience rather than inventing table-only interaction patterns

---

## Accessibility Requirements

- table must be keyboard navigable
- controls must have visible labels
- status and fit should not rely on color alone
- row and interactive states should have clear focus treatment

---

## Functional Requirements

### FR1. Primary jobs list
The dashboard shall present database-backed jobs in a structured table.

### FR2. Fit visibility
The table shall visually distinguish stronger-fit jobs through fit indicators and display a numeric match rating when analysis exists.

### FR3. Row detail access
The user shall be able to open fuller job detail from the table.

### FR5. Search
The user shall be able to search across tracked jobs stored in the database.

### FR6. Filtering
The user shall be able to filter by status, fit state, and source.

### FR7. Sorting
The user shall be able to sort by supported columns, including match percentage.

### FR8. Status visibility
The table shall show workflow status for every job.

### FR9. Status editing
The product shall support updating job status from the dashboard experience through the shared detail surface first, with any table-side shortcut treated as secondary.

### FR10. Detail continuity
The table shall preserve canonical list-to-detail continuity for selected-row state, drawer versus full-page parity, and notes/tag editing handoff.

---

## Open Questions

- Should multi-select bulk actions exist in MVP, or later?
- Should row click and title click both open detail, or title only?
- Should stronger-fit jobs float to top as an optional sort preset?

## Canonical continuity note

Use `specs/jobtrackr-list-detail-contract-examples-2026-04-20.md` as the authoritative source for selected-row continuity, drawer versus route parity, and the way table rows hand off to status, notes, and tag editing in the detail experience.
