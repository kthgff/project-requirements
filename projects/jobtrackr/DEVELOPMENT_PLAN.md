# DEVELOPMENT_PLAN.md

## Project Overview
Build and ship JobTrakr quickly with clean, maintainable implementation. This plan is now execution-first: use locked PM decisions as the product source of truth, minimize new requirements work, and prioritize coding the smallest end-to-end slices.


## Execution Rule

From this point forward, engineering should prefer coding against locked PM semantics over opening new requirement questions unless blocked.

Use these as locked product truths:
- Google login is required
- dashboard is the main jobs page
- jobs page is a searchable database-backed table
- each job has a match percentage
- flagged is a quick triage signal
- TypeScript is the project language direction

If a requirement appears ambiguous, check the canonical PM specs first and escalate only if still blocked.

## Team
| Agent | Role | Channel |
|---|---|---|
| Frank | Lead SWE | #swe-frank |
| Alice | SWE | #swe-alice |
| Marcus | SWE | #swe-marcus |
| Priya | SWE | #swe-priya |

## Active Tasks
| ID | Task | Owner | Status | Branch | Blocked By | DoD | AC | Gap |
|---|---|---|---|---|---|---|---|---|
| T-001 | Finalize PM decision alignment in JobTrackr API contract and debug semantics | Marcus | in-progress | chore/jobtrackr-contract-alignment | — | PM decisions documented, API contract finalized, debug semantics defined, spec approved | All open PM questions resolved, API contract versioned, debug semantics documented, spec reviewed by PM | — |
| T-002 | Build local Go web app for Gmail inbox job search, protected shell, and raw source-email ingestion | Alice | in-progress | feat/gmail-job-search-webapp | — | Local app runs, Gmail search works, protected shell implemented, raw ingestion works, tested | Local web app starts without errors, Gmail inbox search returns emails, protected routes require auth, raw emails stored in DB | — |
| T-003 | Produce JobTrakr dependency map and milestone sequencing pass for parallel-safe delivery | Frank | in-progress | chore/jobtrackr-dependency-map | — | Dependency map created, sequencing defined, parallel-safe validated, documented | Dependency graph complete, milestone sequence validated for parallel execution, blockers identified, documented in spec | — |
| T-004 | Lock jobs dashboard contract for match rating, flagged semantics, and table payload shape | Marcus | in-progress | feat/jobtrackr-jobs-dashboard-contract | — | Match rating defined, flagged semantics defined, table payload locked, API documented, reviewed | Match rating algorithm defined, flag thresholds documented, table JSON schema locked, OpenAPI spec updated | — |
| T-005 | Scaffold protected jobs dashboard web shell with mock table data for the first vertical slice | Priya | in-progress | feat/jobs-dashboard-mock-shell | — | Dashboard scaffolded, mock data displayed, auth works, responsive, loading states | Dashboard page loads with auth, table displays mock jobs, responsive on mobile/tablet, loading spinner shows | — |
| T-006 | Implement canonical low-fit handling for jobs with fit score below 60 without introducing non-canonical workflow statuses | Frank | in-progress | feat/jobtrackr-auto-close-logic | — | Low-fit handling is implemented against the canonical workflow model, low-score jobs remain representable without a fake status transition, tested, and edge cases are handled | Jobs with score < 60 are surfaced as low-fit according to the canonical fit contract, score = null does not force a workflow change, and logging/debug output is added | Reworded under T-033 to remove stale `not a match` workflow semantics and point implementation back to the canonical Gate A rules |
| T-007 | Implement view all jobs page with full job details and filtering | Alice | in-progress | feat/jobtrackr-view-all-jobs-page | — | Page created, details displayed, filtering works, search works, pagination works, tested | Page shows all jobs, filters by status/fit/date work, search returns results, pagination navigates pages | — |
| T-008 | Implement job details page to view full job information (description, skills, tags, notes, source link) | Marcus | in-progress | feat/jobtrackr-job-details-page | — | Details page created, all fields displayed, source link works, notes editable, tags manageable | Page shows all required fields, source link opens in new tab, notes save to DB, tags add/remove works | — |
| T-009 | Polish jobs table interaction states and shared dashboard UX for the mock-data frontend slice | Priya | in-progress | feat/jobtrackr-dashboard-table-polish | — | Dashboard and jobs views share richer table states, selected row treatment is clear, and empty/loading shells are present | Mock-data frontend supports obvious row selection, empty-state rendering, loading placeholders, and smoother dashboard-to-jobs navigation | Claimed from unassigned frontend polish work after Jimmy's latest sequencing pass |
| T-010 | Break the approved dependency sequencing into implementation-ready engineering tickets for Milestones 1 to 4 | Frank | in-progress | chore/jobtrackr-milestone-ticket-breakdown | T-001, T-004 | Milestones 1 to 4 have implementation-ready tickets with dependencies, owners, and exit criteria documented | Each ticket traces to a milestone step, notes contract dependencies, and gives engineering enough detail to implement without PM follow-up | Claimed from unassigned engineering handoff follow-through after Jimmy's latest sequencing pass |
| T-011 | Improve the responsive jobs list UX with mobile cards, clearer active filter context, and compact results navigation | Alice | in-progress | feat/jobtrackr-responsive-job-list | — | Jobs page works cleanly on smaller screens, active filters are visible, and list navigation remains usable while reviewing jobs | Mobile layout shows readable job cards, filter state is obvious, and users can move through filtered results without losing context | Claimed from unassigned jobs-page UX follow-through after Jimmy's latest pickup guidance |
| T-012 | Add editable notes and tag management interactions to the job details page using the agreed detail-view contract | Marcus | in-progress | feat/jobtrackr-job-details-editing | T-008 | Notes can be edited in-place, tags can be added and removed in the detail view, mock-state interactions are documented, and the UI is ready for API wiring | Users can edit notes without leaving the page, add/remove tags with clear controls, and the interaction pattern matches the JobTrackr detail-page contract | Claimed from unassigned detail-view follow-through after Jimmy's latest pickup guidance |
| T-013 | Add visible active-filter context and empty-result recovery controls to the mock jobs workspace | Priya | in-progress | feat/jobtrackr-filter-context | — | Jobs workspace shows the currently active filters, empty-result states explain why nothing matched, and users can recover with a one-click reset or dashboard return path | Active search/status/location filters remain visible after apply, zero-result states render guidance instead of a blank table, and recovery actions clear filters or return to the dashboard without confusion | Claimed from unassigned frontend UX follow-through after Jimmy's latest pickup guidance |
| T-014 | Break the finalized parser and ingestion contract changes into implementation-ready tickets after the jobs dashboard payload is locked | Frank | in-progress | chore/jobtrackr-milestone-ticket-breakdown | T-001, T-004 | Parser and ingestion follow-through is decomposed into concrete engineering tickets with dependencies, delivery order, and acceptance notes | Engineering can pick up Gmail ingestion follow-through work without extra PM clarification once the contract is stable, and each ticket traces to Milestone 4 delivery | Claimed from the unassigned upcoming engineering handoff follow-through during hourly kickoff |
| T-015 | Define the job detail view contract for field grouping, edit interactions, and API expectations across T-008 and T-012 | Marcus | in-progress | feat/jobtrackr-detail-view-contract | T-001, T-004 | Detail-view contract documents required fields, section order, notes and tag edit behavior, and API dependencies for the frontend slice | T-008 and T-012 can implement against one shared detail-view contract without inventing new interaction patterns, and open API dependencies are explicitly called out | Claimed from unassigned detail-view contract follow-through during hourly kickoff |
| T-016 | Define the mock jobs workspace UX contract for active-filter visibility, empty-result recovery, and dashboard-to-jobs navigation | Priya | in-progress | feat/jobtrackr-workspace-ux-contract | T-005, T-009, T-013 | Workspace UX contract documents filter summary treatment, zero-result recovery actions, row-selection persistence, and responsive navigation expectations for the mock-data frontend slice | Priya can implement T-013 and keep T-005/T-009 aligned with one shared interaction contract, with clear states for mobile/tablet and no ambiguity around filter reset or return-to-dashboard flows | Claimed from unassigned frontend workspace follow-through during hourly kickoff |
| T-017 | Close Gate A documentation by reconciling schema, workflow/archive semantics, fit nullability, and source-email linkage across canonical specs | Frank | in-progress | chore/jobtrackr-gate-a-closeout | T-001 | Canonical specs agree on workflow/archive semantics, fit nullability, and one-source-email-to-many-jobs linkage, with Gate A references updated for engineering handoff | Engineering can use the PM memo, schema specs, and milestone tickets without conflicting status enums, archive behavior, fit defaults, or source-email linkage assumptions | Claimed from Jimmy's latest hourly guidance during kickoff |
| T-018 | Implement the Google auth callback, session flow, and Gmail readonly connection path for the first end-to-end vertical slice | Alice | in-progress | feat/jobtrackr-auth-session-gmail-connect | — | Backend exposes auth start/callback/session/logout and Gmail connect/account endpoints for the readonly MVP flow, the app can create a session after Google callback, and the connection state is visible to the frontend | A user can start Google auth, complete callback, receive an app session, and see Gmail readonly connection status without relying on manual token env injection | Claimed from Jimmy's latest hourly guidance during kickoff |
| T-019 | Align the job details UI with the locked detail-view contract, including section order, editing affordances, and list/detail behavior examples | Marcus | in-progress | feat/jobtrackr-job-details-editing | T-015 | Job details UI follows the agreed header, fit/workflow, job content, notes, and tags structure, with notes save/cancel and direct tag editing matching the shared contract | The jobs list side panel and full detail page present the same section order, notes editing requires explicit save/cancel, tags are editable in-context, and mock interactions are documented clearly enough for later API wiring | Claimed from Jimmy's latest hourly guidance during kickoff |
| T-020 | Lock row-selection continuity rules for the mock jobs workspace across filter changes, sorting, and dashboard return flows | Priya | in-progress | feat/jobtrackr-row-selection-continuity | T-016 | Workspace contract and QA cases define when selection is preserved, cleared, restored, and announced across table, detail, and dashboard transitions | Frontend can implement one deterministic selection model for desktop, tablet, and mobile, with explicit recovery behavior when a selected row leaves the visible result set | Claimed from Jimmy's latest hourly guidance during kickoff |
| T-021 | Assemble the final Phase 3 engineering handoff package with source-of-truth links, dependency gates, owner mapping, and immediate pickup guidance | Frank | in-progress | chore/jobtrackr-final-handoff-package | T-017, T-010, T-014 | Engineering has one package that points to the canonical contracts, documents Gate A and Gate B readiness, summarizes milestone tickets, and tells each lane what to pick up next without PM reconstruction | A new engineer can open one handoff file, see the correct source-of-truth docs, understand which contracts are locked vs pending, and know the next recommended task by lane | Claimed during hourly kickoff after Jimmy's 04:02 plan highlighted missing single-file handoff context |
| T-022 | Wire the authenticated frontend shell to the new auth session and Gmail connection endpoints for the first vertical slice | Alice | in-progress | feat/jobtrackr-auth-session-gmail-connect | T-018 | Frontend can start Google auth, reflect signed-in session state, show Gmail connection status, and offer logout/reconnect flows against the new backend endpoints | A user landing in the app can start auth from the UI, see whether a session exists, view Gmail readonly connection state, and reconnect or sign out without manual API calls | Claimed during hourly kickoff to support Alice's auth/session/Gmail vertical slice delivery |
| T-023 | Add canonical list-to-detail contract examples so the jobs workspace and detail UI stay aligned with the locked section order and edit model | Marcus | in-progress | feat/jobtrackr-detail-contract-examples | T-004, T-015 | One shared examples doc shows how the jobs list payload, selected-row state, and detail response work together across drawer and full-page detail patterns, with notes/tag/status interactions matching the locked contract | Frontend and API can point to concrete list/detail examples instead of inferred behavior, and QA can validate section order, selection continuity, and edit affordances from one canonical reference | Claimed during hourly kickoff after Jimmy asked Marcus to push list/detail contract examples and detail editing alignment |
| T-024 | Add canonical workspace session-state examples for deterministic row-selection continuity across filters, sorting, and dashboard return flows | Priya | in-progress | feat/jobtrackr-workspace-session-state | T-016, T-020 | Workspace docs and QA coverage show exact session-state transitions for preserve, clear, and restore behavior across desktop, tablet, and mobile flows | Frontend can implement one session-state model without guessing when to keep `selectedJobId`, when to clear it, how to restore it after filters relax, or what helper copy to show after dashboard re-entry | Claimed during hourly kickoff after Jimmy asked Priya to lock the deterministic selection model across filter changes, sorting, dashboard return flows, and mobile/tablet states |
| T-025 | Publish a canonical Gate A reconciliation matrix so engineering can resolve workflow/archive, fit nullability, and source-email linkage drift from one file | Frank | in-progress | chore/jobtrackr-gate-a-reconciliation-matrix | T-017 | One canonical reconciliation doc maps each Gate A decision to the PM memo, schema, API contract, milestone tickets, and handoff package, with explicit callouts where older assumptions were corrected | Engineers can trace every Gate A rule to the current source-of-truth docs without re-reading the whole repo, and stale workflow/archive semantics are clearly marked as superseded | Claimed during hourly kickoff after Jimmy flagged ongoing contract-drift risk across schema, workflow semantics, and engineering handoff references |
| T-026 | Add persisted Gmail search results and first-pass server-side filtering endpoints for the inbox job search slice | Alice | in-progress | feat/jobtrackr-gmail-search-persistence-filtering | — | API persists Gmail-derived jobs and exposes filtered list retrieval for the first search workflow, with documented query params and tested filter behavior | A developer can run a sync, call one jobs endpoint with status/search/company filters, and receive deterministic persisted results without rescanning Gmail for every view refresh | Claimed from the unassigned upcoming Gmail job search follow-through during hourly kickoff after Jimmy prioritized the auth/session/Gmail vertical slice |
| T-027 | Canonicalize the list-to-detail example docs so API, frontend, and QA reference one authoritative detail continuity example set | Marcus | in-progress | feat/jobtrackr-list-detail-canonicalization | T-015, T-023 | Duplicate or stale list/detail example docs are reconciled, the API contract points to one canonical file, and older references clearly defer to the authoritative example set | Engineers and QA can follow one list/detail example doc without conflicting status examples, edit-flow guidance, or cross-reference drift between API and detail-view contracts | Claimed during hourly kickoff after Jimmy asked Marcus to keep list-to-detail examples canonical and ahead of implementation ambiguity |
| T-028 | Add a canonical workspace continuity QA matrix for filter, sort, dashboard return, and responsive selection behavior | Priya | in-progress | feat/jobtrackr-workspace-continuity-qa | T-016, T-020, T-024 | One QA-ready matrix covers preserve, clear, restore, and helper-copy behavior across desktop, tablet, and mobile workspace flows, with references back to the locked workspace contract and session-state examples | Frontend and QA can validate deterministic selection continuity without inferring missing cases, and each scenario maps to expected `selectedJobId`, `lastSelectedJobId`, and helper-state outcomes | Claimed during hourly kickoff after Jimmy asked Priya to lock deterministic selection continuity across filters, sorting, dashboard return flows, and mobile/tablet behavior |
| T-029 | Deliver the first authenticated persisted-jobs vertical slice by wiring session-backed Gmail search results into the jobs UI | Alice | in-progress | feat/jobtrackr-auth-session-gmail-connect | T-018, T-022, T-026 | The authenticated app can load persisted jobs after Gmail connection, reuse the session flow, and render deterministic server-backed results in the jobs UI without falling back to mock-only behavior | After Google auth and Gmail readonly connect, the frontend can request persisted jobs through the signed-in session, show loading and empty states from real API responses, and document the handoff point between auth/session work and persisted search filtering | Claimed during hourly kickoff after Jimmy directed Alice to complete the first real auth -> session -> Gmail connection -> persisted job retrieval vertical slice |
| T-030 | Harden the job details editing UI with contract-aligned save/cancel notes flow, in-context tag edits, and shared section structure across drawer and page views | Marcus | in-progress | feat/jobtrackr-job-details-editing | T-012, T-019, T-023, T-027 | The mock details UI matches the canonical section order, notes require explicit save/cancel, tags edit directly in context, and the component is ready for later API wiring without interaction ambiguity | The jobs drawer and full-page detail view share the same header, fit/workflow, content, notes, and tags structure, notes do not autosave, tag edits are obvious, and the component behavior matches the canonical detail-view examples | Claimed during hourly kickoff to convert the locked detail-view contract into implementation-ready UI behavior on Marcus's active branch |
| T-031 | Implement deterministic row-selection continuity in the mock jobs workspace across filter changes, pagination, and dashboard return flows | Priya | in-progress | feat/jobtrackr-row-selection-model | T-013, T-016, T-020, T-024, T-028 | Jobs workspace preserves, clears, and restores selection predictably with helper copy and responsive affordances when the selected row leaves or re-enters the visible result set | The mock frontend keeps one deterministic `selectedJobId` and `lastSelectedJobId` model across jobs filters, page changes, and dashboard return links, with explicit recovery UI when a selection is out of view or filtered out | Claimed during hourly kickoff after Jimmy asked Priya to harden the deterministic selection model before frontend behavior drifts |
| T-032 | Rewrite stale Gate A docs so legacy workflow statuses and auto-close behavior no longer conflict with the canonical PM memo, API contract, and reconciliation matrix | Frank | in-progress | chore/jobtrackr-gate-a-stale-doc-cleanup | T-017, T-025 | The stale auto-close and MVP story docs are rewritten or clearly marked so engineering does not reuse superseded workflow/archive semantics during implementation pickup | Engineers reading older follow-through docs no longer see `flagged`, `reviewing`, `skipped`, `interview`, or `not a match` presented as canonical workflow states, and the cleanup points back to the current Gate A source-of-truth set | Claimed during hourly kickoff after Jimmy's latest watchouts highlighted lingering stale workflow semantics in older follow-through docs |
| T-033 | Reconcile DEVELOPMENT_PLAN ticket language with the canonical workflow, fit, and table-contract decisions so engineers stop inheriting stale implementation assumptions | Alice | in-progress | chore/jobtrackr-development-plan-reconciliation | T-001, T-004, T-017, T-025 | DEVELOPMENT_PLAN no longer presents superseded workflow statuses, auto-close semantics, or stale flagged behavior as implementation truth, and any still-open ticket copy clearly points back to the canonical source docs | Engineers can pick up tasks from DEVELOPMENT_PLAN without reintroducing `not a match` as a workflow status, mistaking fit indicators for workflow state, or following stale milestone wording that conflicts with Gate A decisions | Claimed during hourly kickoff after Jimmy asked for the development plan ticket language to be rewritten and reconciled |
| T-034 | Audit and reconcile milestone and ticket wording in DEVELOPMENT_PLAN so canonical workflow, fit, and table semantics stay consistent after the ticket rewrite pass | Marcus | in-progress | chore/jobtrackr-development-plan-reconciliation | T-001, T-004, T-017, T-025, T-033 | Milestone steps and active-ticket wording use the same canonical workflow and fit language, with stale assumptions removed or redirected to source-of-truth docs | Engineers reading either the task table or milestone breakdown see one consistent contract for workflow statuses, low-fit handling, and table payload semantics without reintroducing legacy terms | Claimed during hourly kickoff after Jimmy asked for the development plan rewrite to be reconciled all the way through the engineering plan |
| T-045 | Reconcile PROJECT, milestone, and handoff references so the plan rewrite stays aligned outside the task table | Marcus | in-progress | chore/jobtrackr-development-plan-reconciliation | T-034 | PROJECT.md, milestone wording, and handoff-facing references use the same canonical workflow and fit language as the reconciled development plan, with stale assumptions removed or redirected to the source-of-truth docs | Engineers can move between DEVELOPMENT_PLAN, PROJECT.md, and the implementation milestone docs without seeing `flagged`, `reviewing`, `skipped`, `interview`, or `not a match` presented as current workflow truth, and fit signals remain clearly separate from workflow state | Claimed during hourly kickoff after Jimmy fetch was blocked and no unclaimed Marcus task remained in the active table, so Marcus pulled the next reconciliation follow-through on the active development-plan branch |
| T-046 | Reconcile PRD and QA test-case references so legacy workflow and auto-close language no longer conflicts with the canonical workflow and fit model | Priya | in-progress | chore/jobtrackr-development-plan-reconciliation | T-033, T-034, T-045 | PRD and in-progress QA test cases stop presenting `flagged`, `reviewing`, `skipped`, `interview`, and `not a match` as canonical workflow or auto-close outcomes, while still preserving the intended fit-signal and low-fit coverage | Engineers and QA can read the PRD plus active test cases without inheriting stale status enums or auto-close behavior, and all low-fit coverage points back to the canonical fit-signal contract instead of a workflow mutation | Claimed during hourly kickoff after Jimmy fetch was blocked and no unclaimed active follow-through remained, so Priya pulled the next reconciliation task on the active development-plan branch |
| T-047 | Reconcile remaining product/spec references so legacy flagged and stale workflow language no longer conflicts with the canonical workflow and fit-signal model | Frank | in-progress | chore/jobtrackr-development-plan-reconciliation | T-032, T-033, T-034, T-045, T-046 | Remaining product and implementation specs that still use legacy workflow or flagged wording are rewritten or annotated to align with the canonical workflow statuses and fit-signal semantics | Engineers can read the data model, table UI, fit-analysis, and first-slice specs without mistaking fit indicators for workflow state or inheriting stale status enums | Claimed during hourly kickoff after Jimmy fetch was blocked and no unclaimed active follow-through remained, so Frank pulled the next spec-reconciliation task on the active development-plan branch |
| T-048 | Reconcile parser and ingestion ticket wording so milestone follow-through docs stop implying deprecated auto-close or workflow-state behavior for low-fit jobs | Alice | in-progress | chore/jobtrackr-development-plan-reconciliation | T-033, T-034, T-047 | Parser and ingestion breakdown docs use the canonical low-fit and fit-signal language, keep workflow state separate from parser responsibilities, and point implementation back to the locked source-of-truth set where needed | Engineers can pick up Milestone 4 parser work without inferring `not a match`, parser-driven workflow mutation, or stale flagged semantics from follow-through ticket wording | Claimed during hourly kickoff after Jimmy fetch was blocked and no unclaimed Alice task remained, so Alice pulled the next milestone follow-through reconciliation task on the active development-plan branch |

