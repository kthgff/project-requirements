# JobTrackr Development Plan

## Overview
JobTrackr is a personal job search tracker that connects to Gmail, identifies job-related emails, extracts structured opportunity data, and gives the user a web app to manage their job pipeline.

This development plan captures the recommended technical stack, architecture, core product decisions, and phased implementation path for the MVP.

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

## Why This Stack
- Go is a strong fit for backend ingestion, polling, parsing, and deduplication work
- React and Next.js provide a fast path to a polished user-facing dashboard
- PostgreSQL is sufficient for the MVP data model and can support lightweight job scheduling early on
- sqlc + pgx keeps data access explicit, type-safe, and performant
- This architecture avoids premature complexity while leaving room to split services later if needed

## Recommended Product Decisions
These decisions should be locked early to keep implementation simple and flexible.

### Saved
- Model `saved` as a boolean flag
- Do not represent saved as a primary workflow status

### Archived
- Model archived as an `archived_at` timestamp or archive flag
- Do not represent archived as a primary workflow status in the core data model
- It can still appear as an archived view in the UI

### Manual Job Entry
- Include manual job entry in MVP as a lightweight fallback
- This protects usability when parsing fails or a job did not originate from Gmail

### Duplicate Handling
- Use soft duplicate detection in MVP
- Prevent obvious duplicates when confidence is high
- Avoid aggressive automatic merge behavior
- Prefer flags or review markers over irreversible merges

### Update Emails
- Attach update emails to a basic job activity timeline when match confidence is high
- Otherwise retain them as source emails for traceability without forcing an incorrect association

## High-Level Architecture

### Frontend Responsibilities
The web app is responsible for:
- sign-in entry points
- dashboard views
- job list and job detail experiences
- notes, tags, search, and filtering
- account and Gmail connection settings

### Backend Responsibilities
The Go API is responsible for:
- Google OAuth callback handling
- session validation
- encrypted token storage
- Gmail sync orchestration
- email detection and parsing
- deduplication
- CRUD operations for jobs, tags, notes, and related metadata
- operational and debug endpoints

### Worker Responsibilities
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
│   ├── web/        # Next.js frontend
│   └── api/        # Go backend
├── packages/
│   └── types/      # optional shared types or generated API models
├── infra/          # docker, deployment, env templates
├── docs/           # architecture, parsing rules, API docs
└── README.md
```

A simpler alternative is:

```text
/
├── web/
├── api/
├── infra/
└── docs/
```

## Core Data Model

### users
- id
- email
- name
- image
- created_at
- updated_at

### gmail_accounts
- id
- user_id
- google_account_id
- email
- access_token_encrypted
- refresh_token_encrypted
- token_expires_at
- scopes
- last_synced_at

### gmail_messages
- id
- user_id
- gmail_message_id
- gmail_thread_id
- subject
- from_email
- from_name
- snippet
- received_at
- raw_label_ids
- processed_at
- detection_result
- parse_status
- job_id nullable

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
- status
- saved
- notes
- source_message_id nullable
- date_received
- archived_at nullable
- created_at
- updated_at

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
- started_at
- finished_at
- status
- messages_scanned
- jobs_created
- jobs_matched
- errors_count

## Development Phases

### Phase 1: Foundation
- create repository structure
- scaffold Next.js frontend
- scaffold Go API
- set up Docker Compose for local Postgres
- add config management and health checks
- add migrations and CI

Deliverable:
- frontend and API run locally
- database is connected
- baseline developer workflow works

### Phase 2: Authentication and Gmail Connection
- implement Google OAuth
- support user sign-in
- request Gmail scopes
- securely store tokens
- support reconnect and disconnect flows

Deliverable:
- user can sign in and connect Gmail successfully

### Phase 3: Ingestion Pipeline
- implement scheduled Gmail polling
- fetch recent messages incrementally
- store message metadata and snippets
- track processed message IDs
- log ingestion runs and failures

Deliverable:
- system ingests candidate emails without reprocessing the same messages repeatedly

### Phase 4: Detection and Parsing
- implement rule-based job email detection
- implement parsing heuristics
- extract structured fields
- create or match job records
- add duplicate detection logic

Deliverable:
- relevant job emails reliably create editable job records

### Phase 5: Job Management UI
- build dashboard shell
- build job list and job detail views
- add status editing
- add notes and tags
- add inbox, all jobs, saved, and archived views
- add search and filters

Deliverable:
- user can actively manage a job search pipeline in the web app

### Phase 6: MVP Hardening
- add retry behavior and better ingestion observability
- improve duplicate review workflow
- add manual job entry
- add activity timeline support
- improve parser coverage for common recruiter and job board patterns
- add operational debug tools

Deliverable:
- MVP is stable enough for ongoing personal use

## Recommended Implementation Order
1. frontend and API scaffolding
2. auth and session flow
3. database schema and migrations
4. Gmail connection flow
5. raw Gmail message ingestion
6. detection rules
7. parsing logic
8. jobs list UI
9. job detail editing
10. search and filter support
11. notes and tags
12. dedupe improvements
13. manual job entry
14. hardening and observability

## Key Risks
- Gmail token and refresh edge cases may complicate onboarding
- Rule-based parsing quality may vary widely across job sources
- Duplicate detection may be harder than initial extraction
- Update emails may be difficult to associate correctly with an existing job

## Mitigation Strategy
- keep parsing editable in the UI
- retain source email traceability for all job records
- log ingestion and parsing outcomes clearly
- prefer soft duplicate handling over destructive merge behavior in MVP
- keep architecture simple until actual scale or failure patterns demand more infrastructure

## Immediate Next Deliverables
The next technical documents that should be created are:
- technical architecture spec
- database schema spec
- API contract draft
- repository scaffolding plan
