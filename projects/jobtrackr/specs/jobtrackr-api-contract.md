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
- login starts with a user-supplied Google email address
- OAuth callback must verify the Google profile email matches the supplied email before session creation
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

Request:
```json
{
  "email": "user@example.com"
}
```

Behavior:
- validates email format
- stores normalized email with OAuth state for callback verification
- returns Google auth URL when valid

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
- verifies Google profile email matches the email supplied to `/auth/google/start`
- creates or updates user/session
- creates or updates Gmail account when Gmail scope is granted
- redirects to the frontend app

Response:
- HTTP redirect to app route
- HTTP redirect to login with a retryable `account_mismatch` style error when the returned Google account does not match the supplied email

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
      "googleAccountId": "google-sub-or-account-id",
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

## Search Profile Endpoints

Search profiles are user-scoped saved filters and scheduled discovery instructions. MVP supports unlimited profiles, daily scheduled runs, pause/re-enable behavior, and no manual run-now endpoint.

### GET /search-profiles
Returns search profiles for the authenticated user.

Response:
```json
{
  "data": [
    {
      "id": "uuid",
      "name": "Remote Senior Product Roles",
      "titleQuery": "product manager",
      "locationQuery": "United States",
      "remotePreference": "remote",
      "includedCompanies": [],
      "excludedCompanies": ["Example Corp"],
      "salaryMin": 140000,
      "salaryMax": null,
      "seniority": "senior",
      "employmentTypes": ["full_time"],
      "includeKeywords": ["platform"],
      "excludeKeywords": ["contract"],
      "enabled": true,
      "cadence": "daily",
      "timezone": "America/Chicago",
      "lastRunAt": "2026-05-03T07:00:00Z",
      "nextRunAt": "2026-05-04T07:00:00Z"
    }
  ]
}
```

### POST /search-profiles
Creates a search profile.

Request:
```json
{
  "name": "Remote Senior Product Roles",
  "titleQuery": "product manager",
  "locationQuery": "United States",
  "remotePreference": "remote",
  "includedCompanies": [],
  "excludedCompanies": ["Example Corp"],
  "salaryMin": 140000,
  "salaryMax": null,
  "seniority": "senior",
  "employmentTypes": ["full_time"],
  "includeKeywords": ["platform"],
  "excludeKeywords": ["contract"],
  "timezone": "America/Chicago"
}
```

Behavior:
- creates an enabled profile by default
- defaults cadence to `daily`
- schedules the next run for the overnight daily sourcing window
- may accept an app-suggested name, but the saved name is user-editable

### PATCH /search-profiles/:id
Updates editable profile criteria or name.

Behavior:
- preserves run history
- recalculates `nextRunAt` when schedule-relevant fields change
- does not remove existing job match labels

### POST /search-profiles/:id/disable
Pauses a profile without deleting it.

Behavior:
- sets `enabled` to false
- excludes the profile from future scheduled discovery
- preserves saved-filter access and historical job match labels

### POST /search-profiles/:id/enable
Re-enables a paused profile.

Behavior:
- sets `enabled` to true
- schedules the next daily run

### Search Profile API Exclusions
MVP does not expose:
- manual run-now endpoint
- notification subscription endpoint for newly found jobs
- hard delete requirement for profiles

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
- `searchProfile` one or many repeated profile ids, OR semantics within the search-profile filter
- `dateFrom` inclusive date lower bound in user-facing timezone
- `dateTo` inclusive date upper bound in user-facing timezone
- `source` one or many repeated params for sourced-job provenance (`gmail`, `indeed`, `linkedin`), OR semantics within the source filter
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
        "sourceCount": 2,
        "sources": ["linkedin", "gmail"],
        "matchedProfiles": [
          {
            "id": "uuid",
            "name": "Remote Senior Product Roles",
            "enabled": true
          }
        ],
        "firstSeenAt": "2026-04-18T13:00:00Z",
        "lastSeenAt": "2026-05-02T16:30:00Z",
        "lastSourcedAt": "2026-05-02T16:30:00Z",
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
- `source` is included in the list payload because it is a required dashboard column and maps to the primary source label
- sourced-job provenance may add `sourceCount`, `sources`, `firstSeenAt`, `lastSeenAt`, and `lastSourcedAt` without changing workflow, fit, saved, or archive semantics
- source filters compose with existing filters using AND across filter families and OR within repeated source values
- `matchedProfiles` shows every search profile that matched the job, including disabled profiles when they are preserved as historical labels
- search-profile filters compose with existing filters using AND across filter families and OR within repeated profile ids
- canonical sourced-job grid/drawer/detail continuity rules live in `specs/jobtrackr-sourced-jobs-frontend-continuity-contract-2026-05-02.md`
- canonical list-to-detail examples for row selection, drawer continuity, and detail handoff live in `specs/jobtrackr-list-detail-contract-examples-2026-04-20.md`

