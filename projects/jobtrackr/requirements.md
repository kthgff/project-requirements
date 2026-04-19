# JobTrackr Product Requirements

## Objective
Build an MVP that automatically ingests job-related emails from Gmail, extracts usable job data, and gives an individual job seeker a simple web app to manage opportunities.

## Product Principles
- Minimize manual entry
- Preserve user trust through editable data
- Keep the workflow fast and lightweight
- Optimize for a single user before expanding scope

## Users
### Primary User
- Individual job seeker using Gmail as their main inbox for job search activity

## User Needs
- I want relevant job emails captured automatically
- I want job information extracted into consistent fields
- I want to track status without maintaining a spreadsheet
- I want to search and filter opportunities quickly
- I want to correct mistakes when parsing is wrong

## MVP Requirements

### 1. Account and Authentication
- The user must be able to sign in with Google OAuth
- The user must be able to connect a Gmail account
- Gmail access tokens must be stored securely
- The system must detect expired authorization and require reconnection

### 2. Email Ingestion
- The system must poll Gmail on a recurring schedule
- The system must support candidate email filtering based on sender, keywords, and labels
- The system must store message IDs to prevent duplicate processing
- The system must log processing failures

### 3. Job Detection
- The system must determine whether an email is job-related using rule-based logic
- Detection rules must be configurable in code for MVP
- The system should support common job boards and recruiter outreach patterns

### 4. Parsing and Data Extraction
Required fields to extract when present:
- Job title
- Company
- Location
- Description snippet
- Application link
- Recruiter name
- Date received

Optional fields:
- Salary
- Recruiter email

Behavior requirements:
- A job record must still be created when some fields are missing
- Parsed fields must be editable in the UI
- Source email references must be retained for traceability

### 5. Job Management UI
The app must provide:
- A job list view
- A job detail view
- Views for inbox, all jobs, saved, and archived
- Inline or simple status editing
- Notes editing
- Tag management

Displayed fields in list view:
- Title
- Company
- Location
- Status
- Date received

### 6. Status Tracking
Supported statuses:
- New
- Interested
- Applied
- Interviewing
- Offer
- Rejected
- Archived

Requirements:
- User must be able to update status manually
- Status changes must persist immediately
- Jobs must be filterable by status

### 7. Search and Filtering
The user must be able to:
- Search by keyword across title, company, and description
- Filter by status
- Filter by date received
- Filter by location
- Combine filters in one view

### 8. Notes and Tags
- User must be able to add and edit notes on a job
- User must be able to add and remove tags
- Tags should be available as a filterable attribute

## Data Requirements
Minimum job entity fields:
- id
- gmail_message_id
- gmail_thread_id
- title
- company
- location
- description_snippet
- application_link
- recruiter_name
- recruiter_email
- salary
- status
- notes
- date_received
- created_at
- updated_at
- archived_at

## Non-Functional Requirements
- OAuth tokens must be stored securely
- The system should handle several thousand processed emails for one user
- The UI should remain responsive for a personal dataset
- Failures in ingestion or parsing should be observable in logs
- The system should tolerate partial parsing failures without blocking record creation

## Exclusions
Not required for MVP:
- AI parsing
- Multi-user support
- Native mobile app
- Reminder workflows
- Calendar integration
- Advanced analytics
- Non-Gmail email providers

## Product Risks
- Parsing reliability may undermine trust if too many fields are wrong
- Duplicate records may create a poor management experience
- Gmail permission scope may create onboarding friction
- Job update emails may be hard to associate with existing records

## Decisions Needed
- Whether saved is a distinct state or a saved flag
- Whether archived is a status or a separate attribute
- Whether duplicate handling is automatic or review-based
- Whether manual job entry is needed in MVP
- Whether update emails should appear in a job activity timeline
