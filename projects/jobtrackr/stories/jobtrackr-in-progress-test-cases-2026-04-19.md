# JobTrackr In-Progress Test Cases — 2026-04-20

## Purpose
Test cases for work currently marked `in-progress` in `projects/jobtrackr/DEVELOPMENT_PLAN.md`, updated after Jimmy's latest sequencing pass and the Milestones 1 to 4 engineering ticket breakdown.

## Source tasks covered
- T-001: Finalize PM decision alignment in JobTrackr API contract and debug semantics
- T-002: Build local Go web app for Gmail inbox job search, protected shell, and raw source-email ingestion
- T-003: Produce JobTrakr dependency map and milestone sequencing pass for parallel-safe delivery
- T-004: Lock jobs dashboard contract for match rating, flagged semantics, and table payload shape
- T-005: Scaffold protected jobs dashboard web shell with mock table data for the first vertical slice
- T-006: Implement auto-close logic for jobs with match score below 60
- T-007: Implement view all jobs page with full job details and filtering
- T-008: Implement job details page to view full job information
- T-009: Polish jobs table interaction states and shared dashboard UX for the mock-data frontend slice
- T-010: Break the approved dependency sequencing into implementation-ready engineering tickets for Milestones 1 to 4
- T-011: Improve the responsive jobs list UX with mobile cards, clearer active filter context, and compact results navigation
- T-012: Add editable notes and tag management interactions to the job details page using the agreed detail-view contract
- T-013: Add visible active-filter context and empty-result recovery controls to the mock jobs workspace
- T-014: Break the finalized parser and ingestion contract changes into implementation-ready tickets after the jobs dashboard payload is locked
- T-015: Define the job detail view contract for field grouping, edit interactions, and API expectations across T-008 and T-012
- T-016: Define the mock jobs workspace UX contract for active-filter visibility, empty-result recovery, and dashboard-to-jobs navigation
- T-017: Close Gate A documentation by reconciling schema, workflow/archive semantics, fit nullability, and source-email linkage across canonical specs
- T-018: Implement the Google auth callback, session flow, and Gmail readonly connection path for the first end-to-end vertical slice
- T-019: Align the job details UI with the locked detail-view contract, including section order, editing affordances, and list/detail behavior examples
- T-020: Lock row-selection continuity rules for the mock jobs workspace across filter changes, sorting, and dashboard return flows
- T-021: Assemble the final Phase 3 engineering handoff package with source-of-truth links, dependency gates, owner mapping, and immediate pickup guidance
- T-022: Wire the authenticated frontend shell to the new auth session and Gmail connection endpoints for the first vertical slice
- T-023: Add canonical list-to-detail contract examples so the jobs workspace and detail UI stay aligned with the locked section order and edit model
- T-024: Add canonical workspace session-state examples for deterministic row-selection continuity across filters, sorting, and dashboard return flows
- T-025: Publish a canonical Gate A reconciliation matrix so engineering can resolve workflow/archive, fit nullability, and source-email linkage drift from one file
- T-026: Add persisted Gmail search results and first-pass server-side filtering endpoints for the inbox job search slice
- T-027: Canonicalize the list-to-detail example docs so API, frontend, and QA reference one authoritative detail continuity example set
- T-028: Add a canonical workspace continuity QA matrix for filter, sort, dashboard return, and responsive selection behavior
- T-029: Deliver the first authenticated persisted-jobs vertical slice by wiring session-backed Gmail search results into the jobs UI
- T-030: Harden the job details editing UI with contract-aligned save/cancel notes flow, in-context tag edits, and shared section structure across drawer and page views
- T-031: Implement deterministic row-selection continuity in the mock jobs workspace across filter changes, pagination, and dashboard return flows
- T-032: Rewrite stale Gate A docs so legacy workflow statuses and auto-close behavior no longer conflict with the canonical PM memo, API contract, and reconciliation matrix
- T-033: Reconcile DEVELOPMENT_PLAN ticket language with the canonical workflow, fit, and table-contract decisions so engineers stop inheriting stale implementation assumptions
- T-034: Audit and reconcile milestone and ticket wording in DEVELOPMENT_PLAN so canonical workflow, fit, and table semantics stay consistent after the ticket rewrite pass
- T-045: Reconcile PROJECT, milestone, and handoff references so the plan rewrite stays aligned outside the task table
- T-046: Reconcile PRD and QA test-case references so legacy workflow and auto-close language no longer conflicts with the canonical workflow and fit model
- T-047: Reconcile remaining product/spec references so legacy flagged and stale workflow language no longer conflicts with the canonical workflow and fit-signal model
- T-048: Reconcile parser and ingestion ticket wording so milestone follow-through docs stop implying deprecated auto-close or workflow-state behavior for low-fit jobs

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

### TC-105 Fit column supports numeric score plus fit-signal state
**Steps**
1. Review dashboard fixtures or rendered rows.
2. Compare rows with strong-fit, low-fit, and pending fit states.

**Expected**
- Fit cell can display match rating from 0 to 100.
- Fit-state treatment is visually distinct without implying workflow status.
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

## T-019 Detail UI Alignment Test Cases

