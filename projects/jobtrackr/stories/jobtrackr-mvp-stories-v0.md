# JobTrackr MVP Stories v0

> Superseded draft retained for history only.
> For implementation, use the PM decision memo, API contract, and current DEVELOPMENT_PLAN as the canonical source set.
> In this older file, references to fit "flagging" should be read as fit-signal highlighting, not workflow state changes.

## Epic: Inbox scanning and job ingestion

### Story 1
As Keith, I want the system to scan my Gmail inbox for job alert emails so that job opportunities can be detected automatically.

**Acceptance criteria**
- System can access the target Gmail inbox
- System identifies emails that match the job alert definition
- Non-job emails are ignored

### Story 2
As Keith, I want detected jobs to be looked up and saved to the database so that each opportunity has useful structured detail.

**Acceptance criteria**
- System extracts or identifies a job from a relevant email
- System gathers additional job details from available sources
- System creates or updates a job record in the database
- Duplicate mentions of the same job do not create obvious duplicate records

## Epic: Fit evaluation

### Story 3
As Keith, I want each tracked job compared against my resume so that likely fits are highlighted for review.

**Acceptance criteria**
- System uses Keith's resume as fit context
- System generates a fit result for tracked jobs
- Good-fit jobs are clearly highlighted through fit signals, not workflow-state mutation
- Fit output includes at least a short rationale

## Epic: Job review UI

### Story 4
As Keith, I want to review tracked jobs in a table so that I can scan opportunities quickly.

**Acceptance criteria**
- UI presents tracked jobs in a table
- Table includes core columns for job summary and fit status
- Jobs can be sorted or filtered by fit/status at minimum
- Selecting a job reveals fuller detail
