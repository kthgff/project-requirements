# JobTrackr Development Plan

## Overview
JobTrackr is a personal job search tracker that connects to Gmail, identifies job-related emails, extracts structured opportunity data, and gives the user a web app to manage their job pipeline.

This development plan captures the recommended technical stack, architecture, locked MVP product decisions, and milestone sequencing for the first implementation slice.

## Recommended Stack
- Frontend: Next.js with React and TypeScript
- Backend API: Go with Gin
- Database: PostgreSQL
- Database access: pgx + sqlc
- Styling/UI: Tailwind CSS + shadcn/ui
- Authentication: Google OAuth
- Gmail integration: Gmail API
- Background processing: Go worker running scheduled Gmail polling jobs
- Local development: Docker Compose
- API contract: OpenAPI
- Error tracking and observability: Sentry + structured logs
- Hosting:
  - Frontend: Vercel
  - API: Fly.io, Railway, or Render
  - Database: Neon, Supabase Postgres, or AWS RDS

## Locked MVP Product Decisions
These decisions are now treated as canonical for implementation and should remain aligned with `specs/jobtrackr-pm-decision-memo-2026-04-19.md`.

### Job state model
- `status` is workflow state only
- supported statuses: `new`, `interested`, `applied`, `interviewing`, `offer`, `rejected`
- `saved` is a boolean flag
- archive state is represented by `archived_at`
- UI views are derived from those fields instead of introducing `archived` as a workflow status

### Manual job entry
- out of MVP scope
- parsed jobs remain editable after ingestion

### Recruiter outreach
- in MVP scope when the email clearly references a specific open role

### Duplicate handling
- exact external job ID match is a duplicate
- exact normalized job URL match is a duplicate
- exact normalized `company + title + location` match is a duplicate
- ambiguous near-matches do not auto-merge in MVP

### Parsed-job creation threshold
Create a job only when at least one of the following is true:
- external job ID exists
- normalized job URL exists
- title and company both exist
- title, location, and source platform exist with medium-or-higher extraction confidence

Otherwise persist source email only.

### Gmail connection and sync defaults
- OAuth scopes: `openid`, `email`, `profile`, `https://www.googleapis.com/auth/gmail.readonly`
- initial sync window: last 30 days
- incremental sync cadence: every 10 minutes
- on-demand sync is available in MVP UI and rate-limited per user

### Search and filter semantics
- keyword search is case-insensitive substring match over title, company, and description snippet
- filters compose with AND logic across filter types
- repeated values within a filter use OR logic
- date filters are inclusive and interpreted in the user-facing timezone
- location filtering is case-insensitive exact match on normalized location

## Why This Stack
- Go is a strong fit for backend ingestion, polling, parsing, and deduplication work
- React and Next.js provide a fast path to a polished user-facing dashboard
- PostgreSQL is sufficient for the MVP data model and can support lightweight job scheduling early on
- sqlc + pgx keeps data access explicit, type-safe, and performant
- this architecture avoids premature complexity while leaving room to split services later if needed

## High-Level Architecture

### Frontend responsibilities
The web app is responsible for:
- sign-in entry points
- dashboard views
- job list and job detail experiences
- notes, tags, search, and filtering
- account and Gmail connection settings
- manual sync visibility and trigger

### Backend responsibilities
The Go API is responsible for:
- Google OAuth callback handling
- session validation
- encrypted token storage
- Gmail sync orchestration
- email detection and parsing
- deduplication
- parsed-job update APIs
- tags, notes, and filter metadata
- operational and debug endpoints

### Worker responsibilities
The worker can initially run within the same Go service and be separated later if needed.

Responsibilities:
- scheduled Gmail polling
- token refresh handling
- ingestion retries
- parsing pipeline execution
- ingestion logging and observability

## Suggested Repository Structure
A monorepo is recommended for MVP speed and shared context.

```text
/
├── apps/
│   ├── web/
│   └── api/
├── packages/
│   └── api-client/
├── infra/
├── docs/
└── README.md
```

## Core Data Model

### users
- id
- email
- name
- image
- created_at
- updated_at

### sessions
- id
- user_id
- session_token
- expires_at
- created_at

### gmail_accounts
- id
- user_id
- google_account_id
- email
- access_token_encrypted
- refresh_token_encrypted
- token_expires_at
- scopes
- state
- last_history_id
- last_synced_at
- disconnected_at
- last_error_code
- last_error_message
- created_at
- updated_at

### gmail_messages
- id
- user_id
- gmail_account_id
- gmail_message_id
- gmail_thread_id
- subject
- from_email
- from_name
- snippet
- raw_text
- internal_date
- raw_label_ids
- source_platform
- classification
- detection_reason
- parse_status
- parse_error
- processed_at
- created_at