### TC-1901 Detail uses locked section order in both drawer and route patterns
**Steps**
1. Review the detail-view contract and list-to-detail examples doc.
2. Compare drawer and full-page detail states.

**Expected**
- Both patterns use the same six-section order.
- The UI does not invent a separate edit screen for notes or tags.

### TC-1902 Status edit reflects back into the selected list row
**Steps**
1. Start from a selected job in the jobs workspace.
2. Change the job status in the detail surface.
3. Re-check the selected list row state.

**Expected**
- Status updates in the detail UI.
- The same selected job remains active.
- The list row reflects the updated status without losing workspace context.

### TC-1903 Hidden selected row clears and restores deterministically
**Steps**
1. Select a job from the list.
2. Apply filters that hide that job.
3. Relax filters so the same job becomes visible again.

**Expected**
- Active selection clears as soon as the selected row leaves the visible set.
- Helper copy explains why the detail state disappeared.
- The most recently selected job restores automatically when it becomes visible again in the same session.

## T-020 Row-Selection Continuity Test Cases

### TC-1910 Sorting preserves selection by job id
**Steps**
1. Select a job row in the workspace.
2. Change sort order one or more times.
3. Inspect the selected state after each sort.

**Expected**
- Selection persists by job id, not row index.
- The selected row stays visibly selected after sort changes.
- Returning from detail lands near the selected row.

### TC-1911 Dashboard return preserves workspace snapshot without stale selection
**Steps**
1. Enter the jobs workspace from dashboard.
2. Apply filters and select a job.
3. Return to dashboard, then re-enter jobs in the same session.

**Expected**
- Active filters and sort are preserved.
- Selection is restored only if the selected job still matches the active result set.
- If the job no longer matches, selection stays cleared and helper copy explains why.

## T-024 Workspace Session-State Example Test Cases

### TC-1920 Hidden selection keeps lastSelectedJobId for same-session restoration
**Steps**
1. Select a job in the workspace.
2. Apply filters that hide it.
3. Inspect the stored workspace session state.

**Expected**
- `selectedJobId` clears immediately.
- `lastSelectedJobId` still points to the hidden job.
- Helper mode is set to a hidden-selection state instead of picking a replacement row.

### TC-1921 Dashboard re-entry restores snapshot before selection logic runs
**Steps**
1. Enter the jobs workspace from dashboard.
2. Apply filters, pick a job, and return to dashboard.
3. Re-enter the jobs workspace in the same session.

**Expected**
- Filters and sort restore first.
- Selection restores only if the last selected job is visible in the restored result set.
- The workspace does not open stale detail for a hidden job.

### TC-1922 Mobile detail return preserves list context by job id
**Steps**
1. Open a job from the mobile jobs list.
2. Return from full-page detail to the list.
3. Compare selection and scroll behavior before and after the return.

**Expected**
- The list restores the same filters and sort.
- The user lands near the previously selected job when it still matches.
- If that job no longer matches, selection clears and helper copy appears.

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
2. Validate representation for rated-strong-fit, rated-low-fit, and pending/unavailable fit cases.

**Expected**
- Contract supports numeric score plus canonical fit-state signaling together.
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

### TC-501 Canonical low-fit handling does not invent a workflow transition
**Steps**
1. Create or simulate jobs in `new` and `interested` states.
2. Write fit results below 60.
3. Observe resulting job status, fit metadata, and audit trail.

**Expected**
- Workflow status remains unchanged.
- Low-fit treatment is represented through canonical fit metadata or helper signals, not a synthetic workflow status.
- Structured logging records the low-fit handling path.

### TC-502 Auto-close does not trigger for score 60 or above
**Steps**
1. Analyze jobs with scores of 60 and 61.
2. Inspect status, history, and logs.

**Expected**
- Status remains unchanged.
- No low-fit workflow mutation or synthetic status-history row is added.
- Log path records above-threshold behavior.

### TC-503 Null fit scores do not change workflow state
**Steps**
1. Run fit analysis with `fit_score = null`.
2. Inspect job status and logs.

**Expected**
- Job status remains unchanged.
- No auto-close history row is written.
- Skip reason is logged as null-score behavior.

### TC-504 Manual or user-advanced states are protected from low-fit overwrite
**Steps**
1. Set jobs to `applied`, `interviewing`, `rejected`, and `offer`.
2. Re-run analysis with a score below 60.

**Expected**
- Protected statuses remain unchanged.
- Logs capture protected-state handling behavior.
- No duplicate or destructive transition occurs.

### TC-505 QA blocker: low-fit implementation docs conflict with the canonical workflow model
**Steps**
1. Compare `jobtrackr-auto-close-logic-spec-v1.md` with the PM decision memo, reconciliation matrix, and current API contract.
2. Inspect whether `flagged`, `reviewing`, `skipped`, `interview`, and `not a match` are still presented as canonical workflow statuses or workflow outcomes.

**Expected**
- Any mismatch is treated as a release blocker for implementation sign-off.
- QA requires one canonical workflow status model plus one canonical low-fit handling model before implementation is approved.

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

## T-010 Milestone Ticket Breakdown QA Checks

