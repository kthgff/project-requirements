# JobTrackr PRD

## Product Summary
JobTrackr is a personal job search tracker that connects to a user’s Gmail account, detects job-related emails, extracts structured job data, and presents it in a web app for tracking opportunities through the application lifecycle.

## Problem
Job seekers receive job leads, recruiter outreach, and application updates across a messy inbox. Tracking these opportunities manually in spreadsheets or notes is slow, inconsistent, and easy to abandon.

## Goals

### Primary Goals
- Automatically capture job opportunities from Gmail
- Extract structured job data from emails
- Provide a web interface to manage job opportunities

### Secondary Goals
- Enable filtering and tagging
- Track job application status
- Provide lightweight insight into job search activity

## Target User
- Individual job seeker using Gmail

## Success Metrics
### MVP Metrics
- User can connect Gmail successfully
- System ingests relevant job-related emails automatically
- At least 80% of relevant emails create a usable job record
- User can update job status, add notes, and find jobs through search or filtering
- Duplicate job creation stays low enough that manual cleanup is minimal

### Future Metrics
- Parsing accuracy by field
- Weekly active usage
- Time saved versus manual tracking
- Percentage of jobs with complete lifecycle updates

## MVP Scope

### In Scope
- Gmail OAuth connection
- Polling Gmail inbox for relevant messages
- Rule-based detection of job-related emails
- Structured extraction of core job fields
- PostgreSQL storage
- Dashboard for viewing and managing jobs
- Status tracking
- Notes and tags
- Search and basic filters
- Google-authenticated app access

### Out of Scope
- AI-based parsing
- Multi-user collaboration
- Mobile app
- Browser extension
- Calendar sync
- Reminder workflows
- Support for non-Gmail providers

## Core Features

### 1. Email Ingestion
- Connect to Gmail via OAuth
- Poll inbox on a scheduled interval
- Filter candidate emails by sender, keywords, and labels
- Store Gmail message metadata for traceability and deduplication

### 2. Email Parsing
Extract the following when available:
- Job title
- Company
- Location
- Description snippet
- Application link
- Recruiter name
- Salary (optional)

Approach:
- MVP uses regex and rule-based heuristics
- Later versions may use AI parsing

### 3. Dashboard
Views:
- Inbox (new jobs)
- All jobs
- Saved
- Archived

Default job fields shown:
- Title
- Company
- Location
- Status
- Date received

### 4. Status Tracking
Supported statuses:
- New
- Interested
- Applied
- Interviewing
- Offer
- Rejected
- Archived

### 5. Search and Filters
- Search by keyword
- Filter by status
- Filter by date
- Filter by location

### 6. Notes and Tags
- Add notes to jobs
- Add and remove tags
- Reserve reminders for a future release

### 7. Authentication
- Google OAuth preferred
- Optional local auth is not part of MVP unless implementation constraints require it

## Functional Requirements

### Gmail Integration
- User can connect a Gmail account with Google OAuth
- System requests minimum required Gmail scopes
- System can detect expired auth and prompt the user to reconnect
- User can disconnect Gmail access

### Ingestion
- System polls Gmail on a configurable schedule
- System only processes emails that match defined job-detection rules
- System tracks processed message IDs to avoid reprocessing
- System logs ingestion failures for retry and troubleshooting

### Parsing
- System creates a job record even when some fields are missing
- Parsed data remains editable by the user
- System retains source email metadata for traceability
- System should support common recruiter and job board formats in MVP

### Web App
- User can view a list of jobs
- User can open a job detail view
- User can edit extracted fields manually
- User can change status
- User can add notes and tags
- User can archive and unarchive jobs

## Key Product Decisions
- Email is the ingestion source, but the in-app job record becomes the editable source of truth
- Multiple emails may map to a single job opportunity, so deduplication is required
- Polling is the MVP approach, not push-based Gmail subscriptions
- Manual correction is required in MVP to preserve trust when parsing is imperfect

## Risks
- Gmail permission friction may hurt onboarding conversion
- Rule-based parsing may miss poorly structured recruiter emails
- Deduplication may be harder than initial extraction
- Update emails may be misclassified as new opportunities

## Open Questions
- Should saved be modeled as a status or a separate view state?
- Should archived be a status or a separate archived flag?
- How should duplicates be handled, automatic merge or review queue?
- Should application update emails attach to an existing job timeline?
- Do we need manual job entry in MVP as a fallback?

## Recommended MVP Cut
If speed matters most, first release should focus on:
- Gmail OAuth
- Inbox polling and job-email detection
- Rule-based parsing
- Job list and detail view
- Status updates
- Notes
- Search by keyword and filter by status
