# JobTrackr In-Progress Test Cases — 2026-04-19

## Purpose
Test cases for active development work in `DEVELOPMENT_PLAN.md`, with emphasis on the current kickoff slice and the newly locked PM semantics.

## Source tasks covered
- T-001: Finalize PM decision alignment in JobTrackr API contract and debug semantics
- T-002: Build local web app for Gmail inbox job search, protected shell, and raw source-email ingestion
- T-003: Produce JobTrakr dependency map and milestone sequencing pass for parallel-safe delivery
- T-004: Lock jobs dashboard contract for match rating, flagged semantics, and table payload shape
- T-005: Scaffold protected jobs dashboard web shell with mock table data for the first vertical slice
- T-006: Implement auto-close logic for jobs with match score below 60
- T-007: Implement view all jobs page with full job details and filtering
- T-008: Implement job details page to view full job information
- T-009: Polish jobs table interaction states and shared dashboard UX for the mock-data frontend slice

---

## T-001 Contract Alignment Test Cases

### TC-001 Status model excludes archived
**Precondition:** API contract and implementation-facing specs updated to PM memo semantics.

**Steps**
1. Review documented status enum in API contract and related specs.
2. Attempt to identify any endpoint, example, or enum that still includes `archived` as a workflow status.
3. Review documented list/view semantics for inbox, all jobs, saved, and archived.

**Expected**
- Allowed workflow statuses are only `new`, `interested`, `applied`, `interviewing`, `offer`, `rejected`.
- `archived` is absent from workflow status enums.
- Archive behavior is defined only via `archivedAt`.
- Saved behavior is defined only via `saved: boolean`.

### TC-002 Search and filter semantics are canonical and testable
**Steps**
1. Review list endpoint request semantics and examples.
2. Confirm keyword search scope.
3. Confirm AND-across-filter families and OR-within-repeated-values behavior.
4. Confirm default sort and date boundary semantics.

**Expected**
- Search only covers `title`, `company`, and `descriptionSnippet`.
- Filters combine with AND logic.
- Repeated params inside a filter family combine with OR logic.
- `dateFrom` and `dateTo` are inclusive.
- Default sort is `dateReceived:desc`.

### TC-003 Gmail connection state contract matches PM decision
**Steps**
1. Review Gmail connection/account response shape.
2. Confirm supported state values and reconnect semantics.
3. Confirm readonly Gmail scope in specs.

**Expected**
- Response exposes `connected`, `needsReconnect`, `state`, `lastSyncedAt`, `lastErrorCode`, `lastErrorMessage`.
- Allowed `state` values are `connected`, `disconnected`, `expired`, `revoked`, `denied`.
- `needsReconnect` is true for `expired` and `revoked`.
- OAuth scope remains `gmail.readonly` only for MVP.

### TC-004 One source email can link to many jobs
**Steps**
1. Review data-model and debug-linkage documentation.
2. Inspect whether linkage is modeled as one-to-many or many-to-many.

**Expected**
- Specs explicitly allow one source email to link to multiple jobs.
- Linkage model supports durable source-to-job traceability.

---

## T-002 Web App and Ingestion Kickoff Slice Test Cases

### TC-101 Login page is reachable
**Steps**
1. Open `/login` as an unauthenticated user.

**Expected**
- Login page renders successfully.
- Gmail/Google sign-in affordance is visible.

### TC-102 Unauthenticated user is blocked from dashboard
**Steps**
1. Open `/dashboard` without an authenticated session.

**Expected**
- User is redirected or denied access consistently.
- Protected content is not visible before auth completes.

### TC-103 Successful Google sign-in establishes session
**Steps**
1. Start Google sign-in from `/login`.
2. Complete consent with the required scopes.
3. Return to the app.

**Expected**
- Session is created.
- User lands in authenticated experience.
- User record is created or loaded successfully.

### TC-104 Dashboard shell renders with required table columns
**Precondition:** Authenticated session exists.

**Steps**
1. Open `/dashboard`.
2. Inspect visible jobs table columns.

