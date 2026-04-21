# JobTrackr API Contract Draft

## Purpose
This document defines the MVP API contract for JobTrackr.

The API supports:
- authentication and session flow
- Gmail connection and sync visibility
- parsed job management
- notes, tags, search, and filters
- operational visibility for ingestion

This contract incorporates the PM decisions in `specs/jobtrackr-pm-decision-memo-2026-04-19.md`.

## API Principles
- REST-style JSON endpoints
- versioned routes under `/api/v1`
- cookie-based authenticated web session
- consistent error format
- conservative, explicit request and response shapes

## Base URL
```text
/api/v1
```

## Authentication Model
- user signs in with Google OAuth
- backend creates a secure session cookie
- frontend sends browser requests with credentials enabled
- Gmail tokens are stored server-side only
- OAuth requests the following scopes for MVP:
  - `openid`
  - `email`
  - `profile`
  - `https://www.googleapis.com/auth/gmail.readonly`

## Standard Response Conventions

### Success Envelope
```json
{
  "data": {}
}
```

### Error Envelope
```json
{
  "error": {
    "code": "string_code",
    "message": "Human-readable message",
    "details": {}
  }
}
```

## Auth Endpoints

### POST /auth/google/start
Starts Google OAuth flow.

Response:
```json
{
  "data": {
    "authUrl": "https://accounts.google.com/..."
  }
}
```

### GET /auth/google/callback
Handles OAuth callback.

Behavior:
- validates state
- exchanges code for tokens
- creates or updates user/session
- creates or updates Gmail account when Gmail scope is granted
- redirects to the frontend app

Response:
- HTTP redirect to app route

### POST /auth/logout
Clears authenticated session.

Response:
```json
{
  "data": {
    "success": true
  }
}
```

### GET /auth/session
Returns current authenticated user session.

Response:
```json
{
  "data": {
    "user": {
      "id": "uuid",
      "email": "user@example.com",
      "name": "Keith",
      "imageUrl": "https://..."
    },
    "gmailConnected": true
  }
}
```

## Gmail Account Endpoints

### GET /gmail/account
Returns Gmail connection state for the current user.

Response:
```json
{
  "data": {
    "connected": true,
    "needsReconnect": false,
    "state": "connected",
    "email": "user@gmail.com",
    "lastSyncedAt": "2026-04-19T21:00:00Z",
    "tokenExpiresAt": "2026-04-19T22:00:00Z",
    "lastErrorCode": null,
    "lastErrorMessage": null,
    "grantedScopes": [
      "openid",
      "email",
      "profile",
      "https://www.googleapis.com/auth/gmail.readonly"
    ]
  }
}
```

Allowed `state` values:
- `connected`
- `disconnected`
- `expired`
- `revoked`
- `denied`

### POST /gmail/connect/start
Starts Gmail authorization flow if separated from app auth.

Response:
```json
{
  "data": {
    "authUrl": "https://accounts.google.com/..."
  }
}
```

### POST /gmail/disconnect
Disconnects Gmail access while preserving imported data.

Response:
```json
{
  "data": {
    "success": true
  }
}
```

## Sync Endpoints

Canonical run statuses:
- `queued`
- `running`
- `completed`
- `partial`
- `failed`
- `canceled`

### POST /sync/run
Triggers an on-demand sync for the authenticated user.

Notes:
- rate-limited per user
- exposed in MVP UI as a manual refresh action
- returns quickly rather than blocking until full completion

Response:
```json
{
  "data": {
    "runId": "uuid",
    "status": "queued"
  }
}
```

### GET /sync/runs
Returns recent ingestion runs.

Query params:
- `limit` optional

Response:
```json
{
  "data": [
    {
      "id": "uuid",
      "status": "completed",
      "startedAt": "2026-04-19T20:00:00Z",
      "finishedAt": "2026-04-19T20:00:12Z",
      "messagesScanned": 40,
      "messagesMatched": 8,
      "jobsCreated": 5,
      "jobsAttached": 2,
      "errorsCount": 1,
      "errorSummary": null
    }
  ]
}
```