## Completed
| ID | Task | Owner | Merged |
|---|---|---|---|

## Upcoming
- T-034 was pulled into Active during hourly kickoff so Marcus could finish the reconciliation pass all the way through the milestone plan.
- T-045 was pulled into Active during hourly kickoff so Marcus could keep PROJECT, milestone, and handoff references aligned with the reconciled development plan after Jimmy fetch was blocked.
- T-046 was pulled into Active during hourly kickoff so Priya could reconcile PRD and QA test-case language with the canonical workflow and low-fit contract after Jimmy fetch was blocked.
- T-047 was pulled into Active during hourly kickoff so Frank could reconcile remaining product and implementation specs with the canonical workflow and fit-signal contract after Jimmy fetch was blocked.
- T-048 was pulled into Active during hourly kickoff after Jimmy fetch was blocked and no unclaimed Alice task remained, so Alice pulled the parser and ingestion follow-through reconciliation pass on the active development-plan branch.

## Decisions & Notes
- Keep task ownership explicit to avoid duplicate work across SWE agents.
- Branch naming should track the task focus and remain one task per branch.
- Gmail integration for the new job search project will use Gmail API with local web OAuth flow and env-based secrets, not embedded credentials.
- Gmail access remains Gmail readonly scope only for MVP.
- Google auth policy: allow anyone with a Google account to sign in (not single approved account only).
- The first Alice vertical slice is auth, protected shell, Gmail connection, and raw source-email ingestion before fit analysis.
- Initial sync defaults to the last 30 days, with future work planned around a 10 minute incremental sync plus manual refresh.
- Frank is owning the dependency map and milestone sequencing pass to identify blockers before Milestones 1 through 4 proceed in parallel.
- Marcus is owning the jobs dashboard contract shape so frontend and API work can converge on the same table payload, especially around match rating, low-fit indicator semantics, and non-workflow table signals.
- Job enrichment: scrape full details from job detail page linked in email.
- Deduplication: match on job URL to merge repeated alerts for the same job.
- Mandatory job record fields: id, title, company, description, salary range, location, fit score (nullable until analysis), skills, tags.
- Fit display: show a user-facing match rating when fit analysis exists, while keeping fit state separate from workflow status.
- Low-fit handling: jobs with fit score below 60 must follow the canonical low-fit contract and must not introduce `not a match` as a workflow status.
- Dashboard semantics: match rating and low-fit indicators are fit signals, not workflow states, and table payload wording should reflect that distinction.
- Notifications: no email/push notifications; user reviews jobs in UI.
- Status model: new, interested, applied, interviewing, offer, rejected.

