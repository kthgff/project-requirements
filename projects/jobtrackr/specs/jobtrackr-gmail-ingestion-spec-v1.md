# JobTrackr Gmail Ingestion Spec v1

## Purpose

Define how JobTrackr scans Gmail for job alert emails, identifies relevant opportunities, extracts job references, enriches job details, and stores normalized records for MVP.

## Goal

Convert inbound Gmail job alert emails into structured tracked jobs with minimal manual work.

## Scope

In scope for MVP:
- connect to a Gmail inbox
- scan inbox emails for job alerts
- support all relevant job alert emails, including LinkedIn and Indeed
- classify relevant vs irrelevant emails
- extract one or more job opportunities from a relevant email
- enrich job details when the email lacks enough information
- store source email and normalized job data
- deduplicate repeated alerts for the same job

Out of scope for MVP:
- sending email
- responding to recruiter messages
- applying to jobs automatically
- ingesting non-email job sources directly

---

## High-Level Flow

1. Connect to Gmail inbox
2. Fetch candidate emails
3. Classify whether each email is a job alert email
4. Store source email record
5. Extract one or more job opportunities from the email
6. Enrich each job with fuller details
7. Deduplicate against existing jobs
8. Create or update job records
9. Trigger fit analysis against active resume

---

## Gmail Access Requirements

### Authentication
- Use OAuth with Gmail API
- Request exactly these MVP scopes:
  - `openid`
  - `email`
  - `profile`
  - `https://www.googleapis.com/auth/gmail.readonly`
- Store tokens securely

### Initial sync
- On first connection, scan a bounded historical window
- MVP default: last 30 days
- This window is not user-configurable in MVP

### Incremental sync
- After initial sync, ingest newly received emails on a recurring basis
- MVP default: poll every 10 minutes
- Support a user-triggered on-demand sync in MVP UI via the authenticated sync endpoint

---

## Candidate Email Selection

The system should not run full extraction against every inbox message if basic filtering can reduce noise.

### Operator discovery workflow
For local debugging and ingestion-query validation, the operator may use `gog gmail search` before running the API sync. The canonical repo helper is `apps/api/scripts/gog-discover-job-emails.sh`, which should mirror the same Gmail query and max-results defaults used by the API sync environment.

This discovery step is read-only. It is meant to answer "which Gmail messages would this query return right now?" before the write path runs. Persistence still happens only in the API sync flow, where matching messages are stored as source-email records and then linked to parsed jobs when applicable.

### Recommended pre-filters
Use one or more of:
- inbox placement
- sender/domain patterns
- subject line keywords
- body keywords/snippets
- category/promotions handling if useful but not required

### Examples of likely relevant sources
- LinkedIn job alerts
- Indeed alerts
- other job boards
- recruiter outreach that clearly references an open role

### Examples of likely irrelevant messages
- general newsletters
- marketing emails unrelated to job opportunities
- social notifications without a job opening
- application outcome emails if they do not describe a new opportunity

---

## Job Alert Classification

Each candidate email should be classified as:
- `job_alert`
- `not_job_alert`
- `uncertain`

### Classification inputs
- sender domain and display name
- subject line
- snippet
- body text
- presence of job links
- presence of repeated patterns typical of alerts

### MVP behavior
- `job_alert`: proceed to extraction
- `not_job_alert`: mark email as non-job and stop
- `uncertain`: persist for debug visibility, attempt extraction only when a usable job link or equivalent strong identifier is present, otherwise stop after source-email persistence

---

## Source Email Storage

For every candidate email that is `job_alert`, `uncertain`, or fails after entering the pipeline, store a `source_email` record with:
- Gmail message id
- thread id
- from name
- from address
- subject
- received timestamp
- snippet
- raw text if available
- source platform
- classification result
- detection reason when available

Reason:
- debugging
- traceability
- future model tuning

---

## Job Extraction Requirements

A relevant email may contain one or more jobs.

### Extraction targets
For each detected job, try to extract:
- company name
- job title
- job URL
- location
- work mode if available
- compensation if available
- job summary or description snippet
- source platform
- external job id if present

### Common patterns
- single job alert with one role
- digest email with multiple jobs
- recruiter email with role details in free text
- job board email with cards/links to multiple roles

