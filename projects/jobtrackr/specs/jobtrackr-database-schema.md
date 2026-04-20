# JobTrackr Database Schema Spec

## Purpose
This document defines the proposed PostgreSQL schema for the JobTrackr MVP.

The schema is designed to:
- support Gmail-based ingestion
- preserve traceability from jobs back to source emails
- allow manual editing and job management
- support conservative deduplication
- keep the MVP simple enough to evolve quickly

## Schema Design Principles
- single-user-first, but user-scoped throughout
- explicit foreign keys
- soft-state modeling where uncertainty exists
- preserve source metadata for auditing and troubleshooting
- optimize reads for dashboard, filtering, and sync workflows

## Conventions
- primary keys use UUIDs
- timestamps use `timestamptz`
- snake_case column naming
- created and updated timestamps on mutable business tables
- nullable fields allowed where parsing may be partial

## Enum Definitions

### job_status
```sql
CREATE TYPE job_status AS ENUM (
  'new',
  'interested',
  'applied',
  'interviewing',
  'offer',
  'rejected'
);
```

### message_parse_status
```sql
CREATE TYPE message_parse_status AS ENUM (
  'pending',
  'ignored',
  'parsed',
  'partial',
  'failed'
);
```

### ingestion_run_status
```sql
CREATE TYPE ingestion_run_status AS ENUM (
  'running',
  'completed',
  'partial',
  'failed'
);
```

### activity_type
```sql
CREATE TYPE activity_type AS ENUM (
  'email_ingested',
  'job_created',
  'job_updated',
  'status_changed',
  'note_updated',
  'tag_added',
  'tag_removed',
  'email_attached',
  'manual_entry_created'
);
```

## Tables

## 1. users
Represents an authenticated application user.