## Branch & PR Rules
- Branch naming: `feat/<short-desc>`, `fix/<short-desc>`, `chore/<short-desc>`
- One task per branch
- PR must reference the Task ID in the title
- Do not merge without Sam (QA) sign-off on feature work

## Task Workflow
- When SWE completes DoD for a task, move task status to "QA" and assign to Sam
- Sam (QA) validates DoD criteria are met before approving PR
- If DoD not met, Sam returns task to SWE with feedback
- Only after QA approval can task be moved to "Completed"

## Development Steps

### Milestone 1: Auth Foundation
**Goal:** Ship user login and protected app access.

**Locked product semantics for this milestone:**
- Google federated login
- protected dashboard access
- single jobs workspace entry via authenticated session

**Steps:**
1. Set up monorepo structure: `apps/web`, `apps/api`, `packages/types`, `infra/`
2. Initialize Go API with Gin framework
3. Initialize Next.js web app with TypeScript
4. Configure Google OAuth credentials (client ID, secret, redirect URIs)
5. Implement OAuth initiation endpoint (`GET /api/v1/auth/google`)
6. Implement OAuth callback handler (`GET /api/v1/auth/callback`)
7. Create user record in database on successful OAuth
8. Implement secure session cookie creation (HTTP-only, secure, same-site)
9. Add protected route middleware to validate session
10. Build login page in Next.js with Google sign-in button
11. Add session-aware API client to frontend
12. Test end-to-end sign-in flow
13. Verify unauthenticated users cannot access dashboard
14. Verify authenticated users reach dashboard shell