### TC-901 Milestones 1 to 4 ticket breakdown preserves contract prerequisites
**Steps**
1. Review the implementation-ready ticket breakdown for Milestones 1 to 4.
2. Trace dependencies back to T-001 and T-004.
3. Verify auth, dashboard, persisted jobs, and Gmail ingestion work are sequenced without bypassing contract-lock prerequisites.

**Expected**
- Contract-dependent work is explicitly blocked on T-001 and T-004 where required.
- Tickets are small enough for engineering handoff and QA verification.
- Dependency notes make parallel execution safe rather than implied.

### TC-902 Ticket breakdown includes QA-verifiable acceptance criteria and handoffs
**Steps**
1. Inspect milestone tickets for acceptance criteria quality.
2. Check whether each ticket exposes QA entry points, test data needs, and handoff notes.

**Expected**
- Each ticket has measurable acceptance criteria.
- QA can tell what must be demonstrated before sign-off.
- Hidden prerequisites and ambiguous “done” language are absent.

---

## T-011 Responsive Jobs List UX Test Cases

### TC-1001 Mobile jobs view preserves essential job context
**Steps**
1. Open the jobs view at tablet and mobile breakpoints.
2. Inspect each responsive row or card.
3. Compare the compact layout to the desktop job list.

**Expected**
- Each item still exposes title, company, status, and fit context.
- A user can open the job detail view without relying on hover-only controls.
- Compact navigation does not hide the currently selected or active job context.

### TC-1002 Active filter context remains visible and removable on smaller screens
**Steps**
1. Apply multiple filters on the jobs page.
2. Inspect the active filter summary at desktop, tablet, and mobile widths.
3. Remove one filter, then clear all filters.

**Expected**
- Active filters remain visible without reopening every filter control.
- Individual filters can be removed directly from the summary state.
- The summary and results stay in sync after each change.

### TC-1003 Compact results navigation preserves browsing context
**Steps**
1. Apply filters and search text on the jobs page.
2. Move between result items using the compact navigation affordance.
3. Open a job detail view, then return to the list.

**Expected**
- Users can move through filtered results without losing place.
- Returning from details preserves list context.
- Compact navigation does not drop active filters or search state.

---

## T-012 Job Details Editing Test Cases

### TC-1101 Notes editing is inline, recoverable, and persistence-ready
**Steps**
1. Open a job details page with existing notes.
2. Edit the notes inline.
3. Save the change, then refresh the page.
4. Repeat with an empty notes field and with a simulated save failure.

**Expected**
- Notes can be edited without leaving the page.
- Successful saves persist after refresh.
- Empty-state notes are supported without breaking the layout.
- Failed saves surface clear error feedback and do not silently discard prior saved content.

### TC-1102 Tag add and remove controls are clear and prevent duplicate state
**Steps**
1. Add a new tag from the details page.
2. Add an already-present tag.
3. Remove a tag, then refresh the page.

**Expected**
- Add and remove controls are obvious and usable.
- Duplicate tags are prevented or normalized.
- The visible tag set remains consistent after refresh.

### TC-1103 Detail editing interactions match the agreed job-detail contract
**Steps**
1. Compare notes and tag interactions to `jobtrackr-api-contract.md` and the detail payload shape.
2. Verify the UI does not invent unsupported fields or save paths.

**Expected**
- Editing behavior maps to the documented detail contract.
- Notes update paths and tag-management flows are implementation-ready for API wiring.
- No UI state depends on fields that are absent from the agreed contract.

---

## T-013 Filter Context and Empty-Result Recovery Test Cases

### TC-1201 Active filters stay visible after apply
**Steps**
1. Apply search, status, and location filters in the jobs workspace.
2. Inspect the results area at desktop, tablet, and mobile widths.

**Expected**
- An `Active filters` summary appears above results.
- Each applied filter is visible as its own removable chip or badge.
- Total visible-result count updates with the applied filter state.

### TC-1202 Empty-result state explains recovery paths
**Steps**
1. Apply filters that produce zero matching jobs.
2. Inspect the empty-result messaging and available actions.

**Expected**
- The table body is replaced by an explicit empty-result panel, not a blank region.
- Primary recovery action is `Clear all filters`.
- Secondary recovery action is `Back to dashboard`.
- If search text is active, the empty-state copy references it.

### TC-1203 Filter reset and dashboard return do not confuse workspace state
**Steps**
1. From a filtered empty state, click `Clear all filters`.
2. Reapply filters, then click `Back to dashboard`.
3. Re-enter the jobs workspace.

**Expected**
- Clear-all returns the user to the default jobs workspace state.
- Back-to-dashboard is available without relying on browser back.
- Re-entering the jobs workspace preserves filters when the contract says that session context should survive.

---

## T-014 Parser and Ingestion Ticket Breakdown QA Checks

### TC-1301 Milestone 4 parser follow-through tickets are blocked on contract lock
**Steps**
1. Review the parser and ingestion ticket breakdown work for T-014.
2. Trace each ticket dependency back to T-001 and T-004.

**Expected**
- Parser and ingestion follow-through work does not bypass unresolved contract work.
- Dependencies explicitly mention dashboard payload and ingestion-contract prerequisites.
- Engineering can tell what must be frozen before implementation starts.

