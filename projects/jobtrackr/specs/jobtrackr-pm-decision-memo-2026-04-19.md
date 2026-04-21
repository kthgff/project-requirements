# JobTrackr PM Decision Memo — 2026-04-19

## Purpose
Resolve the remaining P0 product decisions blocking MVP implementation and align the engineering handoff around one canonical set of behaviors.

## Source Inputs
- `projects/jobtrackr/specs/jobtrackr-api-contract.md`
- `projects/jobtrackr/specs/jobtrackr-requirements.md`
- `projects/jobtrackr/specs/jobtrackr-gmail-ingestion-spec-v1.md`
- `projects/jobtrackr/specs/jobtrackr-spec-clarifications-needed.md`

## Current kickoff references
For implementation pickup, pair this memo with:
- `projects/jobtrackr/DEVELOPMENT_PLAN.md`
- `projects/jobtrackr/PROJECT.md`
- `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`

Active implementation slice:
- Google auth -> session -> Gmail readonly connect -> persisted jobs UI

## Decisions

### 1. Canonical job state model
Use three separate concepts:
- `status`: workflow state only
- `saved`: boolean flag for shortlist behavior
- `archivedAt`: nullable timestamp for archive behavior

#### Workflow statuses for MVP
- `new`
- `interested`
- `applied`
- `interviewing`
- `offer`
- `rejected`

`archived` is not a workflow status in the canonical data model.

#### View semantics
- `inbox`: jobs where `archivedAt IS NULL` and `status = new`
- `all jobs`: jobs where `archivedAt IS NULL`
- `saved`: jobs where `archivedAt IS NULL` and `saved = true`
- `archived`: jobs where `archivedAt IS NOT NULL`

### 2. Minimum threshold for creating a parsed job
Create a parsed job record only when at least one of the following is true:
- an external job ID is present
- a normalized job URL is present
- both title and company are present
- title, location, and source platform are present with medium-or-higher extraction confidence

Otherwise:
- persist the source email only
- mark extraction outcome for debug review

### 3. Deduplication and ambiguous match rules
#### Definitive duplicate signals
Treat the candidate as the same job when either is true:
1. exact external job ID match
2. exact normalized job URL match

#### Fallback duplicate signal
Treat the candidate as the same job only when all of the following match exactly after normalization:
- company
- title
- location

#### Ambiguous cases
If the record is close but not exact after normalization:
- do not auto-merge in MVP
- create a new job record
- store duplicate-review metadata internally for future debugging if cheap, but do not require a user-facing duplicate review flow in MVP

#### Duplicate update behavior
When attaching a new source to an existing job:
- update source linkage
- update `lastSeenAt`
- fill null fields
- replace lower-confidence extracted fields with higher-confidence values
- do not overwrite user-edited notes, tags, `saved`, archive state, or manual status changes

### 4. Gmail scopes and connection-state semantics
#### OAuth scopes for MVP
Request:
- `openid`
- `email`
- `profile`
- `https://www.googleapis.com/auth/gmail.readonly`

Do not request broader Gmail scopes in MVP.

#### Gmail connection state model
Expose:
- `connected`: boolean
- `needsReconnect`: boolean
- `state`: enum
- `lastSyncedAt`: nullable timestamp
- `lastErrorCode`: nullable string
- `lastErrorMessage`: nullable string

#### Allowed `state` values
- `connected`
- `disconnected`
- `expired`
- `revoked`
- `denied`

Semantics:
- `connected`: valid account and usable tokens
- `disconnected`: user intentionally disconnected Gmail
- `expired`: token refresh or credential lifecycle requires reconnect
- `revoked`: Google access has been revoked or invalidated
- `denied`: user declined required Gmail permission during connect/reconnect flow

`needsReconnect = true` for `expired` and `revoked`, and may also be true for `denied` when the user should retry.

### 5. Sync defaults
#### Initial sync window
- default to the last 30 days
- not user-configurable in MVP UI

#### Incremental sync cadence
- poll every 10 minutes for MVP

#### On-demand sync
- support `POST /sync/run`
- available for authenticated users in MVP UI as a manual refresh action
- rate-limit per user
- return an accepted/running response rather than blocking

### 6. Search and filter semantics
#### Keyword search
- case-insensitive
- simple substring match for MVP
- search only `title`, `company`, and `descriptionSnippet`
- do not search notes or tags in MVP

#### Filter logic
- combine filters with AND logic
- repeated multi-value params within the same filter use OR semantics inside that filter
  - example: `status=applied&status=interviewing`

#### Date filtering
- `dateFrom` and `dateTo` are inclusive
- persisted timestamps stay in UTC
- filtering uses the user-facing timezone for day-boundary interpretation

#### Location filtering
- case-insensitive exact match on normalized location string for MVP

#### Sorting
- use explicit sort only
- default sort is `dateReceived:desc`
- do not introduce fuzzy relevance ranking in MVP

### 7. Recruiter outreach scope
Recruiter outreach is in MVP scope only when the email clearly references a specific open role or opportunity.

### 8. Manual job entry scope
Manual job entry is out of MVP scope.

Implications:
- remove `POST /jobs` from the MVP API contract
- allow editing of parsed jobs, but do not expose manual job creation in the initial build

### 9. Fit-analysis nullability and reanalysis triggers
#### Nullability
Use:
- `fitFlag = null`
- `fitScore = null`
- `fitSummary = null`

when no analysis exists.

#### Reanalysis triggers
Rerun fit analysis when any of these change:
- title
- company
- location
- work mode
- employment type
- salary
- description or requirements text
- seniority
- active resume

Do not rerun for changes to:
- notes
- tags
- `saved`
- `archivedAt`
- `status`

### 10. Source-email persistence for MVP
Persist `source_email` records for:
- `job_alert`
- `uncertain`
- any candidate that entered the pipeline and failed later

Do not persist clearly irrelevant mail by default in MVP.

### 11. Sync run statuses
Use the canonical run-status enum:
- `queued`
- `running`
- `completed`
- `partial`
- `failed`
- `canceled`

### 12. Source message to job linkage
A single source email may link to multiple jobs.

Implications:
- debug endpoints must support one-message-to-many-jobs linkage
- job detail may show one or more source messages

## Engineering impact summary
Engineering can now proceed with:
- a schema where archive is separate from workflow status
- explicit parsed-job creation thresholds
- deterministic MVP dedupe behavior
- finalized Gmail scope and reconnect semantics
- finalized sync window and cadence defaults
- deterministic search/filter behavior
- a smaller MVP that excludes manual job creation

## Contract update checklist
Update the API contract to reflect:
1. status enum without `archived`
2. `saved` boolean plus `archivedAt` timestamp semantics
3. Gmail account `state` and reconnect metadata
4. canonical sync run statuses
5. repeated multi-value filter semantics
6. removal of `POST /jobs`
7. one-message-to-many-jobs debug linkage
8. fit nullability behavior on job responses where fit data appears

- Implementation language direction: **TypeScript**
