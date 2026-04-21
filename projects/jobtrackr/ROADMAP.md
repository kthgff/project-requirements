# JobTrakr Roadmap

## Product Goal

Help Keith move from inbound job email to prioritized job review faster by automatically tracking jobs, evaluating fit, and making the search workflow visible in one place.

## Product Principles

- ship working slices fast
- keep the first version simple
- optimize for time saved, not feature count
- use AI to accelerate triage, not replace user judgment

---

## Now: Execution Shift

### Goal
Move from requirements into implementation with the smallest useful vertical slice.

### Success looks like
- team is coding against one clear build direction
- first slice is actively under construction
- roadmap is used to sequence work, not just document ideas

---

## Release 0: Foundation and First Usable Shell

### Objective
Create the first usable product shell with authentication and a visible jobs workspace.

### Scope
- Google login
- protected app shell
- dashboard page
- searchable jobs table with mock data
- basic layout, loading, and empty states

### Outcome
Keith can log in and see the intended product shape, even before real data flows through it.

### Exit criteria
- login works
- protected dashboard works
- searchable jobs table renders with required columns
- mock data supports UI development

---

## Release 1: Real Jobs Workspace MVP

### Objective
Replace mock data with real persisted job data and make the dashboard operational.

### Scope
- database schema
- jobs list API
- job detail API
- dashboard connected to DB-backed jobs
- search, sorting, filtering, pagination
- status visibility and updates

### Outcome
Keith can use JobTrakr as a real job database and review workspace.

### Exit criteria
- jobs dashboard reads from the database
- jobs are searchable
- jobs can be filtered and sorted
- status changes persist

---

## Release 2: Gmail Ingestion MVP

### Objective
Populate the jobs workspace automatically from Gmail.

### Scope
- Google Gmail readonly connection
- inbox scanning
- candidate email detection
- source email persistence
- job parsing
- deduplication
- job upsert into database

### Outcome
JobTrakr starts creating job records from actual inbound job emails.

### Exit criteria
- Gmail connection works
- relevant emails create job records
- duplicate obvious repeats do not create duplicate active rows

---

## Release 3: Resume and Match Intelligence

### Objective
Add resume-based fit evaluation so jobs can be prioritized intelligently.

### Scope
- resume upload
- resume parsing
- match percentage generation
- canonical fit-signal derivation (for example low-fit, fit-ready, and strong-fit)
- fit rationale in job detail
- match % visible in jobs table

### Outcome
Keith can quickly identify which jobs align best with his background.

### Exit criteria
- each analyzed job gets a match percentage
- dashboard clearly surfaces fit signals such as low-fit, fit-ready, or strong-fit without treating them as workflow states
- fit rationale is shown in detail view

---

## Release 4: End-to-End Review Workflow

### Objective
Complete the day-to-day workflow for reviewing and managing jobs.

### Scope
- richer job detail experience
- notes and tags if kept in scope
- source link access
- workflow cleanup and status semantics
- UX polish across dashboard and detail flow

### Outcome
Keith can review, organize, and manage jobs end to end inside the product.

### Exit criteria
- job review workflow feels complete
- key job actions are available without leaving the app unnecessarily

---

## Release 5: Quality, Reliability, and Launch Readiness

### Objective
Make the MVP stable enough for regular use.

### Scope
- QA pass against stories and specs
- bug fixing
- auth edge case cleanup
- ingestion reliability improvements
- launch checklist
- usage validation

### Outcome
JobTrakr is ready for regular real-world use.

### Exit criteria
- critical flows work reliably
- major blockers are resolved
- launch checklist is complete

---

## Release 6: Post-MVP Improvements

### Candidate directions
- smarter ranking beyond basic match %
- alerts/notifications for strong matches
- saved searches or job collections
- better duplicate clustering
- application workflow support
- interview pipeline tracking
- resume variants and tailoring

---

## Recommended Immediate Priorities

Current source-of-truth kickoff set:
- `projects/jobtrackr/DEVELOPMENT_PLAN.md`
- `projects/jobtrackr/PROJECT.md`
- `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`

Current implementation slice:
- Google auth -> session -> Gmail readonly connect -> persisted jobs UI

1. Finish execution cleanup so engineering codes against one source of truth
2. Complete the auth -> session -> Gmail readonly connect -> persisted-jobs slice before widening scope
3. Move immediately into the real persisted-jobs workspace so the dashboard stops depending on mock-only flows
4. Add Gmail ingestion before spending heavily on advanced AI features
5. Add match intelligence once enough real job data exists

---

## Risks to Watch

- too much time spent refining docs after the build direction is clear
- drift between PM specs and engineering semantics
- overbuilding AI features before core ingestion and workspace usability are solid
- auth and Gmail permission friction slowing adoption

---

## PM Guidance

This roadmap should be treated as a sequencing tool, not a promise of exact dates. The main goal is to keep the team building the next highest-value slice without losing alignment.

If roadmap wording conflicts with implementation-facing docs, defer to the current source-of-truth kickoff set above and keep fit signals separate from workflow status vocabulary.