### jobs
- id
- user_id
- title
- company
- location
- description_snippet
- application_link
- recruiter_name
- recruiter_email
- salary
- work_mode
- employment_type
- seniority
- external_job_id
- status
- saved
- notes
- fit_flag
- fit_score
- fit_summary
- date_received
- discovered_at
- last_seen_at
- archived_at
- created_at
- updated_at

### gmail_message_jobs
- gmail_message_id
- job_id
- created_at

### job_tags
- id
- user_id
- name

### job_tag_links
- job_id
- tag_id

### job_activities
- id
- job_id
- type
- body
- created_at

### ingestion_runs
- id
- user_id
- gmail_account_id
- started_at
- finished_at
- status
- messages_scanned
- messages_matched
- jobs_created
- jobs_attached
- errors_count
- error_summary
- created_at

## Delivery Strategy
Implementation should move in milestones that each produce a usable proof point and reduce downstream ambiguity.

## Milestone 0. Contract lock and implementation package
Purpose:
- freeze the MVP decision package before code starts
- ensure API, schema, and pipeline assumptions are internally consistent

Dependencies:
- product requirements
- Gmail ingestion spec
- API contract
- PM decision memo

Outputs:
- updated `projects/jobtrackr/api-contract.md`
- updated `projects/jobtrackr/development-plan.md`
- explicit first-slice milestone package

Exit criteria:
- no unresolved P0 blockers remain for the first vertical slice

## Milestone 1. Repository scaffolding and local developer loop
Purpose:
- establish the repo, local infra, and baseline CI needed for all later work

Dependencies:
- `projects/jobtrackr/scaffolding-plan.md`

Implementation package:
- create monorepo layout with `apps/web`, `apps/api`, `packages/api-client`, `infra`, and `docs`
- scaffold Next.js web app
- scaffold Go API service with Gin
- add root task runner and per-app Make/package scripts
- add Docker Compose for Postgres
- add env templates and config loading
- add health endpoints
- add migration tooling and sqlc setup
- add baseline CI for build, lint, and type/test checks

Primary deliverables:
- repo boots locally with one infra command and one dev command
- API can connect to Postgres
- web and API can run together in local development

Exit criteria:
- a new developer can clone, start infra, run migrations, and boot both apps without manual setup beyond env values

## Milestone 2. OAuth and authenticated session flow
Purpose:
- prove the app can sign in a user and persist an authenticated web session

Dependencies:
- Milestone 1
- Google OAuth app credentials

Implementation package:
- create `users` and `sessions` tables
- implement OAuth state handling
- implement `POST /auth/google/start`
- implement `GET /auth/google/callback`
- implement secure session cookies and auth middleware
- implement `GET /auth/session` and `POST /auth/logout`
- build login page and post-auth redirect flow

Primary deliverables:
- user can complete Google sign-in
- session persists across page loads
- frontend can load current authenticated user

Exit criteria:
- authenticated browser session works end to end in local and preview environments

## Milestone 3. Gmail account persistence and reconnect semantics
Purpose:
- persist Gmail account state correctly and expose stable reconnect behavior before ingestion starts

Dependencies:
- Milestone 2
- finalized Gmail scope and account-state model

Implementation package:
- create `gmail_accounts` table
- encrypt and store Gmail tokens server-side
- persist granted scopes, token expiry, last sync state, and reconnect metadata
- implement `GET /gmail/account`
- implement `POST /gmail/connect/start` if connect is separated from login
- implement `POST /gmail/disconnect`
- handle `connected`, `disconnected`, `expired`, `revoked`, and `denied` states consistently
- build settings/account UI for connection status, reconnect, and disconnect

Primary deliverables:
- Gmail account persistence is stable
- user-visible reconnect state is deterministic
- disconnect preserves imported data while disabling further sync

Exit criteria:
- the UI and API agree on Gmail connection state for all expected auth edge cases

## Milestone 4. Raw Gmail ingestion and run observability
Purpose:
- complete the smallest end-to-end ingestion slice before job parsing complexity is added

Dependencies:
- Milestone 3
- `projects/jobtrackr/gmail-auth-ingestion-implementation-package.md`

Implementation package:
- create `ingestion_runs`, `ingestion_errors`, and `gmail_messages` tables
- implement Gmail client wrapper for list and fetch operations
- implement candidate query bounded to the MVP initial sync window
- persist only `job_alert`, `uncertain`, and pipeline-failed candidates
- implement classification result and detection reason storage
- implement `POST /sync/run`
- implement `GET /sync/runs` and `GET /sync/runs/:id`
- implement `GET /messages` and `GET /messages/:id`
- build dashboard cards for latest run, sync status, and recent ingested messages

