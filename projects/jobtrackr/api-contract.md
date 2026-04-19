# JobTrackr API Contract Draft

## Purpose
This document defines the initial API contract for the JobTrackr MVP.

The API is designed for a React frontend backed by a Go service. It supports:
- authentication and session flow
- Gmail connection and sync visibility
- job management
- notes, tags, search, and filters
- operational visibility for ingestion

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

## Standard Response Conventions

### Success Envelope
Success responses may return either a direct resource or an envelope. For MVP consistency, use a simple envelope:

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
    "email": "user@gmail.com",
    "lastSyncedAt": "2026-04-19T21:00:00Z",
    "tokenExpiresAt": "2026-04-19T22:00:00Z",
    "needsReconnect": false
  }
}
```

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

### POST /sync/run
Triggers an on-demand sync for the authenticated user.

Notes:
- may be rate-limited
- may be restricted in production UI if only background schedules should run

Response:
```json
{
  "data": {
    "runId": "uuid",
    "status": "running"
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
      "errorsCount": 1
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

### GET /jobs
Returns a filtered list of jobs.

Query params:
- `q` keyword search
- `status` one or many values
- `location`
- `saved` boolean
- `archived` boolean
- `tag` one or many tag names or IDs
- `dateFrom`
- `dateTo`
- `page`
- `pageSize`
- `sort` default `dateReceived:desc`

Example:
```text
/jobs?q=designer&status=applied&saved=true&page=1&pageSize=20
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
        "status": "applied",
        "saved": true,
        "dateReceived": "2026-04-18T13:00:00Z",
        "archivedAt": null,
        "tags": ["priority", "remote"]
      }
    ],
    "page": 1,
    "pageSize": 20,
    "total": 1
  }
}
```

### POST /jobs
Creates a manual job entry.

Request:
```json
{
  "title": "Backend Engineer",
  "company": "Example Inc",
  "location": "Chicago, IL",
  "descriptionSnippet": "Go backend role",
  "applicationLink": "https://example.com/jobs/123",
  "recruiterName": "Jane Doe",
  "recruiterEmail": "jane@example.com",
  "salaryText": "$150k-$180k",
  "status": "new",
  "saved": false,
  "notes": "Found via networking",
  "dateReceived": "2026-04-19T20:00:00Z",
  "tags": ["networking"]
}
```

Response:
```json
{
  "data": {
    "id": "uuid"
  }
}
```

### GET /jobs/:id
Returns a single job detail.

Response:
```json
{
  "data": {
    "id": "uuid",
    "title": "Backend Engineer",
    "company": "Example Inc",
    "location": "Chicago, IL",
    "descriptionSnippet": "Go backend role",
    "applicationLink": "https://example.com/jobs/123",
    "recruiterName": "Jane Doe",
    "recruiterEmail": "jane@example.com",
    "salaryText": "$150k-$180k",
    "status": "new",
    "saved": false,
    "notes": "Found via networking",
    "dateReceived": "2026-04-19T20:00:00Z",
    "archivedAt": null,
    "tags": [
      {
        "id": "uuid",
        "name": "networking"
      }
    ],
    "activities": [
      {
        "id": "uuid",
        "type": "manual_entry_created",
        "body": "Job created manually",
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
Updates one or more editable fields on a job.

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
    "success": true
  }
}
```

## Notes Endpoints
For MVP simplicity, notes may live directly on the job record. These endpoints remain optional if PATCH /jobs/:id is sufficient.

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

Response:
```json
{
  "data": {
    "statuses": [
      { "value": "new", "count": 8 },
      { "value": "applied", "count": 12 }
    ],
    "locations": [
      { "value": "Remote", "count": 14 }
    ],
    "tags": [
      { "id": "uuid", "name": "priority", "count": 3 }
    ]
  }
}
```

## Admin and Debug Endpoints
These may be hidden from normal UI but are useful during MVP.

### GET /messages
Returns ingested Gmail messages for debug review.

Query params:
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
      "detectionResult": true,
      "parseStatus": "partial",
      "processedAt": "2026-04-19T20:01:00Z"
    }
  ]
}
```

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
    "detectionResult": true,
    "parseStatus": "partial",
    "parseError": null,
    "jobId": "uuid"
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

### Job Create and Update
- `status` must be one of supported enum values
- `title` and `company` may be nullable for parsed jobs, but manual creation should require at least one of them
- `dateReceived` may be omitted for manual entry and default to current time
- `tags` must be deduplicated case-insensitively

### Sync Triggers
- on-demand sync should be rate-limited per user
- sync should return accepted/running rather than block until full completion if job duration becomes noticeable

## Error Codes
Recommended initial error codes:
- `unauthorized`
- `forbidden`
- `invalid_request`
- `validation_failed`
- `gmail_not_connected`
- `gmail_reconnect_required`
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
- richer activity endpoints
- duplicate review endpoints
- parser feedback endpoints
- reminders and follow-up actions
- analytics endpoints
- support for non-Gmail sources
