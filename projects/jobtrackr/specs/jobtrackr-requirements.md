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
- The system must support a Gmail discovery path using the `gog` CLI for local/operator-driven ingestion and debugging
- The system must be able to search for job-related Gmail messages using repeatable queries such as sender filters, subject filters, and keyword filters
- The system must persist source-email records before or alongside parsed job creation so inbox discoveries can be reprocessed later without re-fetch ambiguity

### 3. Job Detection
- The system must determine whether an email is job-related using rule-based logic
- Detection rules must be configurable in code for MVP
- The system should support common job boards and recruiter outreach patterns
- Detection should explicitly support job-alert and search-result style emails from sources like LinkedIn, Indeed, Built In, Greenhouse, Lever, Ashby, company talent alerts, and recruiter outreach when a specific role is present
- The system should support a first-pass "candidate source email" state where job-related emails are stored even if they do not yet produce a complete parsed job record

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
- A parsed job record may be created only when at least one of the following is true:
  - external job ID exists
  - normalized job URL exists
  - title and company both exist
  - title, location, and source platform exist with medium-or-higher extraction confidence
- If the parsed-job threshold is not met, the system must persist the source email only for debug and traceability
- Parsed fields must be editable in the UI
- Source email references must be retained for traceability
- The ingestion flow must support two explicit stages:
  1. save the Gmail message as a source-email record
  2. parse and upsert one or more job records from that source email when confidence is sufficient
- The system must support reprocessing previously saved source emails after parser improvements or rule changes

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
Canonical job-state model:
- `status` is workflow state only
- `saved` is a boolean shortlist flag
- `archived_at` is a separate archive attribute

Supported workflow statuses:
- New
- Interested
- Applied
- Interviewing
- Offer
- Rejected

Derived view semantics:
- Inbox: non-archived jobs with status `new`
- All jobs: all non-archived jobs
- Saved: non-archived jobs where `saved = true`
- Archived: jobs where `archived_at` is set

Requirements:
- User must be able to update status manually
- User must be able to save and unsave jobs independently of status
- User must be able to archive and unarchive jobs independently of status
- Status changes must persist immediately
- Jobs must be filterable by workflow status

### 7. Search and Filtering
The user must be able to:
- Search by case-insensitive substring across title, company, and description
- Filter by workflow status
- Filter by date received using inclusive date boundaries in the user-facing timezone
- Filter by location using case-insensitive exact matching on normalized values
- Filter by saved state and archived state
- Combine filters in one view with AND logic across filter types and OR logic within repeated values of the same filter

### 8. Notes and Tags
- User must be able to add and edit notes on a job
- User must be able to add and remove tags
- Tags should be available as a filterable attribute

## Data Requirements
Minimum source-email entity fields:
- id
- gmail_message_id
- gmail_thread_id
- account_email
- sender
- subject
- date_received
- labels
- source_type
- raw_text or retrievable raw body reference
- processing_state
- parse_confidence
- created_at
- updated_at

Minimum job entity fields:
- id
- gmail_message_id
- gmail_thread_id
- source_email_id
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

## Locked MVP Decisions
- `saved` is a boolean flag, not a workflow state
- archive is a separate attribute represented by `archived_at`, not a workflow status
- duplicate handling is automatic only for exact-match rules defined in the PM decision memo; ambiguous matches do not auto-merge in MVP
- manual job entry is out of MVP scope
- recruiter outreach is in MVP scope only when the email clearly references a specific open role
- Gmail discovery and debug ingestion may use the `gog` CLI as an operator-facing ingestion path for local workflows and verification
- source emails are first-class records in the data model and must be persisted even when a full job row is not created yet

## Decisions Still Open
- Whether update emails should appear in a job activity timeline
- What enrichment source should be authoritative when email data conflicts with the linked job page


## Strategic Product Direction

JobTrakr should evolve toward an AIApply-style AI job search copilot.

This means post-MVP planning should include:
- resume tailoring
- cover letter generation
- application workflow support or automation
- interview preparation support

The MVP does not need to ship all of these immediately, but the architecture and roadmap should allow expansion in that direction.