### GET /sync/runs/:id
Returns details for one ingestion run.

Response:
```json
{
  "data": {
    "id": "uuid",
    "status": "partial",
    "startedAt": "2026-04-19T20:00:00Z",
    "finishedAt": "2026-04-19T20:00:12Z",
    "messagesScanned": 40,
    "messagesMatched": 8,
    "jobsCreated": 5,
    "jobsAttached": 2,
    "errorsCount": 1,
    "errorSummary": "1 parse failure",
    "errors": [
      {
        "id": "uuid",
        "stage": "parse",
        "errorCode": "parse_failed",
        "errorMessage": "Could not extract company",
        "metadata": {}
      }
    ]
  }
}
```

## Jobs Endpoints

### Canonical job model
- `status` is workflow state only
- `saved` is a boolean flag
- `archivedAt` controls archive state

Supported status values:
- `new`
- `interested`
- `applied`
- `interviewing`
- `offer`
- `rejected`

### GET /jobs
Returns a filtered list of non-archived or archived jobs.

Query params:
- `q` keyword search, case-insensitive substring over `title`, `company`, and `descriptionSnippet`
- `status` one or many repeated params, OR semantics within the status filter
- `location` case-insensitive exact match after normalization
- `saved` boolean
- `archived` boolean
- `tag` one or many repeated params, OR semantics within the tag filter
- `dateFrom` inclusive date lower bound in user-facing timezone
- `dateTo` inclusive date upper bound in user-facing timezone
- `page`
- `pageSize`
- `sort` default `dateReceived:desc`

Filter composition rules:
- different filter families combine with AND logic
- repeated values within the same filter family combine with OR logic

Example:
```text
/jobs?q=designer&status=applied&status=interviewing&saved=true&page=1&pageSize=20
```

Response:
```json
{
  "data": {
    "items": [
      {
        "id": "uuid",
        "title": "Senior Product Designer",
        "company": "Acme",
        "location": "Remote",
        "source": "linkedin",
        "status": "applied",
        "saved": true,
        "dateReceived": "2026-04-18T13:00:00Z",
        "archivedAt": null,
        "fitState": "pending",
        "fitFlag": null,
        "fitScore": null,
        "fitSummary": null,
        "tags": ["priority", "remote"]
      }
    ],
    "page": 1,
    "pageSize": 20,
    "total": 1
  }
}
```

List payload notes:
- `fitScore` is the user-facing match rating when present
- `fitFlag` stays as the quick triage boolean
- `fitState` supports `pending`, `scored`, or `unavailable` so the UI can distinguish missing analysis from a low score
- `source` is included in the list payload because it is a required dashboard column
- canonical list-to-detail examples for row selection, drawer continuity, and detail handoff live in `specs/jobtrackr-list-detail-contract-examples-2026-04-20.md`

### GET /jobs/:id
Returns a single job detail.

Interaction and section-order expectations for this payload are defined in `specs/jobtrackr-detail-view-contract-2026-04-20.md`.
Concrete list-row to detail-response examples, selection continuity expectations, and drawer versus full-page parity examples are defined in `specs/jobtrackr-list-detail-contract-examples-2026-04-20.md`.
Use that file as the single canonical example set for list-to-detail continuity, selected-row behavior, and edit-flow expectations.

