# JobTrakr Engineering Handoff v1

## Purpose

Provide an engineer-ready implementation handoff for the JobTrakr MVP based on the approved PM specs.

## Product Summary

JobTrakr is a single-user job search assistant that:
- authenticates the user with Google login
- scans Gmail for job alert emails
- extracts and enriches job records
- stores normalized job data
- compares each job to the uploaded resume
- surfaces match rating and low-fit indicators as fit signals, not workflow-state changes
- presents all jobs in a dashboard table UI

## Recovery Note for Preserved Handoff Guidance

This file is preserved handoff context, not the primary kickoff entrypoint.

Implementation kickoff redirect:
- `projects/jobtrackr/DEVELOPMENT_PLAN.md`
- `projects/jobtrackr/PROJECT.md`
- `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`

Recovery note:
- If a kickoff prompt or older preserved note still points to `~/Documents/project-requirements/DEVELOPMENT_PLAN.md`, treat that as stale external drift only.
- Recover in this order: `~/Documents/project-requirements/projects/jobtrackr/DEVELOPMENT_PLAN.md`, `~/Documents/project-requirements/projects/jobtrackr/PROJECT.md`, `~/Documents/project-requirements/projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`.
- Treat bare filenames in preserved notes as historical shorthand only, not live repo-root entrypoints.

Current implementation slice:
- Next.js web plus Go API on the current auth -> session -> Gmail readonly connect -> persisted jobs UI lane

Current lane ownership for this slice:
- Alice: frontend delivery on the auth -> session -> Gmail readonly connect -> persisted-jobs experience
- Marcus: frontend recovery-entrypoint and kickoff-doc alignment so frontend pickup stays safe during hourly handoffs
- Frank: source-of-truth maintenance across roadmap-facing and preserved planning docs so the live kickoff set and current slice wording stay aligned
- remaining SWE lanes: backend and shared contract follow-through

Shared frontend ownership note:
- Alice and Marcus are the frontend owners for the current slice.

Shared Alice lane note:
- Alice: frontend delivery on the auth -> session -> Gmail readonly connect -> persisted-jobs experience

## Locked Product Decisions

- implementation language direction: TypeScript for the web app and shared typed contracts, with the current backend delivery slice implemented as a Go API
- authentication: Google federated login
- login page required
- primary input source: Gmail inbox job alert emails
- relevant alert sources include LinkedIn, Indeed, and other relevant job alert emails
- resume source: uploaded file
- main UI: dashboard with jobs table
- good-fit behavior: surface fit signals in the UI, do not auto-apply in MVP
- initial workflow statuses:
  - `new`
  - `interested`
  - `applied`
  - `interviewing`
  - `offer`
  - `rejected`
- `saved` is a separate boolean, not a status
- archive state is `archivedAt`, not a workflow value
- fit fields stay nullable until analysis runs
- one source email may link to multiple jobs

## Recommended MVP Technical Direction

This section is preserved historical guidance. For live implementation pickup, prefer the current Phase 3 handoff package and development plan.

### Frontend
- Next.js
- React
- TypeScript

### Backend
- Go API for the current delivery slice

### Core integrations
- Google authentication
- Gmail read access
- database for normalized jobs and resume metadata
- AI fit analysis service

## Recommended Vertical Slice Strategy

Build the MVP in narrow end-to-end slices rather than subsystem silos.

### Slice 1. Auth shell
Goal:
- user can log in with Google and reach a protected dashboard shell

### Slice 2. Dashboard with mock jobs
Goal:
- authenticated user can see dashboard layout and jobs table with mock data

### Slice 3. Real job list persistence
Goal:
- dashboard reads real job records from database

### Slice 4. Gmail raw ingestion
Goal:
- system can scan Gmail and persist source email plus parsed job records

### Slice 5. Resume upload
Goal:
- user can upload a resume and persist active resume state

### Slice 6. Fit analysis
Goal:
- system compares jobs against resume and surfaces match rating plus low-fit indicators without mutating workflow state

### Slice 7. Detail and workflow controls
Goal:
- user can inspect a job in detail and update status

---

## Recommended Build Order

