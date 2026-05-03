# JobTrakr MVP Stories v1

> Historical story draft. Use the PM decision memo, API contract, and current DEVELOPMENT_PLAN for implementation truth.
> Terms like fit flag, flagged, or unflagged in this file are retained as legacy shorthand only and should be read as fit-signal display, not as current product vocabulary or workflow-state behavior.
> Canonical workflow statuses remain `new`, `interested`, `applied`, `interviewing`, `offer`, and `rejected`.

## Epic 0: Authentication

### Story 0
As Keith, I want to log into JobTrakr using my Gmail account so that I can securely access my job tracking workspace.

**Acceptance criteria**
- The product includes a login page
- The login page presents a polished JobTrakr-branded first impression, not a generic placeholder form
- The login page requires me to enter the Google email I intend to use
- The login page offers Google federated sign-in
- The Google account returned by OAuth must match the email I supplied
- After successful sign-in, I am authenticated into the app
- On first successful sign-in, the system creates a user record in the database
- On returning sign-in, the system reuses the existing user record and updates mutable profile fields
- Unauthenticated users cannot access the main app experience

# JobTrackr MVP Stories v1

## Epic 1: Resume setup

### Story 1
As Keith, I want to upload my resume so that the system can evaluate jobs against my background.

**Acceptance criteria**
- I can upload a PDF resume through the product
- The system stores both the original PDF and parsed resume data
- The original PDF is encrypted at rest
- The system parses contact info, summary, work history, skills, education, and certifications when available
- The system preserves full plain-text resume content for fit scoring and future application support
- Job titles are preserved as written on the resume
- I can store multiple resume versions
- A newly uploaded successfully parsed resume becomes the active/default resume automatically
- I can switch the active resume later
- Parsed resume data is not directly editable in MVP
- If parsing fails, I see an error asking for help and can upload a cleaner PDF or paste resume text
- I can view or download the original uploaded resume
- Deleting a resume archives it instead of hard deleting it

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
- Good-fit jobs are clearly highlighted through fit score, fit-signal state, and shortlist cues without treating legacy terms like `flagged` as workflow or current product-state vocabulary
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