**Expected**
- Table includes `Fit`, `Company`, `Title`, `Location`, `Source`, `Date Found`, and `Status`.
- Table renders mock data if live data is not yet wired.

### TC-105 Fit column supports numeric score plus flagged state
**Steps**
1. Review dashboard fixtures or rendered rows.
2. Compare rows with flagged, unflagged, and pending fit states.

**Expected**
- Fit cell can display match rating from 0 to 100.
- Flagged state is visually distinct.
- Pending or unavailable fit state does not show a misleading numeric value.

### TC-106 Empty state is safe and actionable
**Steps**
1. Render dashboard with zero jobs.
2. Render dashboard with filters that return no rows.

**Expected**
- No-jobs state explains how jobs will appear.
- No-results state distinguishes filter mismatch from missing data.
- Clear recovery action is present.

### TC-107 Logout clears session
**Steps**
1. Start from authenticated dashboard.
2. Trigger logout.
3. Attempt to revisit `/dashboard`.

**Expected**
- Session is cleared.
- User cannot access protected dashboard after logout.

### TC-108 Gmail initial sync defaults are honored
**Steps**
1. Trigger initial sync for a newly connected account.
2. Inspect sync request/response and resulting source-email coverage.

**Expected**
- Initial sync targets the last 30 days by default.
- Response is accepted/running rather than blocking to completion.
- Clearly irrelevant mail is not persisted by default.

### TC-109 Below-threshold emails persist as source-email-only records
**Steps**
1. Process a relevant but weakly structured job email that fails parsed-job thresholds.
2. Inspect persisted outcomes.

**Expected**
- Source email is retained.
- No junk job row is created.
- Extraction outcome is available for debugging.

---

## T-003 Dependency and Sequencing QA Checks

### TC-201 First slice is independently testable
**Steps**
1. Review milestone sequencing and kickoff slice definition.
2. Verify first slice does not depend on fit analysis or resume upload.

**Expected**
- Auth and protected shell can be validated without downstream fit-analysis completion.
- Early dashboard behavior remains testable with mock or fixture data.

### TC-202 Parallel lane contracts are frozen before integration leaves mocks
**Steps**
1. Review dependency-map blocker list.
2. Check whether schema/state model, dashboard query contract, and Gmail connection state contract are explicitly called out as hardening prerequisites.

**Expected**
- Parallel implementation safety depends on canonical contracts first.
- QA can identify when UI integration should remain mocked versus when real-data validation can begin.

---

## T-004 Jobs Dashboard Contract Test Cases

### TC-301 Table payload supports fit tri-state
**Steps**
1. Review dashboard/table response contract or fixture model.
2. Validate representation for rated-flagged, rated-not-flagged, and pending/unavailable fit cases.

**Expected**
- Contract supports numeric score and flagged state together.
- Contract supports null or equivalent pending state when fit is unavailable.

### TC-302 Required table fields are present and sortable where promised
**Steps**
1. Review dashboard payload and UI spec.
2. Validate supported sorts.

**Expected**
- Required fields exist for fit, company, title, location, source, date found, and status.
- Sorting support is defined for date found, company, status, and match rating.
- Default sort is date found descending.

### TC-303 Row interaction is unambiguous
**Steps**
1. Review dashboard table interaction spec.
2. Confirm whether row click, title click, or both open detail.

**Expected**
- One interaction pattern is chosen and documented.
- QA can validate detail access consistently.

### TC-304 Accessibility basics are testable
**Steps**
1. Review component acceptance expectations.
2. Validate keyboard navigation and visible labels requirements are explicitly documented.

**Expected**
- Table keyboard navigation is required.
- Interactive controls have visible labels.
- Status and fit are not color-only signals.

---

## T-005 Dashboard Shell Test Cases

### TC-401 Authenticated user lands on protected jobs dashboard shell
**Precondition:** Valid authenticated session exists.

**Steps**
1. Open the dashboard route after login.
2. Verify the user is not redirected away.
3. Inspect the initial shell before real API data is wired.