1. Google login page and protected app shell
2. Dashboard page with table and placeholder states
3. Database schema for users, resumes, source emails, jobs, fit analysis, and status history
4. Read jobs from database into dashboard
5. Resume upload flow
6. Gmail connection and inbox scan trigger
7. Job extraction and persistence pipeline
8. Job enrichment and deduplication behavior
9. Fit analysis pipeline and fit display
10. Job detail drawer and status update interactions
11. Filter and search refinement
12. QA pass and launch checklist

---

## Dependency Map

### Auth dependencies
Needed before:
- protected dashboard
- user-specific data reads
- resume upload
- Gmail integration

### Data model dependencies
Needed before:
- real dashboard list
- ingestion persistence
- fit analysis persistence
- status updates

### Gmail ingestion dependencies
Needed before:
- automated job population
- meaningful dashboard data without mocks

### Resume dependencies
Needed before:
- fit analysis
- match rating and low-fit signals in the dashboard

### Fit analysis dependencies
Needed before:
- match rating and low-fit signal display in the table
- fit summary in the detail drawer

---

## Frontend Delivery Plan

### FE-1. Login page
Deliver:
- `/login`
- Google sign-in button
- loading and error states

### FE-2. Protected shell
Deliver:
- auth gate
- redirect unauthenticated users to login
- top navigation shell

### FE-3. Dashboard layout
Deliver:
- page header
- filters row
- empty states
- account/logout controls

### FE-4. Jobs table
Deliver:
- required columns
- default sorting
- loading and empty states
- mock data first, real data second

### FE-5. Detail drawer
Deliver:
- row click or title click opens detail
- fit summary
- source link
- job description area
- status control

### FE-6. Resume upload entry point
Deliver:
- resume state indicator
- upload action
- post-upload state refresh

---

## Backend Delivery Plan

### BE-1. User and session support
Deliver:
- Google auth callback handling
- user record creation
- session enforcement

### BE-2. Data schema
Deliver:
- tables/collections for user, resume, source_email, job, job_fit_analysis, job_status_history

### BE-3. Job query APIs
Deliver:
- authenticated list endpoint for dashboard
- single-job detail endpoint
- status update endpoint

### BE-4. Resume upload APIs
Deliver:
- upload endpoint
- active resume retrieval

### BE-5. Gmail ingestion pipeline
Deliver:
- inbox scan trigger
- candidate email classification
- extraction
- persistence

### BE-6. Enrichment and deduplication
Deliver:
- enrichment lookup step
- duplicate detection and upsert behavior

### BE-7. Fit analysis pipeline
Deliver:
- resume-to-job analysis
- fit persistence
- mirrored fit fields for dashboard query speed

---

## QA Focus Areas

- login success/failure behavior
- protected route enforcement
- Gmail permission and auth edge cases
- duplicate alert handling
- jobs with partial extracted data
- resume missing vs present fit behavior
- status update persistence
- dashboard empty, loading, and filtered states

---

## Risks

- Gmail auth and Gmail read permissions may create onboarding friction
- extraction quality may vary by email format
- enrichment quality depends on source page consistency
- fit analysis quality depends on resume parsing quality

---

## Files Engineering Should Use

When older docs conflict, use these current files first:

- `projects/jobtrackr/DEVELOPMENT_PLAN.md`
- `projects/jobtrackr/PROJECT.md`
- `projects/jobtrackr/specs/jobtrackr-pm-decision-memo-2026-04-19.md`
- `projects/jobtrackr/specs/jobtrackr-gate-a-reconciliation-matrix-2026-04-20.md`
- `projects/jobtrackr/specs/jobtrackr-api-contract.md`
- `projects/jobtrackr/specs/jobtrackr-detail-view-contract-2026-04-20.md`
- `projects/jobtrackr/specs/jobtrackr-list-detail-contract-examples-2026-04-20.md`
- `projects/jobtrackr/specs/jobtrackr-workspace-ux-contract-2026-04-20.md`
- `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`

---

## Immediate Recommendation

The mock-first shell guidance below is preserved historical context.

For live pickup, engineering should start with the current smallest meaningful vertical slice:
- Google login
- protected dashboard shell
- Gmail readonly connection state
- persisted jobs retrieval in the jobs UI

Use the Phase 3 handoff package and active development plan when current pickup guidance conflicts with the older build-order notes in this file.

Guardrails:
- workflow statuses are `new`, `interested`, `applied`, `interviewing`, `offer`, `rejected`
- match rating and low-fit are fit semantics, not workflow states
- shortlist/save and archive behavior must stay separate from workflow status