### TC-1302 Parser follow-through tickets expose QA-verifiable exit checks
**Steps**
1. Inspect each T-014 ticket for acceptance language.
2. Check whether ingestion logging, source-email persistence, extraction thresholds, and dedupe outcomes are testable.

**Expected**
- Ticket acceptance criteria are measurable.
- QA can verify source-email retention, dedupe-by-URL, and created-job outcomes without guessing.
- Hidden parser assumptions are called out instead of implied.

---

## T-015 Detail View Contract Test Cases

### TC-1401 Detail section order is locked and reusable
**Steps**
1. Review `jobtrackr-detail-view-contract-2026-04-20.md`.
2. Compare the defined section order with T-008 and T-012 expectations.

**Expected**
- Section order is explicitly documented as header summary, fit and workflow, job content, notes, tags, then source and activity context.
- T-008 and T-012 can build against one shared order instead of divergent layouts.

### TC-1402 Editable versus view-only fields are unambiguous
**Steps**
1. Review field-level interaction rules in the detail contract.
2. Verify whether status, notes, and tags are the only editable MVP fields.

**Expected**
- Editable fields are limited to `status`, `notes`, and `tags`.
- Fit fields, recruiter metadata, and source/activity context remain view-only.
- Notes use explicit `Save` and `Cancel`, not autosave.

### TC-1403 Detail payload example supports real implementation handoff
**Steps**
1. Inspect the example `GET /jobs/:id` payload in the detail contract.
2. Compare it with existing T-008 and T-012 test assumptions.

**Expected**
- Payload includes the fields needed for header, fit, notes, tags, and source/activity rendering.
- API write paths for notes and tags are documented clearly enough for frontend and QA alignment.
- Any missing field that blocks implementation is treated as a contract gap.

---

## T-016 Workspace UX Contract Test Cases

### TC-1501 Filter summary contract is explicit across breakpoints
**Steps**
1. Review `jobtrackr-workspace-ux-contract-2026-04-20.md`.
2. Inspect placement and behavior rules for desktop, tablet, and mobile.

**Expected**
- The active-filter summary placement is specified for each breakpoint.
- Summary is hidden only when no filters are active.
- Removing a chip updates results and summary count immediately.

### TC-1502 Empty-result recovery actions are standardized
**Steps**
1. Review the filtered-empty and no-jobs-yet states.
2. Compare required actions and copy guidance.

**Expected**
- Filtered-empty state and no-jobs-yet state are distinct.
- `Clear all filters` and `Back to dashboard` are required recovery actions for filtered-empty states.
- Empty-result messaging is testable and not left to ad hoc frontend copy.

### TC-1503 Selection and navigation rules preserve user context
**Steps**
1. Review the row-selected and dashboard-to-jobs navigation sections.
2. Check whether selection persistence and return paths are defined clearly enough for QA.

**Expected**
- Selected-row treatment must survive non-destructive changes when the row remains visible.
- Dashboard-to-jobs and back navigation are explicit and testable.
- Mobile return behavior preserves filter context, and selected-row preservation is defined for in-session return flows.

### TC-1504 Selection clears with guidance when filters hide the active row
**Steps**
1. Start from a workspace state with one selected job row.
2. Apply a search or filter that removes that job from the visible results.
3. Inspect the resulting list or empty-result state.

**Expected**
- Active selection is cleared immediately.
- Active filters and sort remain intact.
- Inline helper copy explains that the previously selected job is hidden by current filters.

### TC-1505 Selection restores when the same row becomes visible again in-session
**Steps**
1. Select a job row in the workspace.
2. Apply a filter that hides it, then relax or clear that filter during the same session.
3. Return to the list and inspect the row state.

**Expected**
- The most recently selected visible job is restored by id, not by row index.
- The list returns near the restored row or card.
- Restoration behavior is defined for desktop, tablet, and mobile return flows.

---

## T-017 Gate A Closeout Test Cases

### TC-1601 Workflow, saved, and archive semantics are consistent across canonical specs
**Steps**
1. Compare `jobtrackr-pm-decision-memo-2026-04-19.md`, `jobtrackr-api-contract.md`, `jobtrackr-database-schema.md`, and the Milestones 1 to 4 engineering tickets.
2. Check whether workflow enum values, `saved`, and `archivedAt` semantics agree.
3. Check whether any spec still treats archive as a workflow state.

**Expected**
- Canonical workflow enum is `new`, `interested`, `applied`, `interviewing`, `offer`, `rejected` in all Gate A docs.
- `saved` remains a separate boolean flag.
- `archivedAt` remains the only archive-state field.
- No Gate A document reintroduces `archived` as a status value.

### TC-1602 Fit nullability stays explicit before analysis exists
**Steps**
1. Review the PM memo, API contract, schema spec, and milestone tickets.
2. Compare how pre-analysis jobs represent fit data.

**Expected**
- Gate A docs consistently allow `fitFlag = null`, `fitScore = null`, and `fitSummary = null` before analysis runs.
- No Gate A document requires synthetic default fit values.
- Frontend-facing docs distinguish pending or unavailable fit from low-score fit.

### TC-1603 One-source-email-to-many-jobs linkage is preserved end to end
**Steps**
1. Review linkage modeling in schema, API/debug docs, and milestone tickets.
2. Check whether any doc still implies a single `job_id` on the source-email record as the only linkage path.

