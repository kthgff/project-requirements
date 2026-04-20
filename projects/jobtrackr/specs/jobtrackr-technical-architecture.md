# JobTrackr Technical Architecture

## Purpose
This document defines the recommended technical architecture for the JobTrackr MVP using Go, React, and PostgreSQL.

The architecture is optimized for:
- fast MVP delivery
- maintainability
- low operational complexity
- strong support for Gmail ingestion and structured job tracking

## System Overview
JobTrackr is a single-user-first web application that ingests job-related Gmail messages, extracts structured data, and provides a dashboard for managing job opportunities.

The MVP is composed of three main layers:
- frontend web application
- backend API and worker service
- PostgreSQL database

## High-Level Architecture

```text
+-----------------------+
|   React / Next.js UI  |
|  dashboard + settings |
+-----------+-----------+
            |
            | HTTPS / JSON
            v
+-----------------------+
|      Go API (Gin)     |
| auth, jobs, tags, UI  |
| sync controls, admin  |
+-----------+-----------+
            |
            |
            +------------------------+
            |                        |
            v                        v
+----------------------+   +----------------------+
| PostgreSQL           |   | Worker routines      |
| users, jobs, gmail   |   | Gmail polling        |
| sync state, logs     |   | parsing, dedupe      |
+----------------------+   +----------------------+
            ^                        |
            |                        |
            +-----------+------------+
                        |
                        v
                 +-------------+
                 | Gmail API   |
                 +-------------+
```

## Core Components

### 1. Frontend Web App
Recommended stack:
- Next.js
- React
- TypeScript
- Tailwind CSS
- shadcn/ui

Responsibilities:
- sign-in and account onboarding
- Gmail connection state display
- jobs dashboard and job detail UI
- filters, search, notes, and tags
- archived and saved views
- settings and reconnect flows

Design principles:
- keep state management simple for MVP
- use server-friendly data fetching patterns
- favor small reusable UI components
- optimize for clarity over complex abstractions

### 2. Backend API
Recommended stack:
- Go
- Gin
- pgx
- sqlc

Responsibilities:
- Google OAuth initiation and callback handling
- secure session creation and validation
- Gmail token encryption and persistence
- job CRUD and filtering APIs
- tags, notes, and status updates
- admin/debug endpoints for ingestion visibility
- orchestration hooks for sync execution

API style:
- JSON over HTTPS
- versioned under `/api/v1`
- cookie-based authenticated session for web app
- OpenAPI-documented endpoints

### 3. Worker Execution Layer
Initial approach:
- run worker logic in the same Go service process
- trigger polling on a schedule
- split into a dedicated worker service only if scaling or reliability pressure demands it

Responsibilities:
- poll Gmail on schedule
- refresh Gmail tokens when needed
- fetch candidate emails incrementally
- run detection and parsing rules
- match or create job records
- write ingestion logs and error states

This keeps MVP operations simple while preserving a clean separation in code.

### 4. Database Layer
Recommended database:
- PostgreSQL

Responsibilities:
- persist user, auth, Gmail, job, tag, and ingestion state
- support filtering and search queries efficiently
- provide durable traceability from a job record back to Gmail source metadata

Database design priorities:
- simple normalization
- explicit foreign keys
- index fields used in filtering, deduplication, and sync lookups
- soft-state design rather than destructive merges

## Authentication Architecture

### User Authentication
- user signs in with Google OAuth
- application creates a secure HTTP-only session cookie
- session is used by the frontend for authenticated API access

### Gmail Authorization
- Gmail permissions are requested during or after sign-in
- Gmail access token and refresh token are stored encrypted at rest
- token expiration is tracked in the database
- reconnect flow is supported when tokens expire or become invalid

### Security Requirements
- encrypt Gmail access and refresh tokens before persistence
- restrict Google scopes to the minimum required set
- use secure cookie settings in production
- validate OAuth state and redirect URIs strictly
- avoid exposing tokens to the browser

## Gmail Sync Architecture

### Sync Strategy
MVP should use scheduled polling rather than push notifications.

Polling behavior:
- poll on a configurable interval
- fetch messages newer than the last successful checkpoint
- filter to candidate job-related messages using basic Gmail metadata and content rules
- store raw metadata and snippets for traceability
- skip already-processed Gmail message IDs

