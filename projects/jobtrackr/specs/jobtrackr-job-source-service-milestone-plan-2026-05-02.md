# JobTrakr Job Source Service Milestone Plan

## Purpose
Break the new job source service workstream into implementation-ready milestones for a separate repository while keeping integration with the shared JobTrakr data model explicit.

## Workstream summary
This service is a separate microservice and a separate repository.
Its job is to:
- run once-daily per-user sourcing
- scrape Indeed and LinkedIn aggressively
- collect full job detail
- normalize and deduplicate records
- merge into the shared JobTrakr jobs model
- preserve source provenance
- archive jobs that disappear from source sites

## Delivery principles
- ship the smallest end-to-end value first
- keep scraped jobs in the shared jobs model from day one
- prefer explicit provenance over hidden heuristics
- keep observability light but sufficient for failed-run diagnosis
- do not overbuild ranking logic in milestone 1

## Milestone 1 — Shared schema and repo foundation
### Goal
Create the new repository, lock the shared-schema changes, and make one no-op service path capable of authenticating its environment and touching the shared database safely.

### Scope
- create new repo for the job source service
- define service config model
- define environment/secrets contract
- create schema migration plan for `job_sources`, `job_source_runs`, and `search_profiles` updates as needed
- lock canonical provenance semantics with the main JobTrakr API/data model
- add health endpoint and minimal run logging

### Exit criteria
- repository exists
- schema/provenance spec is implemented or migration-ready
- service can connect to the target database safely
- one dry-run job-source run can be created and logged without scraping

### Key dependencies
- shared database access approach
- alignment on migration ownership between app repo and service repo

## Milestone 2 — Search profile management contract
### Goal
Enable the main app and the new service to share one clear model for per-user search profiles.

### Scope
- finalize search profile schema
- define app-facing CRUD contract for search profiles
- support multiple profiles per user
- support enable/disable behavior
- persist run scheduling fields like `last_run_at`

### Exit criteria
- search profiles can be created, updated, listed, enabled, and disabled
- the source service can query enabled profiles per user
- once-daily scheduling inputs are available in persisted form

### Key dependencies
- app-side UX implementation for profile management
- final API ownership decision for search profile CRUD

## Milestone 3 — Indeed connector MVP
### Goal
Get one real source working end to end through the new service into the shared jobs model.

### Scope
- implement Indeed search execution from saved profiles
- fetch search-result pages
- follow matched jobs to full-detail pages
- normalize fields into canonical job payloads
- write jobs and provenance rows to the shared database
- dedupe within Indeed runs and against existing jobs

### Exit criteria
- one scheduled run can source Indeed jobs for a test user
- jobs appear in the main JobTrakr `/jobs` grid
- duplicate jobs are not visibly created
- source provenance is attached

### Key dependencies
- schema/provenance support from Milestone 1
- search profile support from Milestone 2

## Milestone 4 — LinkedIn connector MVP
### Goal
Add the second required source and prove the service architecture is source-extensible.

### Scope
- implement LinkedIn search execution from saved profiles
- fetch full detail for matched jobs
- normalize LinkedIn-specific fields
- write/merge into shared jobs model
- preserve provenance and dedupe behavior across sources

### Exit criteria
- one scheduled run can source LinkedIn jobs for a test user
- duplicate jobs across LinkedIn, Indeed, and Gmail sources merge cleanly when signals match
- UI source filtering can distinguish LinkedIn results

### Key dependencies
- stable canonical write path from Milestone 3

## Milestone 5 — Scheduling, retries, and archive-on-disappear
### Goal
Turn source connectors into an actual daily service.

### Scope
- once-daily scheduling per user
- retry failed runs up to 3 times
- track run status and error summary
- implement disappear detection at the source-provenance layer
- archive jobs when all active source rows are gone/missing according to MVP rules

### Exit criteria
- daily runs execute automatically
- failed runs retry automatically
- missing/disappeared sourced jobs archive correctly in standard cases
- minimal logs are sufficient to diagnose failed runs

### Key dependencies
- stable connectors for Indeed and LinkedIn
- final archive semantics for jobs with workflow activity if needed before release

## Milestone 6 — `/jobs` integration and quality validation
### Goal
Make sure sourced jobs feel native in the main product.

### Scope
- verify primary source rendering in list/grid views
- verify source filters/chips for Email / Indeed / LinkedIn
- verify right-drawer detail view provenance rendering
- verify best-fit-first default ordering works with sourced jobs included
- validate quality over quantity against sample profiles

### Exit criteria
- sourced jobs are visually and behaviorally integrated into `/jobs`
- source filtering works
- provenance appears in the drawer/detail experience
- PM sign-off confirms quality is acceptable for MVP

### Key dependencies
- app-side UI readiness
- fit/ranking pipeline integration

## Milestone 7 — Launch readiness
### Goal
Prepare the new service for controlled MVP release.

### Scope
- define operational runbook
- define known limitations and failure modes
- validate retry behavior and anti-duplicate behavior
- validate archive-on-disappear behavior
- confirm no raw HTML/snapshot retention in MVP

### Exit criteria
- service runbook exists
- QA checklist exists
- known risks are documented
- release recommendation is clear

## Suggested build order
1. shared schema/provenance foundation
2. search profile management contract
3. Indeed connector
4. LinkedIn connector
5. scheduling/retries/archive flow
6. `/jobs` integration validation
7. launch readiness

## Recommended engineering split
- one lane for shared schema + integration contract
- one lane for Indeed connector
- one lane for LinkedIn connector after shared patterns are stable
- one lane for app-side search-profile and `/jobs` integration work

## Open implementation questions
- whether search profile CRUD should live in the main app API, the new service, or both
- exact best-fit ranking logic used by `/jobs`
- archive exception behavior when a disappeared job already has user workflow activity
- runtime approach for high-friction scraping environments

## PM recommendation
Do not wait for every open technical detail before starting.
The right kickoff move is:
- lock schema and provenance first
- prove one source end to end
- then scale to the second source and daily operations

That gets us to real product value fast without getting trapped in theoretical scraper architecture debates.