**Expected**
- Canonical linkage model supports one source email to many jobs.
- Schema, debug endpoints, and milestone tickets all describe durable traceability for that relationship.
- If a legacy single-job pointer still appears, the doc either removes it or clearly marks it transitional and non-canonical.

## T-018 Google Auth and Gmail Connection Slice Test Cases

### TC-1701 Google auth start returns a usable OAuth URL
**Steps**
1. Call `POST /api/v1/auth/google/start` from an unauthenticated state.
2. Inspect the response payload.

**Expected**
- Response returns a non-empty `authUrl`.
- Requested scopes include `openid`, `email`, `profile`, and `gmail.readonly` only.
- Response shape matches the API contract.

### TC-1702 OAuth callback creates session and redirects into the app
**Steps**
1. Start Google auth.
2. Complete the callback with a valid code and state.
3. Inspect resulting redirect and session state.

**Expected**
- Callback validates state and completes without exposing raw tokens to the browser.
- Session cookie is created.
- User lands in the authenticated app experience.
- `GET /api/v1/auth/session` returns the signed-in user and Gmail connection indicator.

### TC-1703 Protected routes deny unauthenticated access after auth work lands
**Steps**
1. Request a protected route without a valid session.
2. Request the same route with a valid session.

**Expected**
- Unauthenticated access is redirected or rejected consistently.
- Authenticated access succeeds.
- Protected shell behavior matches the Milestone 1 exit checks.

### TC-1704 Gmail account endpoint exposes readonly connection state cleanly
**Steps**
1. Complete auth with Gmail readonly granted.
2. Call `GET /api/v1/gmail/account`.
3. Repeat with disconnected, expired, revoked, and denied fixtures or test states.

**Expected**
- Response exposes `connected`, `needsReconnect`, `state`, `lastSyncedAt`, `lastErrorCode`, and `lastErrorMessage`.
- Allowed states match the PM memo and API contract.
- `needsReconnect` is true where reconnect is required.
- No broader Gmail scope appears in the persisted account state.

### TC-1705 Logout fully clears session state without disconnecting imported Gmail history
**Steps**
1. Sign in and confirm session exists.
2. Call `POST /api/v1/auth/logout`.
3. Recheck protected routes and Gmail account behavior after signing back in.

**Expected**
- Logout clears the active session.
- Protected routes are no longer accessible until re-authentication.
- Historical Gmail connection or imported state is not silently deleted by logout alone.

## T-021 Handoff Package QA Checks

### TC-1801 Final handoff package points to one canonical source-of-truth set
**Steps**
1. Review the Phase 3 handoff package.
2. Check the linked plan, contract, QA, and gate documents.

**Expected**
- One handoff file links the active plan, detail/workspace contracts, milestone ticket docs, and latest QA coverage.
- Gate A and Gate B readiness are called out explicitly.
- A new engineer could find current source-of-truth docs without reconstructing state from multiple channels.

## T-022 Frontend Auth and Gmail Connection Wiring Test Cases

### TC-1810 Frontend reflects signed-out, signed-in, and reconnect states
**Steps**
1. Open the app signed out.
2. Start auth from the UI and complete sign-in.
3. Revisit the app with a disconnected or reconnect-required Gmail state.

**Expected**
- Signed-out state exposes a clear auth entry point.
- Signed-in state shows session-aware UI without manual API calls.
- Gmail connection state renders connected versus reconnect-needed states clearly.

### TC-1811 Logout and reconnect actions are available from the authenticated shell
**Steps**
1. Start from an authenticated shell with Gmail account state loaded.
2. Trigger logout.
3. Sign back in and trigger reconnect if the Gmail state requires it.

**Expected**
- Logout clears the shell back to signed-out behavior.
- Reconnect action is visible only when needed.
- Session and Gmail connection controls do not drift from the backend contract.

## T-023 Canonical List-to-Detail Example Test Cases

### TC-1820 List and detail examples use the same section order and selection model
**Steps**
1. Review the list-to-detail examples doc.
2. Compare it with the detail-view contract and workspace UX contract.

**Expected**
- Example flows preserve the same section order defined in the detail contract.
- Selection continuity matches the workspace UX contract.
- Notes, tags, and status interactions do not contradict the locked edit model.

### TC-1821 Example payloads are concrete enough for frontend, API, and QA alignment
**Steps**
1. Inspect list payload, selected-row state, and detail payload examples.
2. Check for missing fields or ambiguous transitions.

**Expected**
- Examples show how list state maps into detail state without inferred behavior.
- Required fields for notes, tags, status, fit, and source metadata are present.
- Any missing example that blocks implementation is treated as a contract gap.

## T-025 Gate A Reconciliation Matrix Test Cases

### TC-1930 Matrix preserves canonical precedence order
**Steps**
1. Review `jobtrackr-gate-a-reconciliation-matrix-2026-04-20.md`.
2. Compare its precedence list to the current handoff package and live development plan.

**Expected**
- The matrix points engineers first to `projects/jobtrackr/DEVELOPMENT_PLAN.md`.
- PM memo, API contract, schema, milestone tickets, and handoff package all appear in a stable precedence order.
- Engineering can resolve a doc conflict without falling back to stale story files.

