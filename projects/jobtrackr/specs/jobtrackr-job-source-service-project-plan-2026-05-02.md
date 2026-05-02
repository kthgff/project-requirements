# JobTrakr Job Source Service Project Plan

## Status
- **Priority:** Top priority
- **State:** Discovery in progress
- **Owner:** Jimmy (PM)
- **Created:** 2026-05-02

## Objective
Create a separate microservice, in a separate repository, that sources jobs for JobTrakr by scraping external job platforms.

## What is already decided
- This is a **new service**, not part of the main application
- This will live in a **new repository**
- The service will **scrape jobs from Indeed and LinkedIn**
- The service exists to **source jobs for JobTrakr**

## Product intent
JobTrakr already handles job ingestion from email alerts. This new service adds a second acquisition channel: direct job discovery from external job boards.

That means the product now has two distinct job-source lanes:
1. **Inbound alerts** via Gmail ingestion
2. **Outbound sourcing** via web scraping

## Working assumptions
These are provisional until Keith confirms them:
- The new service should normalize scraped jobs into a schema JobTrakr can consume
- The service should deduplicate jobs before or during handoff
- The service should be independently deployable and operable
- The service should not own end-user workflow state such as `interested`, `applied`, or `rejected`
- The main app should remain the system of record for user-facing job workflow

## Confirmed decisions
- Scraped jobs should be written **directly into the JobTrakr database**
- The immediate product goal is for sourced jobs to appear in the **front-end application**
- The main application should read and display these records rather than requiring a separate import review step for MVP
- Scraped jobs should use the **same jobs table / model** as email-ingested jobs
- Scraping is an **additional source of jobs**, not a separate job domain
- Search configuration should be **owned per user**, not as one global operator-managed search set
- Users should **configure their own search profiles in the app for MVP**
- MVP search profiles should support **job title, location, remote preference, salary, seniority, employment type, and keyword include/exclude controls**
- Sourcing should run **automatically on a schedule** for MVP
- MVP sourcing cadence should be **once per day per user**
- Scraping posture for MVP should be **aggressive** to maximize coverage
- The new UI should follow a **Coolors trending UI color palette direction**
- Canonical MVP palette selected in `projects/jobtrackr/specs/jobtrackr-ui-palette-direction-2026-05-02.md`
- The `/jobs` page should use a **full-width grid layout**
- Selecting a job in `/jobs` should open a **right-side sliding drawer** for detail review

## Scope for this planning pass
### In scope
- Service boundary and repository boundary
- Source coverage for Indeed and LinkedIn
- Data contract between scraper service and JobTrakr
- High-level ingestion flow
- Operational risks and anti-blocking questions
- Phased delivery plan

### Out of scope for now
- Final implementation language
- Final hosting choice
- Detailed scraper runtime design
- Detailed anti-bot strategy
- Exact persistence model inside the new service

## Key product questions to resolve
1. How freshness, deduplication, and retry rules should work
2. Whether the service stores raw HTML / snapshots / extraction evidence
3. What level of observability and failure handling is required for MVP
4. What source-provenance fields the shared jobs schema needs so the UI and backend can distinguish email-sourced vs scraped jobs cleanly

## Proposed architecture direction
### Recommended boundary
Create a dedicated **job-source-service** repository with one responsibility:
- fetch jobs from supported external sources
- extract and normalize job data
- deduplicate and annotate source metadata
- write normalized job records directly into the JobTrakr database through a controlled persistence contract

### Why this boundary is right
- scraping logic will change faster than the core product
- source-specific breakage should not destabilize the main app
- anti-bot, rate limiting, proxies, and extraction logic need isolated operational handling
- the team can version and deploy scraper changes independently

## Initial MVP source flow
1. User defines one or more search profiles with title, location, remote preference, salary, seniority, employment type, and keyword include/exclude rules
2. A once-daily scheduled job runs source-specific collection against Indeed and LinkedIn for that user's profile set
3. Extract structured job data
4. Normalize into the shared jobs payload
5. Deduplicate against previously seen jobs
6. Write accepted jobs into JobTrakr
7. Log source, fetch time, user ownership, matched search profile, and extraction status for traceability

## MVP output shape to define
The service will likely need to produce at least:
- source type (`scraped` vs existing email ingestion lane)
- source platform (`indeed`, `linkedin`)
- source URL
- external job ID if available
- title
- company
- location
- remote/hybrid/on-site signal if detectable
- compensation text if available
- full description/content
- scrape timestamp
- normalized metadata
- dedupe fingerprint

## Risks to plan around
- LinkedIn and Indeed anti-bot defenses may create reliability risk
- Source markup drift will break parsers over time
- Terms-of-service / compliance constraints may affect approach
- Duplicate jobs across sources will be common
- Search result scraping and detail-page scraping may require different strategies
- Login requirements or geo/rate constraints may affect coverage

## Recommended phased plan
### Phase 1 — Product and interface definition
- lock the service goal
- define the handoff contract into JobTrakr
- define supported search inputs
- define success metrics for sourced jobs
- define compliance and operating constraints

### Phase 2 — Source strategy
- document Indeed collection approach
- document LinkedIn collection approach
- define search-result vs detail-page extraction flow
- define rate limiting, retry, and failure behavior

### Phase 3 — Service contract and repository setup
- create new repo
- define service API / queue / batch handoff contract
- define normalized job schema
- define dedupe rules
- define operator configuration model

### Phase 4 — MVP build
- implement first source connector
- implement second source connector
- add normalization and dedupe
- add delivery into JobTrakr
- add logging and health visibility

### Phase 5 — QA and launch readiness
- validate source coverage
- validate duplicate handling
- validate failed extraction handling
- validate main-app ingestion contract
- define operational runbook

## Decisions Keith still needs to make
- search-result-only vs full-detail scraping depth
- duplicate merge behavior across email and scraped sources
- raw evidence retention level
- multi-profile vs single-profile-per-user limit for MVP
- profile pause/disable behavior
- source visibility and filtering behavior in `/jobs`
- default sort behavior for sourced jobs
- treatment of jobs that later disappear from source sites
- retry behavior after failed daily runs
- MVP success criteria

## Deliverables this plan should produce next
- PRD for the job source service
- service interface contract
- source strategy memo for Indeed and LinkedIn
- UI/brand direction note including the selected Coolors palette
- jobs workspace UX contract update for full-width grid plus right-side drawer behavior
- MVP story set
- implementation milestone plan for the new repository

## Current recommendation
Treat this as a **separate product workstream inside JobTrakr**, but not as part of the main application codebase. The main app should consume sourced jobs; the new service should specialize in finding and extracting them.