Response:
```json
{
  "data": {
    "id": "uuid",
    "title": "Backend Engineer",
    "company": "Example Inc",
    "location": "Chicago, IL",
    "source": "linkedin",
    "descriptionSnippet": "Go backend role",
    "applicationLink": "https://example.com/jobs/123",
    "recruiterName": "Jane Doe",
    "recruiterEmail": "jane@example.com",
    "salaryText": "$150k-$180k",
    "status": "new",
    "saved": false,
    "notes": "Interesting infra scope",
    "dateReceived": "2026-04-19T20:00:00Z",
    "archivedAt": null,
    "fitState": "scored",
    "fitFlag": null,
    "fitScore": null,
    "fitSummary": null,
    "tags": [
      {
        "id": "uuid",
        "name": "networking"
      }
    ],
    "activities": [
      {
        "id": "uuid",
        "type": "source_email_attached",
        "body": "New source email linked to job",
        "createdAt": "2026-04-19T20:01:00Z"
      }
    ],
    "sourceMessages": [
      {
        "id": "uuid",
        "gmailMessageId": "18f0...",
        "subject": "Your application was received",
        "fromEmail": "jobs@example.com",
        "receivedAt": "2026-04-19T19:58:00Z"
      }
    ]
  }
}
```

### PATCH /jobs/:id
Updates one or more editable fields on a parsed job.

Request:
```json
{
  "title": "Senior Backend Engineer",
  "status": "applied",
  "saved": true,
  "notes": "Applied through referral"
}
```

Response:
```json
{
  "data": {
    "success": true
  }
}
```

### POST /jobs/:id/archive
Archives a job.

Response:
```json
{
  "data": {
    "success": true,
    "archivedAt": "2026-04-19T21:00:00Z"
  }
}
```

### POST /jobs/:id/unarchive
Restores an archived job.

Response:
```json
{
  "data": {
    "success": true,
    "archivedAt": null
  }
}
```

## Notes Endpoints
For MVP simplicity, notes may live directly on the job record. These endpoints remain optional if `PATCH /jobs/:id` is sufficient.

### PUT /jobs/:id/notes
Replaces the job notes field.

Request:
```json
{
  "notes": "Recruiter said timeline is next week"
}
```

Response:
```json
{
  "data": {
    "success": true
  }
}
```

## Tag Endpoints

### GET /tags
Returns all user tags.

Response:
```json
{
  "data": [
    {
      "id": "uuid",
      "name": "remote"
    },
    {
      "id": "uuid",
      "name": "priority"
    }
  ]
}
```

### POST /tags
Creates a reusable tag.

Request:
```json
{
  "name": "priority"
}
```

Response:
```json
{
  "data": {
    "id": "uuid",
    "name": "priority"
  }
}
```

### POST /jobs/:id/tags
Adds one or more tags to a job.

Request:
```json
{
  "tagIds": ["uuid1", "uuid2"]
}
```

Alternative request shape:
```json
{
  "tagNames": ["priority", "remote"]
}
```

Response:
```json
{
  "data": {
    "success": true
  }
}
```

### DELETE /jobs/:id/tags/:tagId
Removes a tag from a job.

Response:
```json
{
  "data": {
    "success": true
  }
}
```

## Filter Metadata Endpoints

### GET /filters
Returns filter options and counts used by the dashboard.

The initial dashboard should support fit-signal filtering and match-rating sorting without requiring a separate ratings endpoint. Filter labels should use canonical user-facing wording such as `strong-fit`, `low-fit`, and `pending-fit`, even if implementation stores separate nullable fit fields under the hood.

Response:
```json
{
  "data": {
    "statuses": [
      { "value": "new", "count": 8 },
      { "value": "applied", "count": 12 }
    ],
    "fitStates": [
      { "value": "strong-fit", "count": 6 },
      { "value": "fit-ready", "count": 18 },
      { "value": "pending-fit", "count": 4 }
    ],
    "locations": [
      { "value": "Remote", "count": 14 }
    ],
    "tags": [
      { "id": "uuid", "name": "priority", "count": 3 }
    ],
    "sorts": [
      "dateReceived:desc",
      "dateReceived:asc",
      "company:asc",
      "company:desc",
      "status:asc",
      "status:desc",
      "fitScore:desc",
      "fitScore:asc"
    ]
  }
}
```

## Admin and Debug Endpoints
These may be hidden from normal UI but are useful during MVP.

