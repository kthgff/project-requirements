# JobTrackr PRD v1

## 1. Product Summary

JobTrackr is a job-search workflow tool for Keith Goff. It scans Gmail for job alert emails, identifies job opportunities, enriches them with fuller details, stores them in a database, compares them against Keith's uploaded resume, and flags strong-fit roles in a table-based UI.

## 2. Problem

Job opportunities arrive in email, but manually reviewing alerts, researching each role, tracking them, and deciding which ones deserve attention takes too much time.

## 3. Product Goal

Help Keith move from inbound job alert email to prioritized job review with much less manual effort.

## 4. Primary User

- Keith Goff

## 5. MVP Outcome

The MVP should automatically turn job alert emails into structured tracked jobs and clearly highlight likely fits using resume-based AI evaluation.

## 6. In Scope

- Scan Gmail inbox for job alert emails
- Support all relevant job alert email sources, including LinkedIn and Indeed
- Identify jobs referenced in those emails
- Look up fuller details for each job
- Save structured job records to a database
- Support authenticated access with Gmail federated login
- Include a login page for user sign-in
- Allow Keith to upload a resume for fit evaluation
- Use AI to compare each job against the uploaded resume
- Calculate a match percentage for each job
- Flag jobs that look like good fits
- Show tracked jobs in a table UI
- Support manual status management for tracked jobs

## 7. Out of Scope for MVP

- Automatic application submission
- Automatic resume tailoring
- Automatic cover letter generation
- Multi-user support
- Non-email job ingestion sources

## 8. Proposed User Flow

1. Keith opens the product login page
2. Keith signs in with Gmail federated login
3. System creates or resumes the authenticated session
4. Keith uploads his resume
5. System scans Gmail inbox for job alert emails
6. System detects job opportunities from relevant emails, including LinkedIn and Indeed alerts
7. System looks up fuller job details
8. System creates or updates a job record in the database
9. AI compares the job against Keith's uploaded resume
10. System flags good-fit roles
11. Keith reviews jobs in a table UI and updates status as needed

## 9. Core Entities

### User
- authenticated account
- Google identity
- email address
- created date

### Resume
- uploaded file
- upload date
- parsed text or structured profile used for fit analysis

### Job
- company
- title
- location
- work mode, remote/hybrid/on-site
- compensation if available
- source email sender
- source platform
- source job URL
- job description or summary
- date found
- fit flag
- match percentage
- fit rationale
- status

## 10. Initial Status Model

- new
- flagged
- reviewing
- skipped
- applied
- interview
- rejected
- offer

## 11. UI Direction

Primary view is a table.

Recommended columns:
- company
- title
- location
- source
- date found
- fit flag
- status

Likely first interactions:
- sort by fit flag or date found
- filter by status
- open row for job detail

## 12. Functional Requirements

### FR1. Gmail scanning
The system shall scan Gmail inbox content for job alert emails.

### FR2. Job alert detection
The system shall identify relevant job alert emails from supported sources, including LinkedIn and Indeed.

### FR3. Job extraction and enrichment
The system shall detect referenced jobs and gather fuller job details before persisting them.

### FR4. Job persistence
The system shall create or update job records in the database.

### FR5. Authentication
The system shall support user authentication via Gmail federated login.

### FR6. Login page
The system shall provide a login page where the user can start Gmail federated sign-in.

### FR7. Resume upload
The system shall allow Keith to upload a resume file to be used for fit evaluation.

### FR8. AI fit analysis
The system shall compare tracked jobs against the uploaded resume and produce a fit result.

### FR9. Fit flagging
The system shall clearly flag jobs identified as likely good fits.

### FR10. Table review UI
The system shall present tracked jobs in a table interface.

### FR11. Status tracking
The system shall support manual job status updates using the initial status model.

## 13. Open Questions

- ~~What fields are mandatory for a useful job record?~~ **Answered: id, title, company, description, salary range, location, match, skills, tags**
- ~~What enrichment sources should be used when the email itself lacks detail?~~ **Answered: scrape from job detail page linked in email**
- ~~Should fit be shown as a boolean flag only, or as a score plus rationale?~~ **Answered: score from 1 to 100**
- ~~What deduplication logic should merge repeated alerts for the same job?~~ **Answered: match on job URL**
- ~~Should the system notify Keith when new flagged jobs appear?~~ **Answered: no, can view UI**

## 14. Success Criteria

- Job alert emails are converted into structured job records with minimal manual effort
- Keith can review tracked jobs in a table
- Good-fit roles are clearly surfaced based on the uploaded resume
- Time spent triaging inbound opportunities is materially reduced