### TC-1931 Each Gate A rule names stale references and required engineering behavior
**Steps**
1. Review each Gate A row in the matrix.
2. Check whether workflow, archive, fit-nullability, dashboard payload, and source-email linkage all list stale references plus an explicit engineering action.

**Expected**
- Every Gate A rule identifies at least one risky stale source where drift is still likely.
- Every Gate A rule tells engineering what to implement or avoid.
- The matrix is actionable enough to use as a review checklist, not just a summary.

### TC-1932 Immediate drift callouts align with current QA blockers
**Steps**
1. Compare the matrix drift callouts with the current in-progress QA blockers.
2. Check auto-close semantics, old story docs, and source-email linkage guidance.

**Expected**
- The matrix explicitly flags the stale auto-close status model.
- Older story docs are marked non-canonical for workflow-state implementation.
- Source-email linkage guidance matches the join-table expectation used elsewhere in Gate A docs.

## T-026 Persisted Gmail Search and Filtering Test Cases

### TC-1950 Persisted jobs endpoint returns stored results without rescanning Gmail
**Steps**
1. Trigger one Gmail sync that creates persisted job records.
2. Call the jobs list endpoint twice without starting another sync.
3. Compare response behavior and persistence expectations.

**Expected**
- The jobs endpoint reads from persisted data.
- Repeated list calls do not require a new Gmail scan.
- Response semantics stay deterministic between calls when no new sync runs.

### TC-1951 Server-side filters honor canonical list semantics
**Steps**
1. Seed jobs that vary by status, company, and text content.
2. Call the jobs list endpoint with individual and combined query params.
3. Compare returned rows to the API contract filter rules.

**Expected**
- Supported filters narrow server results correctly.
- Combined filters use AND-across-filter-family behavior.
- Search scope stays limited to canonical searchable fields.

## T-027 Canonical List-to-Detail Example Test Cases

### TC-1960 Only one authoritative list-detail example file remains implementation-facing
**Steps**
1. Review `jobtrackr-list-detail-contract-examples-2026-04-20.md` and any older list-detail example docs.
2. Inspect cross-links from the API contract, detail contract, workspace contract, and handoff package.

**Expected**
- `jobtrackr-list-detail-contract-examples-2026-04-20.md` is the canonical example set.
- Older example files are either compatibility shims or explicitly marked non-canonical.
- New implementation-facing docs point only to the canonical file.

## T-028 Workspace Continuity QA Matrix Test Cases

### TC-1940 Continuity matrix covers preserve, clear, restore, and no-fallback rules
**Steps**
1. Review `jobtrackr-workspace-continuity-qa-matrix-2026-04-20.md`.
2. Confirm it includes sort, filter-hide, restore, dashboard return, mobile return, and no-fallback scenarios.

**Expected**
- QA has one canonical matrix for deterministic selection continuity.
- Each scenario names expected `selectedJobId`, `lastSelectedJobId`, and helper-state outcomes.
- Breakpoint parity is explicit instead of implied.

### TC-1941 Workspace continuity references are wired from the UX and session-state docs
**Steps**
1. Review the workspace UX contract and workspace session-state examples.
2. Confirm both point to the continuity QA matrix.

**Expected**
- Engineers can reach the QA matrix from the two canonical workspace docs.
- Reviewers do not need to infer which continuity file is implementation-facing versus QA-facing.

## T-029 Authenticated Persisted-Jobs Vertical Slice Test Cases

### TC-1970 Authenticated user can reach persisted jobs without mock-only fallback
**Steps**
1. Sign in through the auth flow.
2. Confirm Gmail connection is available.
3. Open the jobs UI after persisted jobs exist.

**Expected**
- The jobs UI loads through the authenticated session.
- Results come from persisted backend data rather than mock-only fixtures.
- Loading and empty states reflect real API responses cleanly.

### TC-1971 Session loss blocks persisted job retrieval cleanly
**Steps**
1. Load persisted jobs in an authenticated session.
2. Expire or clear the session.
3. Retry the jobs request and revisit the jobs UI.

**Expected**
- Persisted job retrieval is denied after session loss.
- The UI returns to a signed-out or re-auth-required state.
- No stale protected job data remains visible.

## T-030 Job Details Editing UI Hardening Test Cases

### TC-1980 Notes require explicit save or cancel in both drawer and page views
**Steps**
1. Open a job detail in drawer form.
2. Edit notes, then cancel.
3. Repeat in full-page detail and save.

**Expected**
- Notes never autosave.
- Cancel discards only unsaved changes.
- Save persists the edited notes in both detail patterns.

### TC-1981 Tag editing stays in-context and matches canonical section structure
**Steps**
1. Open detail in drawer and full-page patterns.
2. Add and remove tags in each view.
3. Compare section order and edit affordances.

**Expected**
- Tag edits happen in-context without a separate management screen.
- Drawer and page views keep the same section order.
- Editing affordances match the canonical detail contract.

## T-031 Deterministic Row-Selection Continuity Implementation Test Cases

### TC-1990 Pagination never replaces a hidden selection with a fallback row
**Steps**
1. Select a job in a paginated result set.
2. Change to another page where that job is not visible.
3. Return to the original page or otherwise make the job visible again.