**Exit Criteria:**
- User can sign in with Google
- Unauthenticated users cannot access dashboard
- Authenticated users reach the dashboard shell

---

### Milestone 2: Dashboard Shell and Table Foundation
**Goal:** Ship the visible frontend shell for the app.

**Locked product semantics for this milestone:**
- dashboard is the main jobs page
- jobs page is a searchable table
- required columns include fit flag, match %, company, title, location, source, date found, and status
- use mock data first only to unblock UI implementation, then replace with DB-backed data

**Steps:**
1. Design dashboard layout (header, filters row, jobs table, pagination)
2. Implement dashboard page in Next.js
3. Add header with account controls and logout
4. Create filters row shell (status, location, date range, search)
5. Build jobs table component with required columns (company, title, location, source, date found, match rating, status)
6. Implement row interaction pattern (click to expand detail drawer)
7. Add loading states for table
8. Add empty states for no jobs
9. Create mock job data for testing
10. Connect table to mock data
11. Test responsive layout
12. Verify table renders all required columns
13. Verify row interaction works

**Exit Criteria:**
- Authenticated user sees dashboard
- Table renders required columns
- Row interaction pattern is defined and working

---

### Milestone 3: Data Model and Real Job List
**Goal:** Replace mock table data with real persisted jobs.

