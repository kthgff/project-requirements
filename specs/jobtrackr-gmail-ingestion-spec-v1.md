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
- Request the minimum practical read scopes for inbox scanning
- Store tokens securely

### Initial sync
- On first connection, scan a bounded historical window
- PM recommendation for MVP default: last 30 days

### Incremental sync
- After initial sync, ingest newly received emails on a recurring basis
- PM recommendation for MVP default: poll every 5 to 15 minutes, unless push is easy and reliable

---

## Candidate Email Selection

The system should not run full extraction against every inbox message if basic filtering can reduce noise.

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
- `uncertain`: either skip or queue for lower-confidence extraction, depending on engineering simplicity

PM recommendation:
- In MVP, treat `uncertain` as extract-if-link-present, otherwise skip

---

## Source Email Storage

For every email processed as candidate input, store a `source_email` record with:
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
- store partial job record only if there is a valid job URL or enough identifying data
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
- optionally refresh changed fields if the new source is richer
- do not create a new active job row

If no duplicate found:
- create a new job record

If match confidence is ambiguous:
- prefer creating a new record with a possible duplicate marker rather than incorrectly merging distinct jobs

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
- optional: store `source_email` with `is_job_alert = false` if useful for future tuning

PM recommendation:
- store relevant and uncertain candidates, not the whole inbox

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

## Open Questions

- What exact Gmail scope should engineering request for MVP?
- Should initial sync be 30 days, 60 days, or user-configurable?
- Should recruiter outreach be treated as first-class job alerts in MVP?
- What enrichment source is authoritative when email data conflicts with linked page data?
- Should uncertain emails be retained for manual review later?
