# JobTrackr MVP Stories v1

## Epic 1: Resume setup

### Story 1
As Keith, I want to upload my resume so that the system can evaluate jobs against my background.

**Acceptance criteria**
- I can upload a resume file through the product
- The system stores the uploaded resume for later fit evaluation
- A newer upload can replace the previous resume

## Epic 2: Inbox job detection

### Story 2
As Keith, I want the system to scan my Gmail inbox for job alert emails so that relevant opportunities are found automatically.

**Acceptance criteria**
- The system can access the configured Gmail inbox
- The system identifies relevant job alert emails
- LinkedIn and Indeed alerts are supported
- Non-job emails are ignored

## Epic 3: Job enrichment and storage

### Story 3
As Keith, I want detected jobs to be enriched and saved so that each opportunity is useful to review.

**Acceptance criteria**
- The system identifies a job opportunity from a relevant email
- The system gathers fuller job details when available
- The system creates or updates a job record in the database
- Duplicate alerts for the same role do not create obvious duplicate records

## Epic 4: Fit evaluation

### Story 4
As Keith, I want tracked jobs compared against my uploaded resume so that strong-fit roles are easy to spot.

**Acceptance criteria**
- The system uses the uploaded resume as fit context
- The system produces a fit evaluation for tracked jobs
- Good-fit jobs are clearly flagged
- The system stores at least a short fit rationale

## Epic 5: Table review workflow

### Story 5
As Keith, I want to review tracked jobs in a table so that I can quickly scan and manage opportunities.

**Acceptance criteria**
- The UI presents tracked jobs in a table
- The table shows company, title, location, source, date found, fit flag, and status
- I can filter or sort the table at minimum by status and date found
- I can update a job status manually
- Available statuses are new, flagged, reviewing, skipped, applied, interview, rejected, and offer