**Locked product semantics for this milestone:**
- jobs page reads from the database
- searchable table remains the primary review UI
- payload must include match percentage when available

**Steps:**
1. Define database schema (users, jobs, tags, notes, gmail_tokens, sync_state)
2. Create PostgreSQL migration scripts
3. Set up local PostgreSQL with Docker Compose
4. Implement sqlc for type-safe database queries
5. Create job repository with CRUD operations
6. Implement jobs list query with search, filters, sorting, and pagination
7. Implement single-job detail query
8. Add API endpoints: `GET /api/v1/jobs`, `GET /api/v1/jobs/:id`
9. Connect frontend dashboard to real API endpoints
10. Test with seed job data
11. Verify dashboard reads jobs from database
12. Verify job detail view can load a real job

**Exit Criteria:**
- Dashboard reads jobs from database
- Job detail view can load a real job

---

### Milestone 4: Gmail Ingestion Baseline
**Goal:** Populate real jobs from Gmail inbox scanning.

**Steps:**
1. Configure Gmail API OAuth scope (readonly)
2. Implement Gmail token storage (encrypted at rest)
3. Add Gmail token refresh logic
4. Build Gmail client wrapper for API calls
5. Implement scheduled polling worker (configurable interval)
6. Fetch recent messages from Gmail (incremental from last checkpoint)
7. Implement job email detection rules (sender, subject keywords, labels)
8. Persist Gmail message metadata
9. Build rule-based job parser (extract title, company, location, link, salary)
10. Implement deduplication logic (match on job URL)
11. Create or update job records from parsed data
12. Add ingestion logging and error handling
13. Test sync with test Gmail account
14. Verify inbox scan creates job records from relevant emails
15. Verify duplicate obvious repeats do not create duplicate active rows