### GET /messages
Returns ingested Gmail messages for debug review.

Query params:
- `classification`
- `parseStatus`
- `jobLinked`
- `limit`

Response:
```json
{
  "data": [
    {
      "id": "uuid",
      "gmailMessageId": "18f0...",
      "subject": "New opportunity",
      "fromEmail": "recruiter@example.com",
      "classification": "job_alert",
      "parseStatus": "partial",
      "processedAt": "2026-04-19T20:01:00Z",
      "linkedJobIds": ["uuid1", "uuid2"]
    }
  ]
}
```

Allowed `classification` values:
- `job_alert`
- `not_job_alert`
- `uncertain`

Classification handling for MVP:
- `job_alert`: proceed through extraction and dedupe
- `uncertain`: attempt extraction only when a usable job link or equivalent strong identifier is present, otherwise persist for debug review without creating a job
- `not_job_alert`: do not create a job and do not persist the message unless it had already entered the pipeline and failed later

### GET /messages/:id
Returns one ingested message and parsing metadata.

Response:
```json
{
  "data": {
    "id": "uuid",
    "gmailMessageId": "18f0...",
    "gmailThreadId": "19ab...",
    "subject": "New opportunity",
    "fromName": "Jane Doe",
    "fromEmail": "recruiter@example.com",
    "snippet": "I wanted to reach out...",
    "classification": "job_alert",
    "parseStatus": "partial",
    "parseError": null,
    "linkedJobs": [
      {
        "id": "uuid",
        "title": "Backend Engineer",
        "company": "Example Inc"
      }
    ]
  }
}
```

## Health Endpoints

### GET /health/live
Response:
```json
{
  "data": {
    "status": "ok"
  }
}
```

### GET /health/ready
Response:
```json
{
  "data": {
    "status": "ok",
    "database": "ok"
  }
}
```

## Validation Rules

### Parsed Job Creation Threshold
A parsed job may be created only when at least one of the following is true:
- external job ID exists
- normalized job URL exists
- title and company both exist
- title, location, and source platform exist with medium-or-higher extraction confidence

Otherwise, persist source email only.

### Source Email Persistence Rules
Persist a `source_email` or `gmail_messages` record for:
- `job_alert`
- `uncertain`
- any candidate that entered the pipeline and failed during later stages

Do not persist clearly irrelevant mail by default in MVP.

### Job Update Rules
- `status` must be one of the supported workflow enum values
- `saved` is a boolean flag
- archive state is represented only by `archivedAt`
- notes are plain text for MVP
- tags are deduplicated case-insensitively

### Deduplication Rules
Treat a candidate as a duplicate when one of the following is true:
1. exact external job ID match
2. exact normalized job URL match
3. exact normalized `company + title + location` match

If the match is not exact after normalization, do not auto-merge in MVP.

### Sync Triggers
- on-demand sync is rate-limited per user
- initial historical window defaults to 30 days
- incremental sync cadence defaults to every 10 minutes

## Error Codes
Recommended initial error codes:
- `unauthorized`
- `forbidden`
- `invalid_request`
- `validation_failed`
- `gmail_not_connected`
- `gmail_reconnect_required`
- `gmail_access_denied`
- `sync_already_running`
- `job_not_found`
- `tag_not_found`
- `internal_error`

Example:
```json
{
  "error": {
    "code": "validation_failed",
    "message": "status is invalid",
    "details": {
      "field": "status"
    }
  }
}
```

## OpenAPI Generation Guidance
The production implementation should generate or maintain an OpenAPI document for these endpoints.

Recommended outputs:
- machine-readable OpenAPI YAML or JSON
- generated TypeScript client types for frontend use
- request/response DTOs versioned with backend handlers

## Future API Extensions
Not needed for MVP, but likely later:
- manual job creation
- richer activity endpoints
- duplicate review endpoints
- parser feedback endpoints
- reminders and follow-up actions
- analytics endpoints
- support for non-Gmail sources