**Expected**
- Selection clears when the job is no longer visible.
- No first-row or nearest-row fallback is auto-selected.
- The exact prior job restores only when it is visible again.

### TC-1991 Dashboard return preserves continuity state across responsive layouts
**Steps**
1. Select a job on desktop or tablet.
2. Leave to dashboard, then re-enter jobs on a different breakpoint.
3. Inspect selection and helper-state behavior.

**Expected**
- Filters and sort restore before selection logic runs.
- Selection restores only when the same job is visible.
- Breakpoint changes do not mutate continuity state by themselves.

## T-032 Stale Gate A Doc Cleanup Test Cases

### TC-2000 Legacy workflow statuses are removed or explicitly superseded in stale docs
**Steps**
1. Review older Gate A-related docs, especially stale auto-close and MVP story references.
2. Check for `flagged`, `reviewing`, `skipped`, `interview`, and `not a match` presented as canonical workflow statuses.

**Expected**
- Legacy workflow statuses are removed from implementation-facing docs or clearly marked superseded.
- Cleaned docs point back to the PM memo, API contract, and reconciliation matrix.
- Engineering cannot reasonably mistake stale semantics for current source of truth.

## T-033 Development Plan Ticket Language Reconciliation Test Cases

### TC-2010 Active ticket language no longer treats fit signals as workflow state
**Steps**
1. Review all active-ticket rows in `projects/jobtrackr/DEVELOPMENT_PLAN.md`.
2. Inspect task names, DoD text, AC text, and gap notes for low-fit, flagged, match-rating, and table-state wording.

**Expected**
- Active-ticket language does not present `flagged` or low-fit handling as workflow status.
- Match rating and low-fit wording are consistently described as fit signals or table semantics.
- Ticket copy points back to canonical source docs when implementation detail depends on locked contracts.

### TC-2011 Reconciled development plan keeps canonical workflow enum intact
**Steps**
1. Review all workflow references in the active-task table and notes sections.
2. Compare them to the canonical workflow enum in the PM memo and API contract.

**Expected**
- Only `new`, `interested`, `applied`, `interviewing`, `offer`, and `rejected` appear as workflow statuses.
- `not a match`, `flagged`, `reviewing`, `skipped`, and `interview` do not appear as canonical workflow outcomes in the development plan.
- Any legacy phrasing is removed or clearly marked superseded.

## T-034 Milestone and Ticket Wording Reconciliation Test Cases

### TC-2020 Milestone steps align with active-ticket semantics after the rewrite pass
**Steps**
1. Compare Milestones 1 to 8 in `DEVELOPMENT_PLAN.md` against the active-ticket table.
2. Inspect workflow, fit, and table semantics across both sections.

**Expected**
- Milestone steps do not contradict the active-ticket wording.
- Low-fit handling remains separate from workflow state throughout milestone text.
- Table payload and match-rating semantics stay consistent between milestone steps and active tasks.

### TC-2021 Milestone 6 low-fit wording stays canonical end to end
**Steps**
1. Review Milestone 6 steps and exit criteria.
2. Compare low-fit handling wording with T-006, T-033, and the Gate A reconciliation docs.

**Expected**
- Milestone 6 does not imply auto-close workflow mutation for low-fit jobs.
- Exit criteria describe fit indicators separately from workflow state.
- Low-fit jobs remain representable without introducing a synthetic workflow enum.

## T-045 PROJECT and Handoff Reference Reconciliation Test Cases

### TC-2030 PROJECT.md uses the same workflow and fit language as DEVELOPMENT_PLAN
**Steps**
1. Review `projects/jobtrackr/PROJECT.md`.
2. Compare status-model, fit, and flagged references with `DEVELOPMENT_PLAN.md` and the PM memo.

**Expected**
- PROJECT.md uses the canonical workflow enum only.
- Fit percentage, low-fit, and flagged behavior are described as separate signals, not workflow transitions.
- Next steps and active risks do not reintroduce stale semantics.

### TC-2031 Handoff-facing references point to reconciled source documents
**Steps**
1. Review PROJECT links to handoff, contract, and reconciliation docs.
2. Verify linked files represent the current canonical source set.

**Expected**
- PROJECT.md points engineers to the reconciled development plan, PM memo, API contract, and handoff package.
- Deprecated or compatibility-shim references are not presented as the preferred implementation path.

## T-046 PRD and QA Reference Reconciliation Test Cases

### TC-2040 PRD no longer exposes legacy workflow or auto-close semantics
**Steps**
1. Review the PRD and related MVP story docs.
2. Inspect workflow, low-fit, and flagged language.

**Expected**
- PRD does not present legacy statuses as canonical workflow states.
- Low-fit behavior is described through fit-signal semantics rather than workflow mutation.
- If older language must remain for history, it is clearly marked non-canonical.

### TC-2041 QA-owned test cases align with the canonical workflow and fit model
**Steps**
1. Review in-progress QA test cases and continuity matrices.
2. Inspect whether any QA case still expects stale workflow enums or auto-close behavior.

**Expected**
- QA cases test canonical workflow statuses only.
- QA coverage for low-fit behavior checks fit-signal handling, not fake status transitions.
- QA docs do not keep stale expectations alive after the reconciliation pass.