Primary deliverables:
- manual sync produces observable ingestion runs
- raw Gmail messages are persisted without duplicate reprocessing
- failures are visible without aborting the entire run

Exit criteria:
- the team can inspect account state, latest runs, and stored candidate messages through the UI and API

## Milestone 5. Parsed job creation, dedupe, and first job surfaces
Purpose:
- turn raw candidate ingestion into reliable job creation with deterministic MVP rules

Dependencies:
- Milestone 4
- locked parsed-job threshold and dedupe semantics

Implementation package:
- create `jobs` and `gmail_message_jobs` tables
- implement parsed-job creation threshold rules
- implement exact-match dedupe based on external job ID, normalized URL, and normalized company-title-location
- persist `discovered_at` and `last_seen_at`
- support one-message-to-many-jobs linkage
- expose `GET /jobs`, `GET /jobs/:id`, `PATCH /jobs/:id`, `POST /jobs/:id/archive`, and `POST /jobs/:id/unarchive`
- build initial list and detail views for parsed jobs

Primary deliverables:
- relevant candidate emails create editable jobs when the threshold is met
- duplicate alerts attach to existing jobs deterministically
- users can inspect and update parsed jobs

Exit criteria:
- the first true user-facing job-tracking slice works end to end

## Milestone 6. Search, tags, notes, and workflow views
Purpose:
- complete the day-to-day job management surface for MVP

Dependencies:
- Milestone 5

Implementation package:
- add tag tables and tag endpoints
- add notes support if separate endpoint is still needed
- add saved flag editing and UI
- add inbox, all jobs, saved, and archived views derived from canonical fields
- implement keyword search, date filters, location filters, and status filters using locked semantics
- add filter metadata endpoint and UI wiring

Primary deliverables:
- users can manage jobs efficiently across the main dashboard views
- frontend and backend share deterministic filter behavior

Exit criteria:
- the core personal job-management workflow is complete without manual data entry

## Milestone 7. MVP hardening
Purpose:
- improve reliability, observability, and trust once the core workflow is functional

Dependencies:
- Milestone 6

Implementation package:
- improve retry behavior and failure recovery
- add replay/reprocess support for debug workflows
- improve parser coverage for common job boards and recruiter email patterns
- add fit analysis trigger and mirrored fit fields
- tighten logging, metrics, and error reporting
- document runbooks and operational checks

Primary deliverables:
- MVP is stable enough for ongoing personal use
- debugging and recovery paths exist for the most common ingestion failures

Exit criteria:
- the product is ready for sustained daily use on a live inbox

## First Vertical Slice Dependency Summary
For the first slice, sequencing should be treated as:
1. repo scaffolding
2. auth and session flow
3. Gmail account persistence
4. raw ingestion and sync observability

That slice is complete only when the following dependency chain works end to end:
- Google OAuth credentials configured
- database migrations applied
- session cookie issued after auth
- Gmail account row stored with encrypted tokens
- manual sync creates an ingestion run
- candidate Gmail messages persist without duplication
- UI displays connection state, latest run, and recent messages

## Recommended Implementation Order
1. Milestone 1, repository scaffolding and local developer loop
2. Milestone 2, OAuth and authenticated session flow
3. Milestone 3, Gmail account persistence and reconnect semantics
4. Milestone 4, raw Gmail ingestion and run observability
5. Milestone 5, parsed job creation, dedupe, and first job surfaces
6. Milestone 6, search, tags, notes, and workflow views
7. Milestone 7, MVP hardening

## Key Risks
- Gmail token and refresh edge cases may complicate onboarding
- rule-based parsing quality may vary widely across job sources
- duplicate detection may be harder than initial extraction
- multi-job digest traceability will create complexity if message-to-job linkage is modeled too narrowly

## Mitigation Strategy
- keep the first slice focused on auth, account persistence, and raw ingestion
- retain source email traceability for all candidate messages and created jobs
- prefer deterministic exact-match dedupe over aggressive merge behavior in MVP
- keep one-message-to-many-jobs support in the data model from the start
- keep architecture simple until actual scale or failure patterns demand more infrastructure

## Immediate Build-Ready Deliverables
The implementation handoff should now include:
- `projects/jobtrackr/api-contract.md`
- `projects/jobtrackr/development-plan.md`
- `projects/jobtrackr/scaffolding-plan.md`
- `projects/jobtrackr/gmail-auth-ingestion-implementation-package.md`
- `specs/jobtrackr-pm-decision-memo-2026-04-19.md`
