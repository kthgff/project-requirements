# JobTrakr Table UI Spec v1

## Purpose

Define the MVP table UI for reviewing tracked jobs in JobTrakr.

## Goal

Allow the user to scan all tracked jobs quickly, identify flagged opportunities, and update workflow status with minimal friction.

## Table Overview

The jobs table is the primary dashboard component in MVP.

It should:
- display all tracked jobs for the authenticated user
- prioritize fast scanning
- highlight good-fit jobs
- support filtering and sorting
- provide access to full job detail

---

## Columns

### Required columns
1. `Fit`
2. `Company`
3. `Title`
4. `Location`
5. `Source`
6. `Date Found`
7. `Status`

### Optional future columns
- Salary
- Work Mode
- Last Seen

---

## Column Requirements

### Fit
- show both a numeric `match rating` from 0 to 100 and a simple flagged/not-flagged state
- support three fit availability states:
  - flagged or not flagged with a computed rating
  - analyzed but below flag threshold
  - unavailable or pending because resume or job data is incomplete
- numeric rating should be sortable
- flagged state should remain visually obvious for fast triage

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

### Sorting behavior
At minimum support sorting by:
- Date Found
- Company
- Status
- Match rating

Default sort:
- Date Found descending

### Filtering behavior
At minimum support:
- text search
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
Allow status update from detail drawer first.

Optional if cheap:
- inline status dropdown in table

Reason:
- keeps initial table simpler
- reduces accidental edits

---

## Visual Hierarchy

The table should make these things easy to notice first:
1. flagged jobs
2. title and company
3. current status
4. recency

### Recommended treatment
- flagged jobs use a badge or colored icon
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
- expose full content in detail view or tooltip

---

## Accessibility Requirements

- table must be keyboard navigable
- controls must have visible labels
- status and fit should not rely on color alone
- row and interactive states should have clear focus treatment

---

## Functional Requirements

### FR1. Primary jobs list
The dashboard shall present jobs in a structured table.

### FR2. Fit visibility
The table shall visually distinguish flagged jobs and display a numeric match rating when analysis exists.

### FR3. Row detail access
The user shall be able to open fuller job detail from the table.

### FR4. Search
The user shall be able to search across tracked jobs.

### FR5. Filtering
The user shall be able to filter by status, fit state, and source.

### FR6. Sorting
The user shall be able to sort by supported columns.

### FR7. Status visibility
The table shall show workflow status for every job.

### FR8. Status editing
The product shall support updating job status from the dashboard experience.

---

## Open Questions

- Should multi-select bulk actions exist in MVP, or later?
- Should row click and title click both open detail, or title only?
- Should flagged jobs float to top as an optional sort preset?