### Sync Stages
1. select connected Gmail account
2. refresh token if needed
3. fetch recent messages
4. persist message metadata
5. run job-detection logic
6. run parser for matched messages
7. dedupe against existing jobs
8. create or associate records
9. log result and metrics

### Failure Handling
- failed sync runs must be logged
- one failed message should not block the full sync batch
- parsing failures should still preserve source message metadata
- retries should be bounded and observable

## Parsing Architecture

### MVP Parsing Strategy
Use rule-based detection and extraction first.

Detection inputs:
- sender
- subject keywords
- Gmail labels
- body/snippet patterns

Extracted fields:
- title
- company
- location
- description snippet
- application link
- recruiter name
- recruiter email when available
- salary when available
- date received

### Parser Design Principles
- create a job even if some fields are missing
- preserve editability in UI
- preserve source traceability
- separate detection from extraction in code
- make rules configurable and testable

### Future Extension Path
Later phases may add:
- AI-assisted parsing fallback
- source-specific parsing rules by job board
- richer activity linking from update emails

## Deduplication Strategy
MVP deduplication should be conservative.

Signals:
- Gmail thread ID
- normalized title + company
- sender identity
- application link
- recent matching records

Rules:
- prevent obvious duplicates when confidence is high
- avoid automatic destructive merge logic
- attach likely update emails to an existing job when confidence is strong
- preserve ambiguity for later user review

## Search and Filtering Architecture
MVP search requirements:
- keyword search across title, company, description snippet
- filters for status, location, date received, saved, archived, tags

Implementation approach:
- PostgreSQL queries for exact and partial search
- start with `ILIKE` and indexed filters
- add full-text search later only if needed

## Repository Structure
Recommended monorepo layout:

```text
/
├── apps/
│   ├── web/
│   └── api/
├── packages/
│   └── types/
├── infra/
├── docs/
└── README.md
```

### apps/web
Contains:
- Next.js app router or pages router implementation
- UI components
- dashboard pages
- auth/session-aware client UI

### apps/api
Contains:
- Gin server
- route handlers
- auth logic
- worker scheduling and sync routines
- repositories/queries
- parser and dedupe packages

### infra
Contains:
- Docker Compose
- environment templates
- deployment manifests if needed

### docs
Contains:
- parsing rule documentation
- local development instructions
- integration notes

## Deployment Architecture

### MVP Hosting Model
- frontend on Vercel
- API on Fly.io, Railway, or Render
- PostgreSQL on Neon, Supabase, or AWS RDS

### Environment Separation
Minimum environments:
- local
- staging
- production

Each environment should have:
- distinct Google OAuth credentials where practical
- separate database
- isolated application secrets

## Observability
Minimum observability for MVP:
- structured logs from API and worker routines
- ingestion run records in database
- error tracking via Sentry
- health endpoint for API service

Recommended health endpoints:
- `/health/live`
- `/health/ready`

Recommended tracked events:
- Gmail account connected
- sync run started/completed/failed
- parsing success/failure
- duplicate detection triggered
- job created
- job manually edited

## Testing Strategy

### Backend
- unit tests for parser rules
- unit tests for dedupe logic
- integration tests for database access layer
- integration tests for OAuth callback and protected routes where practical

### Frontend
- component tests for key views if time allows
- end-to-end coverage for sign-in, dashboard, and job editing in a later phase

### Data Safety
- seed fixtures for common recruiter and job board emails
- use anonymized test fixtures only

## Non-Goals for MVP
The following are intentionally excluded from the initial architecture:
- multi-tenant SaaS complexity
- event bus or distributed queue systems
- microservices split
- AI parsing dependency in the critical path
- reminder workflows
- non-Gmail providers

## Architecture Decisions Summary
- Use Next.js for frontend
- Use Go with Gin for API and worker logic
- Use PostgreSQL as the primary data store
- Use sqlc + pgx for explicit typed database access
- Keep worker execution in-process for MVP
- Use scheduled Gmail polling rather than push notifications
- Use conservative deduplication with full traceability
- Keep app auth and Gmail auth both based on Google OAuth for MVP

## Immediate Next Technical Artifacts
- database schema spec
- API contract draft
- repository scaffolding plan
- local development setup spec