**Exit Criteria:**
- Inbox scan creates job records from relevant emails
- Duplicate obvious repeats do not create duplicate active rows

---

### Milestone 5: Resume Upload and Fit Foundation
**Goal:** Allow the system to evaluate jobs against the user's resume.

**Steps:**
1. Design resume upload UI in settings page
2. Implement file upload endpoint (`POST /api/v1/resume`)
3. Add resume storage (file system or object storage)
4. Parse uploaded resume (extract text, skills, experience)
5. Store parsed resume profile in database
6. Implement active resume selection logic
7. Wire fit analysis trigger (run when new jobs are created)
8. Add resume display in settings
9. Test resume upload and parsing
10. Verify user can upload a resume
11. Verify uploaded resume is available for job fit analysis

**Exit Criteria:**
- User can upload a resume
- Uploaded resume is available for job fit analysis

---

### Milestone 6: Fit Analysis and Fit Signals
**Goal:** Surface likely good-fit roles in the dashboard without conflating fit indicators with workflow state.

**Steps:**
1. Design fit scoring algorithm (compare job vs resume skills/experience)
2. Implement fit scoring service (score 0-100)
3. Add fit analysis trigger on job creation
4. Store nullable fit score, low-fit indicator metadata, and rationale in the job record
5. Implement canonical low-fit handling for jobs below 60 without writing a non-canonical workflow status
6. Add low-fit indicator logic as a fit signal, not a workflow status
7. Display fit score in the table column as the user-facing match rating
8. Add fit badge/color coding (high score vs low score)
9. Show fit rationale in the job detail drawer
10. Test fit analysis with sample jobs and resume
11. Verify jobs receive fit results
12. Verify the dashboard clearly shows match rating, low-fit signals, and pending-fit states without implying a workflow transition
13. Verify low-fit handling works for jobs below 60 score without mutating workflow state incorrectly

