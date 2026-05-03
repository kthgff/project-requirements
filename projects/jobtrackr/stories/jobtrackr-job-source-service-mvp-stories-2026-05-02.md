# JobTrakr Job Source Service MVP Stories

> Source-service story set for the new scraping workstream.
> Canonical decisions currently live in:
> - `projects/jobtrackr/PROJECT.md`
> - `projects/jobtrackr/specs/jobtrackr-job-source-service-project-plan-2026-05-02.md`
> - `projects/jobtrackr/prd/jobtrackr-job-source-service-prd-2026-05-02.md`

## Epic 1: Search profile management

### Story 1
As a JobTrakr user, I want to create my own search profiles so that the system can source jobs that match what I actually want.

**Acceptance criteria**
- I can create a search profile in the app
- A profile supports job title, location, remote preference, companies to include/exclude, salary, seniority, employment type, include keywords, and exclude keywords
- JobTrakr suggests a readable profile name from the criteria, and I can edit it
- The profile saves successfully and is associated with my account

### Story 2
As a JobTrakr user, I want to create unlimited search profiles so that I can source jobs for different role targets.

**Acceptance criteria**
- I can create as many profiles as I need
- Each profile is listed separately
- Each profile can run independently in scheduled sourcing
- Each profile can also be used as a saved filter over all jobs in the database

### Story 3
As a JobTrakr user, I want to pause a search profile without deleting it so that I can stop sourcing temporarily.

**Acceptance criteria**
- I can disable a profile from the app
- Disabled profiles are excluded from daily sourcing
- I can re-enable a disabled profile later
- Existing jobs keep their historical matched-profile labels when a profile is disabled

## Epic 2: Scheduled sourcing

### Story 4
As a JobTrakr user, I want the system to source jobs automatically every day so that I do not have to run searches manually.

**Acceptance criteria**
- The system runs sourcing once per day per user
- The run uses that user’s enabled search profiles
- The run covers Indeed and LinkedIn in MVP
- Daily sourcing defaults to overnight in the user's configured timezone
- I cannot manually run a profile on demand in MVP

### Story 5
As a JobTrakr user, I want failed sourcing runs retried automatically so that one transient failure does not cause me to miss a day of opportunities.

**Acceptance criteria**
- Failed daily runs retry automatically
- The system retries up to 3 times
- Retry outcomes are logged for diagnosis

## Epic 3: Scraping and normalization

### Story 6
As a JobTrakr user, I want full job details captured from sourced jobs so that each job is worth reviewing inside the app.

**Acceptance criteria**
- The system scrapes full job detail pages for matched jobs
- The system stores normalized title, company, location, description/content, source URL, and scrape timestamp when available
- The system stores seniority, employment type, compensation, and remote/hybrid/on-site signals when detectable

### Story 7
As a JobTrakr user, I want sourced jobs written directly into JobTrakr so that they appear in the same workspace as my email-derived jobs.

**Acceptance criteria**
- Sourced jobs write directly into the shared jobs model
- No separate import approval step is required in MVP
- The front end can read and render sourced jobs through the existing jobs experience
- The dashboard is the only notification surface for newly found jobs in MVP

## Epic 4: Deduplication and merge

### Story 8
As a JobTrakr user, I want the system to avoid duplicate jobs so that my workspace stays clean.

**Acceptance criteria**
- Duplicate jobs within a scrape run do not create multiple job records
- Duplicate jobs across different scrape runs do not create multiple job records
- Duplicate jobs across email ingestion and scraping merge into one job record

### Story 9
As a JobTrakr user, I want merged jobs to retain source provenance so that I can still tell where a job came from.

**Acceptance criteria**
- A merged job can show Email, Indeed, and/or LinkedIn provenance in the UI
- The canonical job record remains singular
- Provenance survives later updates to the same job

## Epic 5: Jobs workspace behavior

### Story 10
As a JobTrakr user, I want the dashboard to show all jobs by default so that I start from the full opportunity set.

**Acceptance criteria**
- The dashboard default view shows all jobs in the database that are visible to me
- The default view is not scoped to a single search profile
- If no jobs or search profiles exist yet, the dashboard empty state directs me to create a search profile

### Story 11
As a JobTrakr user, I want to see which search profiles matched a job so that I understand why it appears in my workspace.

**Acceptance criteria**
- A job can show matched search profile names in the row, drawer, or full detail view
- If a job matches multiple profiles, all matched profiles are preserved and can be shown
- Paused profiles can still appear as historical match labels

### Story 12
As a JobTrakr user, I want sourced jobs visible in the main `/jobs` grid so that all opportunities are reviewed in one place.

**Acceptance criteria**
- `/jobs` uses a full-width grid layout
- Sourced jobs appear alongside email-derived jobs
- Users can filter or visually distinguish Email, Indeed, and LinkedIn sources

### Story 13
As a JobTrakr user, I want to inspect job details in a drawer so that I can review jobs without losing my place in the grid.

**Acceptance criteria**
- Selecting a job opens a drawer sliding in from the right
- The selected job’s detail is shown in the drawer
- Closing the drawer returns me to the same grid context

### Story 14
As a JobTrakr user, I want the default ordering to bias toward best-fit jobs so that the highest-quality opportunities rise to the top.

**Acceptance criteria**
- The default `/jobs` ordering favors best fit
- The ranking model can incorporate sourced jobs and email-derived jobs together
- Exact ranking logic may evolve without changing the product promise that best-fit opportunities surface first

## Epic 6: Archive behavior

### Story 15
As a JobTrakr user, I want jobs that disappear from the source site archived so that stale sourced jobs do not clutter my active workspace.

**Acceptance criteria**
- The system detects when a previously sourced job is no longer available from the source site
- The system archives that job
- Archive handling for jobs with existing workflow activity remains a follow-up edge-case decision

## Epic 7: MVP operations

### Story 16
As the team, we want lightweight operational visibility so that daily sourcing failures can be diagnosed without overbuilding platform tooling.

**Acceptance criteria**
- Failed runs are logged
- Retry attempts are logged
- Minimal status/error visibility exists for debugging MVP issues
- Raw HTML or snapshot retention is not required
