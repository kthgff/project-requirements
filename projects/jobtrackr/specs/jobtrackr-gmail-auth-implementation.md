# JobTrackr Gmail Auth and Ingestion Implementation Package

## Purpose
Define the first-pass implementation package that turns the Gmail auth and ingestion specs into concrete backend modules, migrations, endpoints, and execution order.

This package is intentionally scoped to the smallest vertical slice that gets:
- Google OAuth connected
- Gmail account stored
- raw Gmail messages ingested
- ingestion runs persisted
- the system ready for later extraction into jobs

## Scope of This Package
In scope:
- auth/session foundation
- Gmail account connection
- manual sync trigger
- raw Gmail message fetch and persistence
- ingestion run tracking
- minimal frontend surfaces for connection and sync visibility

Out of scope in this package:
- full job extraction quality
- enrichment against linked job pages
- fit analysis
- advanced dedupe
- full jobs dashboard

## External Interfaces to Implement
Derived from API contract and ingestion spec.

### Auth endpoints
- `POST /api/v1/auth/google/start`
- `GET /api/v1/auth/google/callback`
- `POST /api/v1/auth/logout`
- `GET /api/v1/auth/session`

### Gmail endpoints
- `GET /api/v1/gmail/account`
- `POST /api/v1/gmail/connect/start`
- `POST /api/v1/gmail/disconnect`

### Sync endpoints
- `POST /api/v1/sync/run`
- `GET /api/v1/sync/runs`
- `GET /api/v1/sync/runs/:id`

### Debug endpoints for first slice
- `GET /api/v1/messages`
- `GET /api/v1/messages/:id`

## Gmail OAuth Flow

### Recommended MVP approach
Use Google OAuth once, requesting both:
- identity scopes for app sign-in
- Gmail read scope for inbox scanning

Recommended practical scopes:
- `openid`
- `email`
- `profile`
- `https://www.googleapis.com/auth/gmail.readonly`

### Flow steps
1. user clicks connect/sign in in frontend
2. frontend calls `POST /auth/google/start`
3. backend creates OAuth state and returns Google auth URL
4. browser redirects to Google
5. Google redirects to `GET /auth/google/callback`
6. backend validates state and exchanges code for tokens
7. backend upserts `users`
8. backend upserts `gmail_accounts`
9. backend creates app session in `sessions`
10. backend sets HTTP-only cookie and redirects to frontend dashboard

## Required Tables for This Package

### 1. users
Needed for app identity.

Minimum fields:
- `id`
- `email`
- `name`
- `image_url`
- `created_at`
- `updated_at`

### 2. sessions
Needed for secure browser auth.

Minimum fields:
- `id`
- `user_id`
- `session_token`
- `expires_at`
- `created_at`

### 3. gmail_accounts
Needed for Gmail token storage and sync state.

Minimum fields:
- `id`
- `user_id`
- `google_account_id`
- `email`
- `access_token_encrypted`
- `refresh_token_encrypted`
- `token_expires_at`
- `scopes`
- `last_history_id`
- `last_synced_at`
- `disconnected_at`
- `created_at`
- `updated_at`

### 4. ingestion_runs
Needed for traceability and UI visibility.

Minimum fields:
- `id`
- `user_id`
- `gmail_account_id`
- `status`
- `started_at`
- `finished_at`
- `messages_scanned`
- `messages_matched`
- `jobs_created`
- `jobs_attached`
- `errors_count`
- `error_summary`
- `created_at`

### 5. ingestion_errors
Needed for message-level and stage-level failures.

Minimum fields:
- `id`
- `ingestion_run_id`
- `gmail_message_id`
- `stage`
- `error_code`
- `error_message`
- `metadata`
- `created_at`

### 6. gmail_messages
Needed to persist source email records before extraction.

Minimum fields:
- `id`
- `user_id`
- `gmail_account_id`
- `gmail_message_id`
- `gmail_thread_id`
- `subject`
- `from_name`
- `from_email`
- `snippet`
- `raw_text`
- `internal_date`
- `raw_label_ids`
- `source_platform`
- `classification`
- `detection_reason`
- `parse_status`
- `parse_error`
- `processed_at`
- `job_id`
- `created_at`

## Migration Package Order

### Migration 1
Create:
- enums for `ingestion_run_status`
- enums for `message_parse_status`
- enums for `message_classification`

### Migration 2
Create:
- `users`
- `sessions`
- `gmail_accounts`

### Migration 3
Create:
- `ingestion_runs`
- `ingestion_errors`

### Migration 4
Create:
- `gmail_messages`
- indexes for message lookup by account, message id, parse status, internal date

### Migration 5
Create optional initial `jobs` table only if the team wants raw-ingestion-plus-placeholder-job creation in the same slice

Recommendation:
- do not require `jobs` for the first auth + raw ingestion slice
- make raw message persistence the acceptance target first

## Backend Modules and Responsibilities

### auth package
Owns:
- state nonce generation
- Google OAuth URL creation
- callback exchange
- session token creation
- auth middleware

### gmail package
Owns:
- Gmail API service construction
- list candidate messages
- fetch full message bodies
- normalize headers/body into internal DTOs
- identify likely source platform from sender/domain

### ingest package
Owns:
- create ingestion run
- fetch candidate messages from Gmail
- classify candidate emails
- upsert raw source email records
- write run summary and failures

### classification package
Owns:
- classify candidate messages as `job_alert`, `not_job_alert`, or `uncertain`
- attach detection reason strings for observability