## T-047 Remaining Product and Spec Reference Reconciliation Test Cases

### TC-2050 Remaining specs stop using flagged as workflow shorthand
**Steps**
1. Review remaining product and implementation specs called out by the reconciliation pass.
2. Inspect flagged references in table UI, fit-analysis, and data-model-facing docs.

**Expected**
- `flagged` is not described as a workflow state.
- Any flagged behavior is clearly framed as shortlist or fit-signal behavior only where canonically supported.
- Spec wording does not let engineering confuse fit indicators with workflow state.

### TC-2051 Cross-spec workflow and fit language is internally consistent
**Steps**
1. Compare PM memo, API contract, schema, table UI spec, fit-analysis spec, and first-slice docs.
2. Check for conflicting workflow, archive, and fit-signal language.

**Expected**
- Cross-spec language is consistent enough for engineering pickup without PM clarification.
- Archive, saved, workflow, and fit concepts remain distinct across all reviewed docs.
- Any surviving stale references are isolated and clearly marked as blockers.

## T-048 Parser and Ingestion Ticket Wording Reconciliation Test Cases

### TC-2060 Parser follow-through docs do not imply parser-driven workflow mutation
**Steps**
1. Review Milestone 4 ticket breakdown and parser follow-through docs.
2. Inspect references to low-fit, rejected outcomes, and parser responsibilities.

**Expected**
- Parser work is limited to ingestion, extraction, dedupe, and persistence concerns.
- Parser docs do not imply `not a match` or any other non-canonical workflow outcome.
- Low-fit handling points back to the canonical fit-analysis contract where relevant.

### TC-2061 Parser and ingestion tickets preserve source-email retention and debug traceability
**Steps**
1. Review parser and ingestion acceptance language after reconciliation.
2. Inspect source-email retention, extraction-threshold, and dedupe wording.

**Expected**
- Tickets still require source-email retention even when no job row is created.
- Debug traceability remains explicit.
- Workflow-state semantics stay separate from parser acceptance criteria.

## Current QA coverage gaps
1. No tasks are marked `done` or moved to QA in `DEVELOPMENT_PLAN.md`, so this hour remains acceptance-coverage and blocker surfacing work rather than runnable execution validation.
2. T-006 is still blocked by status-model drift in `jobtrackr-auto-close-logic-spec-v1.md`, which still references pre-decision statuses like `flagged`, `reviewing`, `skipped`, `interview`, and `not a match` instead of the canonical workflow model.
3. T-002 still describes a Go web app in the live plan, while Milestone 1 and the handoff package describe a Next.js frontend plus Go API split, so QA cannot lock environment-specific execution steps until those docs are reconciled.
4. Jimmy's cron note points QA at `~/Documents/project-requirements/DEVELOPMENT_PLAN.md`, but the live plan is actually `projects/jobtrackr/DEVELOPMENT_PLAN.md`.
5. Gate A is still not fully closed, because the reconciliation matrix exists but older schema, story, PRD, and auto-close references still expose stale workflow or linkage assumptions.
6. Real runnable API fixtures are still needed before list and workspace checks for T-007, T-011, T-013, T-016, T-020, T-024, and T-025 can graduate from contract validation into executable integration tests.
7. The duplicate list/detail example naming issue is now partially mitigated: `jobtrackr-list-detail-examples-2026-04-20.md` is retained only as a compatibility shim, and `jobtrackr-list-detail-contract-examples-2026-04-20.md` is the canonical source. QA should verify new cross-links keep pointing only to the canonical file.
8. Gmail connection-state edge cases like `expired`, `revoked`, and `denied` now have acceptance coverage, but QA still needs runnable fixtures or test accounts before T-018 and T-022 can be signed off.
9. T-010 and T-014 ticket work now describe Gate A and Gate B, but QA still needs engineering to treat those gates as hard blockers before live-data integration starts.
10. T-023, T-024, and T-025 now have stronger canonical docs, but those examples and reconciliation rules still need explicit cross-links from the API contract, workspace contract, and handoff package so implementation teams do not fork behavior.
11. T-026 and T-029 still lack runnable persisted-jobs fixtures or endpoint examples in QA-owned docs, so execution coverage is blocked at contract level until engineering exposes deterministic sample responses.
12. T-030 and T-031 now have acceptance coverage, but QA still needs a real branch build to verify drawer/page parity, session-state transitions, and no-fallback selection behavior in the UI.
13. T-032 is still an active blocker area because older docs continue to advertise legacy workflow states that conflict with the canonical PM memo and current Gate A reconciliation work.
14. T-033 through T-048 now have contract-level QA coverage in this file, but execution coverage is still blocked until the reconciled downstream docs actually land and can be diff-verified against the PM memo, API contract, and reconciliation matrix.
15. Jimmy plan fetch is still blocked on Discord API auth from this environment, so QA cannot confirm whether the latest PM pickup order changed outside the local repo state.
16. The hourly prompt referenced `~/Documents/project-requirements/DEVELOPMENT_PLAN.md`, but the live source file remains `~/Documents/project-requirements/projects/jobtrackr/DEVELOPMENT_PLAN.md`; that path drift is itself a coordination gap worth fixing.