```sql
CREATE TABLE users (
  id UUID PRIMARY KEY,
  email TEXT NOT NULL UNIQUE,
  name TEXT,
  image_url TEXT,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

Notes:
- MVP is single-user-first but still modeled as user-scoped
- email is the canonical unique identifier for app login

## 2. sessions
Stores authenticated web sessions if handled in the app database.

```sql
CREATE TABLE sessions (
  id UUID PRIMARY KEY,
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  session_token TEXT NOT NULL UNIQUE,
  expires_at TIMESTAMPTZ NOT NULL,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

Notes:
- may be replaced by a library-managed session store depending on implementation choice

## 3. gmail_accounts
Stores Gmail connection metadata and encrypted tokens.

```sql
CREATE TABLE gmail_accounts (
  id UUID PRIMARY KEY,
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  google_account_id TEXT NOT NULL,
  email TEXT NOT NULL,
  access_token_encrypted TEXT NOT NULL,
  refresh_token_encrypted TEXT,
  token_expires_at TIMESTAMPTZ,
  scopes TEXT[] NOT NULL DEFAULT '{}',
  last_history_id TEXT,
  last_synced_at TIMESTAMPTZ,
  disconnected_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  UNIQUE (user_id, google_account_id)
);
```

Indexes:
```sql
CREATE INDEX idx_gmail_accounts_user_id ON gmail_accounts(user_id);
CREATE INDEX idx_gmail_accounts_last_synced_at ON gmail_accounts(last_synced_at);
```

Notes:
- `last_history_id` may support future incremental sync strategies
- disconnected_at allows soft disconnect auditing

## 4. gmail_messages
Stores raw Gmail message metadata and processing state.

```sql
CREATE TABLE gmail_messages (
  id UUID PRIMARY KEY,
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  gmail_account_id UUID NOT NULL REFERENCES gmail_accounts(id) ON DELETE CASCADE,
  gmail_message_id TEXT NOT NULL,
  gmail_thread_id TEXT NOT NULL,
  subject TEXT,
  from_name TEXT,
  from_email TEXT,
  snippet TEXT,
  internal_date TIMESTAMPTZ,
  raw_label_ids TEXT[] NOT NULL DEFAULT '{}',
  message_hash TEXT,
  detection_result BOOLEAN,
  parse_status message_parse_status NOT NULL DEFAULT 'pending',
  parse_error TEXT,
  processed_at TIMESTAMPTZ,
  job_id UUID REFERENCES jobs(id) ON DELETE SET NULL,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  UNIQUE (gmail_account_id, gmail_message_id)
);
```

Indexes:
```sql
CREATE INDEX idx_gmail_messages_user_id ON gmail_messages(user_id);
CREATE INDEX idx_gmail_messages_thread_id ON gmail_messages(gmail_thread_id);
CREATE INDEX idx_gmail_messages_parse_status ON gmail_messages(parse_status);
CREATE INDEX idx_gmail_messages_internal_date ON gmail_messages(internal_date DESC);
CREATE INDEX idx_gmail_messages_job_id ON gmail_messages(job_id);
```

Notes:
- message_hash may help secondary dedupe or debugging
- `detection_result` records whether the email was considered job-related

## 5. jobs
Primary business entity for a job opportunity.

```sql
CREATE TABLE jobs (
  id UUID PRIMARY KEY,
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  primary_gmail_message_id UUID REFERENCES gmail_messages(id) ON DELETE SET NULL,
  title TEXT,
  company TEXT,
  location TEXT,
  description_snippet TEXT,
  application_link TEXT,
  recruiter_name TEXT,
  recruiter_email TEXT,
  salary_text TEXT,
  status job_status NOT NULL DEFAULT 'new',
  saved BOOLEAN NOT NULL DEFAULT FALSE,
  notes TEXT,
  date_received TIMESTAMPTZ,
  archived_at TIMESTAMPTZ,
  duplicate_of_job_id UUID REFERENCES jobs(id) ON DELETE SET NULL,
  parse_confidence NUMERIC(5,2),
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

Indexes:
```sql
CREATE INDEX idx_jobs_user_id ON jobs(user_id);
CREATE INDEX idx_jobs_status ON jobs(user_id, status);
CREATE INDEX idx_jobs_saved ON jobs(user_id, saved);
CREATE INDEX idx_jobs_archived_at ON jobs(user_id, archived_at);
CREATE INDEX idx_jobs_date_received ON jobs(user_id, date_received DESC);
CREATE INDEX idx_jobs_company_title ON jobs(user_id, company, title);
CREATE INDEX idx_jobs_recruiter_email ON jobs(user_id, recruiter_email);
```

Notes:
- archived jobs are modeled with `archived_at`, not a status enum value
- `duplicate_of_job_id` supports soft duplicate handling without destructive merges
- parse_confidence is optional but useful for future tuning and review workflows

## 6. job_tags
Stores reusable tags owned by a user.

```sql
CREATE TABLE job_tags (
  id UUID PRIMARY KEY,
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  name TEXT NOT NULL,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  UNIQUE (user_id, name)
);
```

Indexes:
```sql
CREATE INDEX idx_job_tags_user_id ON job_tags(user_id);
```

## 7. job_tag_links
Join table linking jobs and tags.

```sql
CREATE TABLE job_tag_links (
  job_id UUID NOT NULL REFERENCES jobs(id) ON DELETE CASCADE,
  tag_id UUID NOT NULL REFERENCES job_tags(id) ON DELETE CASCADE,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  PRIMARY KEY (job_id, tag_id)
);
```

Indexes:
```sql
CREATE INDEX idx_job_tag_links_tag_id ON job_tag_links(tag_id);
```

## 8. job_activities
Timeline of notable events for a job.

```sql
CREATE TABLE job_activities (
  id UUID PRIMARY KEY,
  job_id UUID NOT NULL REFERENCES jobs(id) ON DELETE CASCADE,
  type activity_type NOT NULL,
  body TEXT,
  metadata JSONB NOT NULL DEFAULT '{}'::jsonb,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

Indexes:
```sql
CREATE INDEX idx_job_activities_job_id ON job_activities(job_id, created_at DESC);
```

Notes:
- metadata allows source email references, old/new status values, and debug fields without over-modeling the MVP

## 9. ingestion_runs
Tracks each sync execution.

```sql
CREATE TABLE ingestion_runs (
  id UUID PRIMARY KEY,
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  gmail_account_id UUID NOT NULL REFERENCES gmail_accounts(id) ON DELETE CASCADE,
  status ingestion_run_status NOT NULL,
  started_at TIMESTAMPTZ NOT NULL,
  finished_at TIMESTAMPTZ,
  messages_scanned INTEGER NOT NULL DEFAULT 0,
  messages_matched INTEGER NOT NULL DEFAULT 0,
  jobs_created INTEGER NOT NULL DEFAULT 0,
  jobs_attached INTEGER NOT NULL DEFAULT 0,
  errors_count INTEGER NOT NULL DEFAULT 0,
  error_summary TEXT,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

Indexes:
```sql
CREATE INDEX idx_ingestion_runs_user_id ON ingestion_runs(user_id, started_at DESC);
CREATE INDEX idx_ingestion_runs_account_id ON ingestion_runs(gmail_account_id, started_at DESC);
```

## 10. ingestion_errors
Optional detail table for message-level failures.

```sql
CREATE TABLE ingestion_errors (
  id UUID PRIMARY KEY,
  ingestion_run_id UUID NOT NULL REFERENCES ingestion_runs(id) ON DELETE CASCADE,
  gmail_message_id UUID REFERENCES gmail_messages(id) ON DELETE SET NULL,
  stage TEXT NOT NULL,
  error_code TEXT,
  error_message TEXT NOT NULL,
  metadata JSONB NOT NULL DEFAULT '{}'::jsonb,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

Indexes:
```sql
CREATE INDEX idx_ingestion_errors_run_id ON ingestion_errors(ingestion_run_id);
```

## Optional MVP Table: manual_job_sources
If we want manual-entry traceability from day one.

```sql
CREATE TABLE manual_job_sources (
  id UUID PRIMARY KEY,
  job_id UUID NOT NULL UNIQUE REFERENCES jobs(id) ON DELETE CASCADE,
  created_by_user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

This table is optional because manual entries can also be inferred from `primary_gmail_message_id IS NULL`.

## Relationship Summary
- one user has many gmail_accounts
- one user has many gmail_messages
- one user has many jobs
- one user has many job_tags
- one gmail_account has many gmail_messages
- one gmail_message may map to zero or one job
- one job has many activities
- many jobs can have many tags through job_tag_links
- one gmail_account has many ingestion_runs
- one ingestion_run may have many ingestion_errors

## Search Strategy
Initial search should use SQL filters and `ILIKE` against:
- jobs.title
- jobs.company
- jobs.description_snippet
- jobs.recruiter_name

Possible future optimization:
- add a generated `tsvector` column for full-text search
- add trigram indexes if fuzzy matching becomes important

Not recommended for MVP unless needed by observed performance.

## Deduplication Support
The schema supports conservative dedupe through:
- gmail thread IDs on messages
- normalized company/title comparisons in query logic
- optional `duplicate_of_job_id` links
- activity logging for review trails

## Data Lifecycle Rules
- Gmail messages are preserved for traceability unless explicit cleanup rules are added later
- jobs are archived, not hard-deleted, during normal product use
- disconnecting Gmail should not delete historical jobs or source references
- parsing failures should keep raw message records for debugging

## Migration Strategy
- use forward-only SQL migrations
- use `golang-migrate` or equivalent migration tool
- keep enum evolution explicit and versioned carefully
- avoid destructive column changes without backfill planning

## Initial Query Priorities
The first sqlc query groups should cover:
- user lookup and session auth
- Gmail account connect and refresh state
- insert/find gmail messages
- create job from parsed message
- attach message to existing job
- list jobs with filters
- fetch single job detail with tags and activities
- update job status and notes
- tag add/remove
- list ingestion runs and errors

## Future Extensions
The schema can evolve later to support:
- richer email body storage and attachment metadata
- AI parse results and confidence explanations
- reminders and follow-up workflows
- non-Gmail providers
- multi-user collaboration and organizations
