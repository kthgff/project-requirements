# JobTrakr MVP Stories v1

> Historical story draft. Use the PM decision memo, API contract, and current DEVELOPMENT_PLAN for implementation truth.
> Terms like fit flag in this file should be read as fit-signal display only. Canonical workflow statuses remain `new`, `interested`, `applied`, `interviewing`, `offer`, and `rejected`.

## Epic 0: Authentication

### Story 0
As Keith, I want to log into JobTrakr using my Gmail account so that I can securely access my job tracking workspace.

**Acceptance criteria**
- The product includes a login page
- The login page offers Gmail federated sign-in
- After successful sign-in, I am authenticated into the app
- Unauthenticated users cannot access the main app experience

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
- The system calculates a match percentage for each tracked job
- Good-fit jobs are clearly highlighted through fit score, fit state, and shortlist cues without treating `flagged` as a workflow status
- The system stores at least a short fit rationale

## Epic 5: Table review workflow

### Story 5
As Keith, I want to review tracked jobs in a table so that I can quickly scan and manage opportunities.

**Acceptance criteria**
- The UI presents tracked jobs in a table
- The table shows company, title, location, source, date found, match rating / fit signal, and status
- I can filter or sort the table at minimum by status and date found
- I can update a job status manually
- Available workflow statuses are new, interested, applied, interviewing, offer, and rejected
- Shortlist behavior uses `saved`
- Archive behavior uses `archivedAt`, not a workflow status