### MVP recommendation
Support both:
- single-job emails
- multi-job digest emails

If extraction confidence is low:
- store a parsed job record only when at least one of the following is true:
  - external job ID exists
  - normalized job URL exists
  - title and company both exist
  - title, location, and source platform exist with medium-or-higher extraction confidence
- otherwise keep only the source email record

---

## Enrichment Requirements

Emails often do not contain full job detail.

### Enrichment objective
Given extracted job references, retrieve fuller job detail before saving or finalizing the job record.

### Enrichment inputs
- job URL
- job board metadata
- extracted company/title/location values
- external job id when available

### Enrichment outputs
Try to gather:
- full or richer job description
- normalized company/title/location
- work mode
- salary if available
- employment type
- seniority if available

### Fallback behavior
If enrichment fails:
- save the job with whatever reliable data exists from the email
- mark missing fields as null
- do not block ingestion entirely

---

## Deduplication Rules

The system should avoid creating obvious duplicates when multiple alerts reference the same role.

### Matching signals, strongest to weakest
1. exact external job id
2. exact job URL after normalization
3. high-confidence canonical match on company + title + location

### Recommended behavior
If duplicate found:
- update existing job record
- update `last_seen_at`
- attach the new source email linkage
- fill null fields
- replace lower-confidence extracted fields with higher-confidence values
- do not overwrite user-edited notes, tags, `saved`, archive state, or manual status changes
- do not create a new active job row

If no duplicate found:
- create a new job record

If match confidence is ambiguous:
- do not auto-merge in MVP
- create a new record
- optional internal duplicate-review metadata is allowed if cheap, but no user-facing review flow is required in MVP

---

## Persistence Rules

### On relevant email with at least one extracted job
- store or update `source_email`
- create or update `job`
- set `discovered_at` on first creation
- update `last_seen_at` whenever seen again

### On relevant email with no extractable job
- store `source_email`
- mark extraction outcome for debugging

### On irrelevant email
- do not persist `source_email` by default in MVP

MVP retention rule:
- store `source_email` for `job_alert`, `uncertain`, and pipeline-failed candidates only

---

## Triggering Fit Analysis

After a job record is created or materially updated:
- load the active uploaded resume
- run fit analysis
- store `job_fit_analysis`
- mirror latest fit values onto `job`

If no active resume exists:
- ingest the job anyway
- mark fit fields as pending or null

---

## Failure Handling

The ingestion pipeline should fail gracefully.

### Failure cases
- Gmail auth failure
- malformed email content
- email classified relevant but extraction fails
- enrichment source unavailable
- DB write failure

### MVP expectations
- log the failure reason
- continue processing other emails when possible
- avoid losing the original candidate email context
- support replay/reprocessing later

---

## Suggested Internal Pipeline Stages

1. `fetch_candidates`
2. `classify_job_alert`
3. `store_source_email`
4. `extract_jobs`
5. `enrich_job`
6. `dedupe_job`
7. `upsert_job`
8. `run_fit_analysis`

This separation will make debugging and later improvements easier.

---

## Functional Requirements

### FR1. Gmail connection
The system shall connect to Keith's Gmail inbox using read access.

### FR2. Historical scan
The system shall support an initial historical scan window.

### FR3. Incremental ingestion
The system shall ingest newly received candidate emails after initial sync.

### FR4. Job alert classification
The system shall classify candidate emails as relevant or not relevant to job tracking.

### FR5. Multi-job extraction
The system shall support extracting multiple jobs from a single email when applicable.

### FR6. Partial record support
The system shall allow partially populated job records when complete detail is unavailable.

### FR7. Job enrichment
The system shall attempt to enrich extracted jobs with fuller details.

### FR8. Deduplication
The system shall avoid obvious duplicate tracked jobs.

### FR9. Persistence
The system shall persist source email data and normalized job records.

### FR10. Fit trigger
The system shall trigger fit analysis after job creation or meaningful job update when a resume is available.

---

## Non-Functional Requirements

- ingestion should be idempotent where practical
- failures on one email should not stop the full ingestion run
- source traceability should be preserved for debugging
- pipeline should support future expansion to more job sources

---

## Remaining Open Questions

- What enrichment source is authoritative when email data conflicts with linked page data?