**Expected**
- Authenticated user reaches the dashboard shell.
- Core layout is visible with page header, jobs area, and account/logout controls.
- Mock data is allowed, but the protected shell must render without console-visible auth errors.

### TC-402 Mock jobs table renders required visible columns
**Steps**
1. Open the mock-data dashboard state.
2. Inspect the table header and first page of rows.

**Expected**
- Table shows the required dashboard columns promised for the current slice.
- Mock rows populate consistently enough for layout and interaction testing.
- Column labeling is stable and human-readable.

### TC-403 Dashboard shell supports loading and empty states
**Steps**
1. Simulate a loading state.
2. Simulate zero jobs.
3. Simulate a filter result with no rows.

**Expected**
- Loading state is visually distinct from empty state.
- Empty state explains how jobs appear in the app.
- No-results state offers a clear filter-reset or recovery action.

### TC-404 Dashboard shell is responsive on mobile and tablet breakpoints
**Steps**
1. Render the dashboard at desktop, tablet, and mobile widths.
2. Verify table overflow, stacking, or scroll behavior.

**Expected**
- Layout remains usable on mobile and tablet.
- Critical controls are still reachable.
- Table content does not become unreadable or irrecoverably clipped.

---

## T-006 Auto-Close Logic Test Cases

### TC-501 Auto-close transitions eligible low-fit jobs to `not a match`
**Steps**
1. Create or simulate jobs in `new`, `flagged`, and `reviewing` states.
2. Write fit results below 60.
3. Observe resulting job status and audit trail.

**Expected**
- Eligible triage-state jobs change to `not a match`.
- A status-history entry is created once per actual transition.
- Structured logging records the auto-close action.

### TC-502 Auto-close does not trigger for score 60 or above
**Steps**
1. Analyze jobs with scores of 60 and 61.
2. Inspect status, history, and logs.

**Expected**
- Status remains unchanged.
- No auto-close history row is added.
- Log path records skipped-above-threshold behavior.

### TC-503 Null fit scores do not change workflow state
**Steps**
1. Run fit analysis with `fit_score = null`.
2. Inspect job status and logs.

**Expected**
- Job status remains unchanged.
- No auto-close history row is written.
- Skip reason is logged as null-score behavior.

### TC-504 Manual or user-advanced states are protected from auto-close overwrite
**Steps**
1. Set jobs to `skipped`, `applied`, `interview`, `rejected`, and `offer`.
2. Re-run analysis with a score below 60.

**Expected**
- Protected statuses remain unchanged.
- Logs capture protected-state skip behavior.
- No duplicate or destructive transition occurs.

### TC-505 QA blocker: auto-close spec conflicts with PM canonical workflow model
**Steps**
1. Compare `jobtrackr-auto-close-logic-spec-v1.md` with the PM decision memo and current API contract.
2. Inspect whether `flagged`, `reviewing`, `skipped`, `interview`, and `not a match` are still canonical workflow statuses.

**Expected**
- Any mismatch is treated as a release blocker for implementation sign-off.
- QA requires one canonical status model before implementation is approved.

---

## T-007 View All Jobs Page Test Cases

### TC-601 View-all page lists persisted jobs with full-page browsing controls
**Steps**
1. Open the all-jobs page with seeded job data.
2. Inspect the list layout, pagination controls, and default ordering.

**Expected**
- Page renders more than the limited dashboard preview state.
- Pagination or equivalent page navigation is present.
- Default sort is documented and behaves consistently.

### TC-602 Filtering works across status, fit, date, and source inputs
**Steps**
1. Apply one filter at a time.
2. Apply combined filters.
3. Clear filters.

**Expected**
- Single filters narrow results correctly.
- Combined filters respect AND-across-filter-family behavior.
- Clear/reset returns the default list view.

### TC-603 Search returns matching jobs across supported fields only
**Steps**
1. Search by title, company, and description snippet text.
2. Search by notes and tags to verify unsupported-field handling.

