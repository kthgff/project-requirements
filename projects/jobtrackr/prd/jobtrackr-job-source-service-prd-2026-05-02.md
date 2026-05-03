# JobTrakr Job Source Service PRD

## Product Summary
JobTrakr needs a second acquisition lane beyond Gmail alerts: a separate microservice in a separate repository that scrapes jobs from Indeed and LinkedIn, normalizes them, deduplicates them, and writes them directly into the shared JobTrakr jobs model so they appear in the main front-end application.

## Problem
Email alerts alone are not enough. Users need higher-quality job discovery that proactively finds relevant opportunities even when those jobs were never delivered by email. Without direct sourcing, users miss strong-fit openings and the product stays too dependent on inbox coverage.

## Goals

### Primary Goals
- Source additional high-quality jobs from Indeed and LinkedIn
- Write sourced jobs directly into the existing JobTrakr jobs dataset
- Show sourced jobs in the existing `/jobs` workspace without creating a second review surface
- Let each user define and manage their own search profiles
- Optimize the sourcing lane for job quality over raw job volume

### Secondary Goals
- Preserve source provenance so users can distinguish Email vs Indeed vs LinkedIn
- Merge duplicates across email and scraped sources into one job record
- Support aggressive sourcing behavior with controlled retries
- Archive jobs that disappear from source sites

## Target User
- Individual job seeker using JobTrakr

## Success Metrics
### MVP Metrics
- User can create and manage multiple sourcing profiles in-product
- Each user receives one automated sourcing run per day
- Sourced jobs appear in `/jobs` without requiring manual import review
- Duplicate job records across email and scraping remain effectively invisible to the user
- Users can distinguish the source of a job in the UI
- Daily sourcing improves perceived job quality, not just count

### MVP Quality Lens
For this workstream, quality is more important than volume. A smaller number of high-fit, relevant jobs is a better outcome than a large number of weak matches.

## MVP Scope

### In Scope
- Separate job source microservice in a new repository
- Indeed scraping
- LinkedIn scraping
- Per-user search profiles
- Search profile fields:
  - job title
  - location
  - remote preference
  - companies to include
  - companies to exclude
  - salary
  - seniority
  - employment type
  - include keywords
  - exclude keywords
- Unlimited search profiles per user
- Pause/disable per profile
- Automatic once-daily sourcing per user
- Search profiles act as both saved filters and scheduled discovery instructions
- Search profile matching applies across all jobs in the database, including Gmail, Indeed, LinkedIn, and future sources
- Jobs can display all matched search profiles
- Aggressive sourcing posture for coverage
- Full-detail job scraping
- Duplicate merge across email and scraped sources
- Direct database writes into the shared JobTrakr jobs model
- Source filtering/visibility in `/jobs`
- Default sourced-job ordering biased toward best fit
- Archive sourced jobs that disappear from source sites
- Retry failed daily runs up to 3 times
- Minimal MVP observability and failure surfacing

### Out of Scope
- Raw HTML retention
- Snapshot/evidence storage
- Separate scraped-job staging table requiring manual promotion
- User-triggered manual run-now control
- Email, push, or in-app notifications for newly found jobs
- Additional job sources beyond Indeed and LinkedIn
- Final ranking algorithm beyond the best-fit product direction
- Final archive exception behavior for jobs with existing user workflow activity

## Core Product Decisions
- Scraping lives outside the main application codebase
- Scraped jobs and email-ingested jobs share the same job record model
- Scraping is an additional source, not a separate workflow
- Search ownership is per user, self-serve, and configured in-product for MVP
- Sourcing runs once per day per user automatically
- Daily sourcing defaults to an overnight run in the user's configured timezone, with implementation-level jitter allowed to avoid load spikes
- Users cannot manually run a profile in MVP
- The dashboard remains the only surface for newly found jobs in MVP
- Scraping should fetch full job details, not just search snippets
- Duplicate jobs from different sources should merge into one canonical job record
- Only normalized fields/data should be stored for MVP
- Quality is the MVP success target

## User Experience Requirements

### Search Profile Management
Users must be able to:
- create unlimited search profiles
- edit search profiles
- pause/disable a profile
- re-enable a paused profile
- define the full search input set listed above
- use a search profile as a saved filter over all jobs in the database

Default profile behavior:
- JobTrakr should suggest a readable profile name from the criteria.
- The user can edit the suggested name.
- Pausing a profile stops future scheduled discovery but does not remove historical profile labels from jobs already matched.

### `/jobs` Workspace
- `/jobs` should use a full-width grid layout
- selecting a job should open a right-side sliding drawer
- sourced jobs should appear in the main grid, not a separate tab
- dashboard default view should show all jobs, not a single search profile
- jobs should show matched search profile names in the row, drawer, or full detail view when available
- if a job matches multiple profiles, all matched profiles should be represented
- users should be able to distinguish Email, Indeed, and LinkedIn sources via visible source filtering or chips
- default ordering should favor best fit
- empty dashboard state should guide the user to create a search profile first

## Functional Requirements

### Source Service
- The system runs as a separate microservice in a separate repository
- The system scrapes Indeed and LinkedIn
- The system performs full-detail extraction for matched jobs
- The system writes normalized job data directly into the JobTrakr database
- The system associates sourced jobs with the user and the matched search profile
- The system retries failed daily runs up to 3 times

### Search Profiles
- User can create more than one search profile
- User can create unlimited search profiles
- User can set title, location, remote preference, companies to include/exclude, salary, seniority, employment type, and include/exclude keywords
- User can pause/disable a profile without deleting it
- Disabled profiles do not run during scheduled sourcing
- Disabled profiles remain visible for editing, re-enabling, and historical match context
- Search profiles can be used as saved filters over all database jobs

### Deduplication and Merge
- The system must deduplicate within scraped results
- The system must merge duplicate jobs across scraped and email-ingested lanes
- The user should not see duplicate job entries for the same opportunity
- The merged record must preserve enough provenance to show that multiple sources contributed to the record

### Persistence
The shared job model must support at least:
- source type
- source platform
- source URL
- external source job ID when available
- matched search profile reference
- scrape timestamp
- provenance needed for source visibility in the UI
- dedupe fingerprint or equivalent merge key

### Archiving
- If a sourced job later disappears from the source site, the system should archive it
- Final exception behavior for jobs with existing user workflow activity is still TBD

### Observability
- MVP observability can stay light
- The service should expose enough logs/status to diagnose failed daily runs and retry behavior
- Rich dashboards and evidence retention are not required for MVP

## Risks
- Indeed and LinkedIn anti-bot defenses may reduce reliability
- Aggressive scraping increases operational and compliance risk
- Deduplicating across email and scraped sources may be harder than source-local dedupe
- Best-fit ordering depends on downstream fit quality and ranking semantics that are not fully locked yet
- Archiving disappeared jobs may create edge cases when the user already interacted with the job

## Open Questions
- Exact source-provenance fields to add to the shared jobs schema
- Exact best-fit ranking logic for default sorting in `/jobs`
- Archive behavior when a disappeared job already has user workflow activity

## Recommended MVP Cut
Ship the smallest end-to-end lane that proves value:
- in-product search profile management
- one automated daily run per user
- Indeed + LinkedIn full-detail scraping
- direct write into shared jobs model
- duplicate merge
- source visibility in `/jobs`
- best-fit-first default ordering
- full-width grid + right-side detail drawer review flow
