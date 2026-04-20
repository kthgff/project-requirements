# JobTrackr Initial User Stories

## Epic 1: Gmail Connection and Ingestion

### Story 1.1: Connect Gmail account
As a job seeker,
I want to connect my Gmail account,
So that JobTrackr can import relevant job emails automatically.

#### Acceptance Criteria
- User can sign in with Google OAuth
- User can grant Gmail read access
- The system stores the authorization securely
- The user can disconnect Gmail later
- If authorization expires, the system prompts the user to reconnect

### Story 1.2: Ingest relevant emails
As a job seeker,
I want the system to import relevant job-related emails,
So that I do not need to enter every opportunity manually.

#### Acceptance Criteria
- The system polls Gmail on a scheduled interval
- The system evaluates emails using sender, keyword, and label rules
- The system stores processed message IDs to avoid duplicate processing
- The system logs failures for debugging or retry

### Story 1.3: Avoid duplicate jobs
As a job seeker,
I want duplicate job records minimized,
So that my tracker stays clean and trustworthy.

#### Acceptance Criteria
- The system uses Gmail metadata and parsed fields to identify likely duplicates
- The system prevents obvious duplicate record creation where confidence is high
- Duplicate handling behavior is documented for MVP

## Epic 2: Parsing and Job Record Creation

### Story 2.1: Create job records from email
As a job seeker,
I want job emails converted into structured records,
So that I can manage them consistently.

#### Acceptance Criteria
- The system extracts title, company, location, description snippet, application link, recruiter name, and date received when available
- Optional fields such as salary and recruiter email are captured when present
- A record is created even if some fields are missing
- The source email reference is retained for traceability

### Story 2.2: Edit parsed job details
As a job seeker,
I want to correct extracted job information,
So that parsing mistakes do not reduce trust in the app.

#### Acceptance Criteria
- User can edit extracted fields in the UI
- Changes save successfully and persist on refresh
- Edited records remain searchable and filterable

## Epic 3: Job Management Dashboard

### Story 3.1: View jobs in a dashboard
As a job seeker,
I want a dashboard of job opportunities,
So that I can quickly review and manage my pipeline.

#### Acceptance Criteria
- User can access views for inbox, all jobs, saved, and archived
- List view shows title, company, location, status, and date received
- User can open a job detail page from the list

### Story 3.2: Update job status
As a job seeker,
I want to track the status of each opportunity,
So that I know where I stand in the process.

#### Acceptance Criteria
- User can set status to New, Interested, Applied, Interviewing, Offer, Rejected, or Archived
- Status changes persist immediately
- User can filter jobs by status

### Story 3.3: Add notes and tags
As a job seeker,
I want to add notes and tags to jobs,
So that I can capture context and organize opportunities.

#### Acceptance Criteria
- User can add and edit notes on a job
- User can add and remove tags
- Tags can be used to filter or find jobs

## Epic 4: Search and Filtering

### Story 4.1: Search jobs
As a job seeker,
I want to search jobs by keyword,
So that I can quickly find relevant opportunities.

#### Acceptance Criteria
- Search checks title, company, and description content
- Search results update correctly for matching records

### Story 4.2: Filter jobs
As a job seeker,
I want to filter my jobs,
So that I can narrow down the list to what matters right now.

#### Acceptance Criteria
- User can filter by status
- User can filter by date received
- User can filter by location
- Filters can be combined