### GET /jobs/:id
Returns a single job detail.

Interaction and section-order expectations for this payload are defined in `specs/jobtrackr-detail-view-contract-2026-04-20.md`.
Concrete list-row to detail-response examples, selection continuity expectations, and drawer versus full-page parity examples are defined in `specs/jobtrackr-list-detail-contract-examples-2026-04-20.md`.
Sourced-job provenance display, merged-source explanation, and source-filter continuity expectations are defined in `specs/jobtrackr-sourced-jobs-frontend-continuity-contract-2026-05-02.md`.
Use those files as the canonical example set for list-to-detail continuity, selected-row behavior, sourced-job provenance, and edit-flow expectations.

Response:
```json
{
  "data": {
    "id": "uuid",
    "title": "Backend Engineer",
    "company": "Example Inc",
    "location": "Chicago, IL",
    "source": "linkedin",
    "sourceCount": 2,
    "sources": ["linkedin", "gmail"],
    "matchedProfiles": [
      {
        "id": "uuid",
        "name": "Remote Senior Product Roles",
        "enabled": true,
        "matchedAt": "2026-05-02T12:00:00Z"
      },
      {
        "id": "uuid",
        "name": "AI Platform Engineering",
        "enabled": false,
        "matchedAt": "2026-05-02T12:30:00Z"
      }
    ],
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

### GET /source-emails
Returns persisted Gmail source-email provenance records for operator and QA debug review. This is the implemented T-108 read path; older `/messages` naming is historical shorthand and should not be used as the current contract.

Query params:
- `matchedAsJob` optional boolean; `true` returns records linked to at least one persisted job, `false` returns records without job linkage
- `fromEmail` optional case-insensitive sender filter
- `search` optional case-insensitive search across sender, subject, and snippet/body preview fields
- `limit` optional result cap for recent records

Response:
```json
{
  "data": [
    {
      "gmailMessageId": "18f0...",
      "gmailThreadId": "19ab...",
      "subject": "New opportunity",
      "fromName": "Jane Doe",
      "fromEmail": "recruiter@example.com",
      "snippet": "New backend engineer role...",
      "body": "Full or normalized message body...",
      "receivedAt": "2026-04-19T19:58:00Z",
      "matchedAsJob": true,
      "parseError": null,
      "processedAt": "2026-04-19T20:01:00Z",
      "createdAt": "2026-04-19T20:00:59Z",
      "updatedAt": "2026-04-19T20:01:00Z"
    }
  ],
  "meta": {
    "limit": 10,
    "matchedAsJob": true,
    "fromEmail": "recruiter",
    "search": "engineer"
  }
}
```

QA usage for T-095/T-108:
- use `/api/v1/source-emails?matchedAsJob=true` to find persisted messages that produced or attached to jobs
- use `/api/v1/source-emails?matchedAsJob=false` to inspect persisted candidate messages that did not create jobs
- use `fromEmail`, `search`, and `limit` to narrow fixture-backed sign-off before falling back to PostgreSQL for join-table assertions

Classification handling for MVP:
- job-alert or uncertain candidate mail that enters the ingestion pipeline should remain inspectable through this source-email read path
- clearly irrelevant mail is not persisted by default unless it entered the pipeline and failed later

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
