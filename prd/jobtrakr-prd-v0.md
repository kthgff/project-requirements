# JobTrakr PRD v0

## Product Summary

JobTrakr is a job-search workflow tool for Keith Goff. It scans Gmail inbox job alert emails, identifies jobs, looks up fuller job details, saves them to a database, and helps Keith quickly see which roles are worth attention by comparing each job against his resume.

## Problem

Job searching takes too long because relevant opportunities arrive by email and require too much manual effort to identify, research, track, and evaluate.

## Goal

Reduce the time from job alert email to a clear decision about whether the opportunity is worth attention.

## Primary User

- Keith Goff

## Core Jobs To Be Done

- When job alert emails arrive, I want the system to automatically find and track the jobs so I do not miss them.
- When a job is tracked, I want the system to gather useful details so I do not have to research every role from scratch.
- When I review jobs, I want AI to compare them to my resume and flag likely fits so I can focus faster.

## MVP Scope

### In scope
- Scan Gmail inbox for job alert emails
- Detect job opportunities in those emails
- Look up each job to gather fuller details
- Save job details to a database
- Show tracked jobs in a table UI
- Compare jobs against Keith's resume
- Flag jobs that appear to be a good fit

### Out of scope for v0
- Automatic job application submission
- Multi-user support
- Advanced workflow automation beyond tracking, lookup, and fit flagging

## Proposed Workflow

1. System scans Gmail inbox for job alert emails
2. System identifies jobs referenced in the email
3. System looks up each job to gather fuller details
4. System creates or updates a job record in the database
5. System compares the role against Keith's resume
6. System flags the job if it appears to be a good fit
7. Keith reviews jobs in a table UI

## Core Data Needed Per Job

Initial assumption:
- company
- title
- location
- remote/hybrid/on-site
- compensation if available
- source email
- source link
- job description or summary
- fit flag
- fit rationale
- status
- date found

## UI Direction

Primary first view:
- table of tracked jobs

Likely useful columns:
- company
- title
- location
- source
- date found
- fit flag
- status

## Key Open Questions

- How do we define a "job alert email" for filtering and detection?
- Which external sources should be used to look up fuller job details?
- What exact file or profile source should be used as the resume input?
- What statuses should jobs support?
- How should fit be represented, boolean flag only, score plus flag, or rationale plus flag?
- What level of deduplication is required when multiple emails mention the same role?

## Success Criteria

- Inbox job alert emails are converted into structured job records
- Keith can review tracked jobs in a simple table
- Good-fit roles are clearly flagged using resume-based evaluation
- Keith spends materially less time triaging job opportunities