### messages package
Owns:
- list ingested messages
- fetch one message detail for debug UI

### store package
Owns:
- sqlc query wrappers
- transaction orchestration for callback and ingestion flows

## Execution Flow for the First Vertical Slice

### A. Auth and connect flow
1. frontend requests auth URL
2. backend generates state and stores short-lived state marker in secure cookie or server-side store
3. callback exchanges code for tokens
4. backend extracts Google profile identity
5. backend upserts user
6. backend encrypts and stores Gmail tokens in `gmail_accounts`
7. backend creates session row and cookie
8. frontend loads session and Gmail account state

### B. Manual sync flow
1. frontend calls `POST /sync/run`
2. backend authenticates current session
3. backend loads active Gmail account and refresh token if needed
4. backend creates `ingestion_runs` row with status `running`
5. backend calls Gmail list API using bounded query window
6. backend fetches full message data for candidates
7. backend classifies each email
8. backend upserts `gmail_messages`
9. backend writes per-message errors when needed
10. backend completes `ingestion_runs` row with counts and status
11. frontend fetches updated sync run and message list

## Candidate Selection for MVP
Apply cheap filters before storing everything.

### Recommended initial Gmail query
- bound by recent window, default 30 days for initial run
- narrowed by likely job terms where practical

Example direction:
- `newer_than:30d (job OR jobs OR recruiter OR interview OR opportunity OR linkedin OR indeed)`

### Per-message classifier result
For each fetched candidate store one of:
- `job_alert`
- `not_job_alert`
- `uncertain`

MVP rule:
- persist `job_alert` and `uncertain`
- optional persistence for `not_job_alert` only when useful for tuning

## Source Platform Detection
At raw-ingestion stage, attempt to identify:
- `linkedin`
- `indeed`
- `recruiter_direct`
- `other_job_board`
- `unknown`

Signal inputs:
- sender domain
- display name
- known URL domains in body

This is useful before full extraction exists.

## Concrete sqlc Query Groups

### auth.sql
- `GetUserByEmail`
- `UpsertUser`
- `CreateSession`
- `GetSessionByToken`
- `DeleteSessionByToken`

### gmail_accounts.sql
- `UpsertGmailAccount`
- `GetActiveGmailAccountByUserID`
- `DisconnectGmailAccount`
- `UpdateGmailAccountSyncState`

### ingestion_runs.sql
- `CreateIngestionRun`
- `CompleteIngestionRun`
- `IncrementIngestionRunCounters`
- `ListIngestionRunsByUserID`
- `GetIngestionRunByID`
- `CreateIngestionError`

### gmail_messages.sql
- `UpsertGmailMessage`
- `ListGmailMessagesByUserID`
- `GetGmailMessageByID`
- `ExistsGmailMessageByProviderID`

## Minimal Frontend Surfaces for This Package

### Login page
Needs:
- sign in with Google button
- loading and error handling

### Dashboard shell
Needs:
- authenticated layout
- Gmail connected or not connected status
- manual sync trigger
- latest sync run summary
- recent ingested messages list

### Settings page
Needs:
- connected Gmail account email
- last synced time
- reconnect button
- disconnect button

## Acceptance Criteria for the Slice

### Auth
- user can start Google OAuth
- callback creates a user and session
- Gmail account record is persisted securely
- session endpoint returns authenticated user and Gmail connection state

### Ingestion
- user can trigger a sync
- sync creates an ingestion run row
- relevant candidate Gmail messages are stored as `gmail_messages`
- failures are recorded without aborting the whole run
- sync run status and counts are visible

### UI
- frontend shows connected Gmail state
- frontend can trigger sync manually
- frontend can display recent runs and message previews

## Smallest Vertical Slice Execution Order

### Order 1. Foundation
- scaffold Next.js app and Go API
- add Docker Compose Postgres
- add migrations and sqlc
- add config and health endpoints

### Order 2. Auth core
- implement Google OAuth config
- implement `/auth/google/start`
- implement `/auth/google/callback`
- implement session cookies and `/auth/session`
- implement logout

### Order 3. Gmail account persistence
- create `gmail_accounts` migration
- encrypt/store tokens
- add `GET /gmail/account`
- add `POST /gmail/disconnect`

### Order 4. Raw ingestion
- create `ingestion_runs`, `ingestion_errors`, `gmail_messages`
- implement Gmail list and fetch wrappers
- implement classifier
- implement `/sync/run`
- persist run + messages + errors

### Order 5. Debug visibility
- implement `/sync/runs`
- implement `/sync/runs/:id`
- implement `/messages`
- implement `/messages/:id`
- add dashboard cards for connection and latest run

## Recommended Non-Goals for This Package
Do not mix these into the first slice unless strictly necessary:
- multi-job extraction beyond message storage
- HTML-heavy parsing edge cases
- job enrichment HTTP scraping
- duplicate merge UX
- full jobs dashboard
- fit analysis trigger

## Risks and Guardrails

### Risk
Trying to create jobs too early will bloat the first slice.

Guardrail:
- make raw email persistence the proof point first

### Risk
OAuth and Gmail token handling can become tangled.

Guardrail:
- centralize token encryption and account persistence in one auth/account service

### Risk
Polling may reprocess the same emails.

Guardrail:
- unique constraint on `(gmail_account_id, gmail_message_id)`
- record sync checkpoints early

### Risk
Frontend can get blocked waiting on final dashboard features.

Guardrail:
- expose a simple dashboard with connection and sync observability first
