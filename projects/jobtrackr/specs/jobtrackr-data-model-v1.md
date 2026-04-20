# JobTrackr Data Model v1

## Purpose

Define the initial data model for JobTrackr MVP so engineering can implement ingestion, fit analysis, and table-based review.

## Design Principles

- Keep MVP schema simple and extensible
- Store enough raw source data to debug ingestion
- Separate source email metadata from normalized job data
- Support deduplication across repeated alerts
- Support future enrichment and fit-analysis improvements without destructive schema changes

## Core Entities

1. `resume`
2. `source_email`
3. `job`
4. `job_fit_analysis`
5. `job_status_history`

---

## 1) resume

Represents the user-uploaded resume used for fit analysis.

### Fields

- `id` UUID, primary key
- `user_id` UUID or string, required
- `file_name` string, required
- `storage_path` string, required
- `mime_type` string, nullable
- `file_size_bytes` integer, nullable
- `parsed_text` text, required
- `uploaded_at` timestamp, required
- `replaced_at` timestamp, nullable
- `created_at` timestamp, required
- `updated_at` timestamp, required

### Notes

- MVP assumes one active resume per user.
- Older resumes may remain stored but inactive.

---

## 2) source_email

Represents an email that triggered job detection.

### Fields

- `id` UUID, primary key
- `user_id` UUID or string, required
- `gmail_message_id` string, unique, required
- `thread_id` string, nullable
- `from_address` string, required
- `from_name` string, nullable
- `subject` string, required
- `received_at` timestamp, required
- `snippet` text, nullable
- `raw_text` text, nullable
- `html_body` text, nullable
- `source_platform` enum/string, nullable
  - examples: `linkedin`, `indeed`, `ziprecruiter`, `dice`, `recruiter`, `unknown`
- `is_job_alert` boolean, required
- `detection_reason` text, nullable
- `created_at` timestamp, required
- `updated_at` timestamp, required

### Notes

- Keep enough email content to debug extraction.
- `source_platform` is derived from sender/domain/content.

---

## 3) job

Represents a normalized tracked job opportunity.

### Fields

- `id` UUID, primary key
- `user_id` UUID or string, required
- `canonical_key` string, unique-ish, nullable
- `company_name` string, required
- `job_title` string, required
- `job_url` string, nullable
- `source_platform` enum/string, nullable
- `external_job_id` string, nullable
- `location_text` string, nullable
- `location_type` enum/string, nullable
  - `remote`, `hybrid`, `onsite`, `unknown`
- `city` string, nullable
- `state_region` string, nullable
- `country` string, nullable
- `salary_min` integer, nullable
- `salary_max` integer, nullable
- `salary_currency` string, nullable
- `salary_period` enum/string, nullable
  - `hour`, `year`, `month`, `contract`, `unknown`
- `employment_type` enum/string, nullable
  - `full_time`, `part_time`, `contract`, `temporary`, `internship`, `unknown`
- `seniority` string, nullable
- `job_description_text` text, nullable
- `job_summary` text, nullable
- `requirements_text` text, nullable
- `posted_at` timestamp, nullable
- `discovered_at` timestamp, required
- `last_seen_at` timestamp, required
- `status` enum/string, required
  - `new`, `interested`, `applied`, `interviewing`, `offer`, `rejected`
- `saved` boolean, required, default `false`
- `archived_at` timestamp, nullable
- `fit_flag` boolean, nullable
- `fit_score` integer, nullable
- `match_percentage` integer, nullable
- `fit_summary` text, nullable
- `duplicate_of_job_id` UUID, nullable
- `created_at` timestamp, required
- `updated_at` timestamp, required

### Notes

- `canonical_key` should help deduplicate the same role across repeated alerts.
- Suggested canonical key input: normalized company + normalized title + normalized location + external job id if present.
- `saved` and `archived_at` are separate from workflow status.
- fit outputs stay nullable until analysis has actually run.

---

## 4) job_fit_analysis

Stores the current or historical AI evaluation for a job against a resume.

### Fields

- `id` UUID, primary key
- `job_id` UUID, foreign key, required
- `resume_id` UUID, foreign key, required
- `model_name` string, nullable
- `fit_flag` boolean, required
- `fit_score` integer, nullable
- `match_percentage` integer, nullable
- `summary` text, required
- `strengths` json/text, nullable
- `gaps` json/text, nullable
- `reasoning_version` string, nullable
- `analyzed_at` timestamp, required
- `created_at` timestamp, required

### Notes

- Keep separate from `job` so fit analysis can be rerun after resume changes.
- `job.fit_flag`, `job.fit_score`, and `job.fit_summary` can mirror the latest analysis for query speed.

---

## 5) job_status_history

Stores audit trail of job status changes.

### Fields

- `id` UUID, primary key
- `job_id` UUID, foreign key, required
- `from_status` string, nullable
- `to_status` string, required
- `changed_by` string, required
  - examples: `system`, `user`, `agent`
- `change_reason` text, nullable
- `created_at` timestamp, required

### Notes

- Useful for timeline/history and future analytics.

---

## Relationship Summary

- One `resume` belongs to one user
- One `source_email` belongs to one user
- One `source_email` may produce one or more `job` records through a `job_source_email` linkage table
- One `job` may have one latest or many historical `job_fit_analysis` records
- One `job` may have many `job_status_history` records

---

## Required Fields for MVP Table View

Minimum fields needed for the initial table UI:

- `job.id`
- `job.company_name`
- `job.job_title`
- `job.location_text`
- `job.source_platform`
- `job.discovered_at`
- `job.saved`
- `job.archived_at`
- `job.fit_flag`
- `job.match_percentage`
- `job.status`

Recommended supporting detail fields:

- `job.job_url`
- `job.job_summary`
- `job.fit_summary`
- `source_email.subject`
- `source_email.from_address`

---

## Deduplication Rules, MVP Recommendation

Treat a job as a probable duplicate when all of the following are substantially the same:
- company name
- job title
- location
- source job url or external job id when available

Recommended behavior:
- update `last_seen_at` on existing job
- attach new `source_email_id` via linkage table later if needed
- do not create a visibly separate active job row unless confidence is low

---

## Implementation Notes

### Suggested enums

#### `job.status`
- `new`
- `interested`
- `applied`
- `interviewing`
- `offer`
- `rejected`

Canonical note:
- fit signals such as `fit_flag`, low-fit indicators, shortlist cues, or review priority must stay separate from workflow status
- do not persist legacy statuses such as `flagged`, `reviewing`, `skipped`, or `interview`

#### `job.location_type`
- `remote`
- `hybrid`
- `onsite`
- `unknown`

#### `source_email.source_platform`
- `linkedin`
- `indeed`
- `ziprecruiter`
- `dice`
- `recruiter`
- `unknown`

---

## Non-Goals for MVP Schema

Not required yet:
- multi-user org/team model complexity
- interview scheduling entities
- application form submission entities
- company CRM/contact tracking
- cover letter or resume variant management

---

## Open Questions

- Do we need a `job_email_link` join table in MVP if many emails can map to one job?
- Should `fit_score` be standardized to 0 to 100 now, even if hidden in UI?
- Should compensation be normalized more deeply for contract/hourly roles?
- Should the system store structured skills extracted from the resume and job description in MVP or later?