**Exit Criteria:**
- Jobs receive fit results
- Dashboard clearly shows fit indicators without treating them as workflow states
- Low-fit handling works for jobs below 60 score under the canonical contract

---

### Milestone 7: Workflow Completion
**Goal:** Complete the core triage and review workflow.

**Steps:**
1. Implement job detail drawer or detail page with full job information
2. Display job description, skills, tags, notes, and source link on details page
3. Add status update endpoint (`PATCH /api/v1/jobs/:id/status`)
4. Add tag management endpoints (CRUD)
5. Add notes endpoint (CRUD)
6. Implement filter refinements (by status, fit score, date, tags)
7. Add source link access (open job URL in new tab)
8. Add better error states and retry logic
9. Implement job archival logic
10. Add search across title, company, description
11. Implement view all jobs page with full job details and advanced filtering
12. Test end-to-end workflow (login → view jobs → view details → update status → add tags)
13. Verify user can review, inspect, and update jobs end to end

**Exit Criteria:**
- User can review, inspect, and update jobs end to end

---

### Milestone 8: QA and Release Readiness
**Goal:** Prepare MVP for real use.

**Steps:**
1. Review all features against PRD and specs
2. Run acceptance tests for each milestone
3. Fix critical bugs identified during testing
4. Validate edge cases (empty inbox, parsing failures, token expiry)
5. Test with real Gmail account and job alerts
6. Verify Gmail token refresh works
7. Test resume upload with various file formats
8. Validate fit analysis accuracy
9. Create launch checklist
10. Set up staging environment
11. Deploy to staging for final validation
12. Prepare production deployment
13. Configure production environment variables
14. Set up monitoring (Sentry, health endpoints)
15. Verify critical user flow works from login to fit-scored jobs in the dashboard
16. Resolve major known blockers

**Exit Criteria:**
- Critical user flow works from login to fit-scored jobs in the dashboard
- Major known blockers resolved
