# DEVELOPMENT_PLAN.md

## Project Overview
Build and ship the current AI-powered app initiative quickly with clean, maintainable implementation, using this plan to coordinate work across engineering agents and avoid overlap.

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
| T-006 | Implement auto-close logic for jobs with match score below 60 (set to 'not a match' status) | Frank | in-progress | feat/jobtrackr-auto-close-logic | — | Auto-close implemented, jobs below 60 set to 'not a match', tested, edge cases handled | Jobs with score < 60 auto-set to 'not a match', edge case (score = null) handled, logging added | — |
| T-007 | Implement view all jobs page with full job details and filtering | Alice | in-progress | feat/jobtrackr-view-all-jobs-page | — | Page created, details displayed, filtering works, search works, pagination works, tested | Page shows all jobs, filters by status/fit/date work, search returns results, pagination navigates pages | — |
| T-008 | Implement job details page to view full job information (description, skills, tags, notes, source link) | Marcus | in-progress | feat/jobtrackr-job-details-page | — | Details page created, all fields displayed, source link works, notes editable, tags manageable | Page shows all required fields, source link opens in new tab, notes save to DB, tags add/remove works | — |
| T-009 | Polish jobs table interaction states and shared dashboard UX for the mock-data frontend slice | Priya | in-progress | feat/jobtrackr-dashboard-table-polish | — | Dashboard and jobs views share richer table states, selected row treatment is clear, and empty/loading shells are present | Mock-data frontend supports obvious row selection, empty-state rendering, loading placeholders, and smoother dashboard-to-jobs navigation | Claimed from unassigned frontend polish work after Jimmy's latest sequencing pass |
| T-010 | Break the approved dependency sequencing into implementation-ready engineering tickets for Milestones 1 to 4 | Frank | in-progress | chore/jobtrackr-milestone-ticket-breakdown | T-001, T-004 | Milestones 1 to 4 have implementation-ready tickets with dependencies, owners, and exit criteria documented | Each ticket traces to a milestone step, notes contract dependencies, and gives engineering enough detail to implement without PM follow-up | Claimed from unassigned engineering handoff follow-through after Jimmy's latest sequencing pass |
| T-011 | Improve the responsive jobs list UX with mobile cards, clearer active filter context, and compact results navigation | Alice | in-progress | feat/jobtrackr-responsive-job-list | — | Jobs page works cleanly on smaller screens, active filters are visible, and list navigation remains usable while reviewing jobs | Mobile layout shows readable job cards, filter state is obvious, and users can move through filtered results without losing context | Claimed from unassigned jobs-page UX follow-through after Jimmy's latest pickup guidance |
| T-012 | Add editable notes and tag management interactions to the job details page using the agreed detail-view contract | Marcus | in-progress | feat/jobtrackr-job-details-editing | T-008 | Notes can be edited in-place, tags can be added and removed in the detail view, mock-state interactions are documented, and the UI is ready for API wiring | Users can edit notes without leaving the page, add/remove tags with clear controls, and the interaction pattern matches the JobTrackr detail-page contract | Claimed from unassigned detail-view follow-through after Jimmy's latest pickup guidance |
| T-013 | Add visible active-filter context and empty-result recovery controls to the mock jobs workspace | Priya | in-progress | feat/jobtrackr-filter-context | — | Jobs workspace shows the currently active filters, empty-result states explain why nothing matched, and users can recover with a one-click reset or dashboard return path | Active search/status/location filters remain visible after apply, zero-result states render guidance instead of a blank table, and recovery actions clear filters or return to the dashboard without confusion | Claimed from unassigned frontend UX follow-through after Jimmy's latest pickup guidance |
| T-014 | Break the finalized parser and ingestion contract changes into implementation-ready tickets after the jobs dashboard payload is locked | Frank | in-progress | chore/jobtrackr-milestone-ticket-breakdown | T-001, T-004 | Parser and ingestion follow-through is decomposed into concrete engineering tickets with dependencies, delivery order, and acceptance notes | Engineering can pick up Gmail ingestion follow-through work without extra PM clarification once the contract is stable, and each ticket traces to Milestone 4 delivery | Claimed from the unassigned upcoming engineering handoff follow-through during hourly kickoff |

## Completed
| ID | Task | Owner | Merged |
|---|---|---|---|

## Upcoming
- Add persistence and filtering improvements to the Gmail job search web app after initial scaffold

## Decisions & Notes
- Keep task ownership explicit to avoid duplicate work across SWE agents.
- Branch naming should track the task focus and remain one task per branch.
- Gmail integration for the new job search project will use Gmail API with local web OAuth flow and env-based secrets, not embedded credentials.
- Gmail access remains Gmail readonly scope only for MVP.
- Google auth policy: allow anyone with a Google account to sign in (not single approved account only).
- The first Alice vertical slice is auth, protected shell, Gmail connection, and raw source-email ingestion before fit analysis.
- Initial sync defaults to the last 30 days, with future work planned around a 10 minute incremental sync plus manual refresh.
- Frank is owning the dependency map and milestone sequencing pass to identify blockers before Milestones 1 through 4 proceed in parallel.
- Marcus is owning the jobs dashboard contract shape so frontend and API work can converge on the same table payload, especially around match rating and flagged semantics.
- Job enrichment: scrape full details from job detail page linked in email.
- Deduplication: match on job URL to merge repeated alerts for the same job.
- Mandatory job record fields: id, title, company, description, salary range, location, match (1-100), skills, tags.
- Fit display: show match score from 1 to 100.
- Auto-close: jobs with match score below 60 are automatically set to "not a match" status.
- Notifications: no email/push notifications; user reviews jobs in UI.
- Status model: new, flagged, reviewing, skipped, not a match, applied, interview, rejected, offer.

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

**Steps:**
1. Design dashboard layout (header, filters row, jobs table, pagination)
2. Implement dashboard page in Next.js
3. Add header with account controls and logout
4. Create filters row shell (status, location, date range, search)
5. Build jobs table component with required columns (company, title, location, source, date found, fit flag, status)
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

**Steps:**
1. Define database schema (users, jobs, tags, notes, gmail_tokens, sync_state)
2. Create PostgreSQL migration scripts
3. Set up local PostgreSQL with Docker Compose
4. Implement sqlc for type-safe database queries
5. Create job repository with CRUD operations
6. Implement jobs list query with filters
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

### Milestone 6: Fit Analysis and Flagged Jobs
**Goal:** Surface likely good-fit roles in the dashboard.

**Steps:**
1. Design fit scoring algorithm (compare job vs resume skills/experience)
2. Implement fit scoring service (score 1-100)
3. Add fit analysis trigger on job creation
4. Store fit score and rationale in job record
5. Implement auto-close logic (jobs below 60 set to "not a match")
6. Add fit flag logic (threshold for "flagged" status)
7. Display fit score in table column
8. Add fit badge/color coding (high score vs low score)
9. Show fit rationale in job detail drawer
10. Test fit analysis with sample jobs and resume
11. Verify jobs receive fit results
12. Verify dashboard clearly shows flagged jobs
13. Verify auto-close logic works for low-fit jobs

**Exit Criteria:**
- Jobs receive fit results
- Dashboard clearly shows flagged jobs
- Auto-close logic works for jobs below 60 score

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
15. Verify critical user flow works from login to flagged jobs in dashboard
16. Resolve major known blockers

**Exit Criteria:**
- Critical user flow works from login to flagged jobs in dashboard
- Major known blockers resolved