**Expected**
- Matches are returned for supported search fields only.
- Notes and tags are not silently included if still out of scope for MVP.
- Empty search results are handled cleanly.

### TC-604 Pagination preserves current filter and search state
**Steps**
1. Apply filters and search text.
2. Navigate across pages.
3. Return to a prior page.

**Expected**
- Active filter/search state is preserved during pagination.
- Back/forward navigation does not lose the browsing context unexpectedly.

---

## T-008 Job Details Page Test Cases

### TC-701 Job details page displays all required fields
**Steps**
1. Open a job details page from the list or dashboard.
2. Inspect visible fields and metadata.

**Expected**
- Page shows description, skills, tags, notes, and source link.
- Core metadata like title, company, location, status, and fit are visible.
- Missing optional data is handled without broken layout.

### TC-702 Source link opens safely and predictably
**Steps**
1. Click the source/job link from the details page.
2. Observe browser behavior.

**Expected**
- Link opens in a new tab or otherwise follows the locked product behavior.
- The details page remains intact in the original session.

### TC-703 Notes can be edited and persisted without clobbering unrelated fields
**Steps**
1. Update the notes field.
2. Save changes.
3. Refresh the page.

**Expected**
- Notes persist after refresh.
- Unrelated fields are unchanged.
- Validation or error feedback is shown if save fails.

### TC-704 Tags can be added and removed cleanly
**Steps**
1. Add a new tag.
2. Remove an existing tag.
3. Refresh the page.

**Expected**
- Added tags persist.
- Removed tags stay removed.
- Duplicate tags are prevented or normalized.

---

## T-009 Dashboard Table Polish Test Cases

### TC-801 Row selection and detail affordance are obvious
**Steps**
1. Hover, focus, and click on table rows and title links.
2. Verify selected-row treatment.

**Expected**
- Selected row state is visually obvious.
- Users can tell what is clickable.
- The interaction does not rely on color alone.

### TC-802 Dashboard and jobs views share consistent empty and loading shells
**Steps**
1. Compare dashboard and jobs-page loading states.
2. Compare dashboard and jobs-page empty states.

**Expected**
- Loading placeholders follow the same visual/system pattern.
- Empty states feel consistent across views.
- Copy differences are purposeful, not accidental drift.

### TC-803 Dashboard-to-jobs navigation is smooth and state-aware
**Steps**
1. Navigate from the dashboard summary view to the full jobs view.
2. Observe whether relevant context carries over.

**Expected**
- Navigation is obvious and not jarring.
- If filters or selected context are meant to carry over, they do so predictably.
- No dead-end navigation path exists.

### TC-804 Keyboard and focus behavior remain usable after polish changes
**Steps**
1. Navigate the table and controls with keyboard only.
2. Trigger row/detail actions without a mouse.

**Expected**
- Focus states are visible.
- Keyboard navigation still works after visual polish changes.
- Interactive table affordances remain accessible.

---

## Current QA coverage gaps
1. No completed tasks are listed yet in `DEVELOPMENT_PLAN.md`, so this hour remains spec and acceptance-coverage work rather than execution validation.
2. T-006 is blocked by status-model drift: the auto-close spec still uses `flagged`, `reviewing`, `skipped`, `interview`, and `not a match`, while the PM memo/API contract currently define `interested` and `interviewing` and do not include `not a match` as a canonical workflow state.
3. `DEVELOPMENT_PLAN.md` still says T-002 is a Go web app, while the PM memo and engineering handoff now point implementation toward TypeScript, so QA cannot lock environment-specific execution tests until engineering direction is reconciled.
4. The table UI spec still leaves row-click behavior as a recommendation, not a locked decision, which blocks deterministic interaction testing for T-005 and T-009.
5. Real API payload examples for the jobs table and job details page are still needed before fixture-based UI checks can graduate into integration tests for T-007 and T-008.
6. Gmail connection-state edge cases like `expired`, `revoked`, and `denied` still need explicit acceptance coverage in the first runnable slice before QA can sign off on auth-plus-ingestion behavior.
