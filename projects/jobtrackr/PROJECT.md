# PROJECT.md

## Project

- **Name:** JobTrakr
- **Status:** Discovery complete, MVP specification and engineering handoff largely complete
- **Owner:** Keith Goff
- **PM:** OC
- **Started:** April 2026

## Current Position

JobTrakr has moved from vague concept into defined MVP discovery. Core product direction is now clear, the foundational PM specs are being assembled, and TypeScript has been chosen as the project language direction.

## Current Phase

**Phase 3: Engineering handoff and kickoff**

## Overall Progress

- Phase 1: Discovery and product definition, **complete**
- Phase 2: MVP requirements and specs, **mostly complete**
- Phase 3: Engineering handoff package, **in progress**
- Phase 4: Build and implementation, **not started**
- Phase 5: QA and launch readiness, **not started**
- Phase 6: Launch and post-launch iteration, **not started**

## What Is Done

### Discovery
- Project named **JobTrakr**
- Primary user defined as **Keith Goff**
- Core problem defined: job searching and applying takes too long
- Core input source defined: **Gmail inbox job alert emails**
- Supported email sources clarified to include **all relevant alerts**, including **LinkedIn** and **Indeed**
- Resume source defined as **uploaded file**
- Main initial UI defined as **table view**
- Main jobs page should be a **searchable jobs table backed by database data**
- Each job should include a **match percentage** showing alignment with Keith's resume and known profile
- Project language direction defined as **TypeScript**
- Good-fit action defined as **flag it**, not auto-apply
- Canonical workflow statuses defined:
  - `new`
  - `interested`
  - `applied`
  - `interviewing`
  - `offer`
  - `rejected`
- Separate state concepts locked:
  - `saved` for shortlist behavior
  - `archivedAt` for archive behavior

### PM artifacts completed
- PRD v1 created
- MVP stories v1 created
- Data model v1 created
- Gmail ingestion spec v1 created
- Fit analysis spec v1 created
- Auth and login spec v1 created
- Frontend plan v1 created
- Table UI spec v1 created
- Engineering handoff v1 created
- Implementation milestones v1 created
- First build slice v1 created

## What Is In Progress

### PM cleanup
Still needed:
- open decision cleanup where specs and development-plan semantics differ
- tracker alignment so task status reflects actual implementation progress

### Engineering kickoff
In progress:
- engineering development plan actively populated with implementation tasks
- dependency map and milestone sequencing being finalized
- frontend and auth kickoff slices prepared for development

## What Is Not Started

### Build phase not yet verified complete
- Gmail integration implementation
- ingestion pipeline implementation
- resume upload implementation
- fit analysis implementation
- job table UI implementation
- status updates and filters implementation

### QA and launch
- test plan
- acceptance review
- bug triage
- launch checklist
- post-launch metrics review

## Active Risks / Gaps

- ~~job detail enrichment source is not yet fully specified~~ **Answered: scrape from job detail page linked in email**
- dependency map across implementation milestones still needs to be finalized
- some older docs still expose stale workflow semantics and need cleanup against the Gate A canonical set
- ~~Google auth access policy is not yet finalized, single approved account vs broader sign-in~~ **Answered: allow anyone with Google account**

## Next Recommended Steps

1. Reconcile status and fit semantics between PM specs and development plan
2. Lock the first shared schema and API interfaces for the raw-ingestion vertical slice
3. Confirm TypeScript framework choices with Engineering
4. Move the first build slice into active coding with verifiable completion states

## Key Files

- `prd/jobtrakr-prd-v1.md`
- `stories/jobtrakr-mvp-stories-v1.md`
- `specs/jobtrakr-data-model-v1.md`
- `specs/jobtrakr-gmail-ingestion-spec-v1.md`
- `specs/jobtrakr-fit-analysis-spec-v1.md`
- `projects/jobtrackr/scaffolding-plan.md`
- `projects/jobtrackr/gmail-auth-ingestion-implementation-package.md`
- `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`

## Project Plan to Completion

### Phase 1. Discovery and product definition
- [x] Define the product idea
- [x] Define the primary user
- [x] Define the core problem
- [x] Define the MVP direction
- [x] Define initial statuses
- [x] Define resume source
- [x] Define core UI direction

### Phase 2. MVP specification
- [x] Write PRD
- [x] Write MVP user stories
- [x] Define data model
- [x] Define Gmail ingestion spec
- [x] Define fit analysis spec
- [x] Define auth and login spec
- [x] Define frontend plan
- [x] Define table UI spec
- [ ] Resolve remaining open product questions

### Phase 3. Engineering handoff
- [x] Create engineering handoff summary
- [x] Break work into implementation milestones
- [x] Define recommended build order
- [x] Identify dependencies and blockers
- [ ] Hand off final PM package to Engineering

### Phase 4. Build and implementation
- [ ] Set up code repositories and environment
- [ ] Implement Gmail auth and inbox scanning
- [ ] Implement email classification and extraction
- [ ] Implement job enrichment and deduplication
- [ ] Implement database schema and persistence
- [ ] Implement resume upload flow
- [ ] Implement fit analysis pipeline
- [ ] Implement table UI and job detail view
- [ ] Integrate status updates and filters

### Phase 5. QA and launch readiness
- [ ] Define acceptance checklist
- [ ] Validate MVP against stories and specs
- [ ] Fix critical bugs
- [ ] Confirm usability of end-to-end workflow
- [ ] Prepare launch checklist

### Phase 6. Launch and iteration
- [ ] Launch MVP
- [ ] Review real usage and pain points
- [ ] Prioritize post-launch improvements
- [ ] Plan v2 enhancements

## Notes

This file is the live source of truth for where the JobTrakr project stands. It should be updated whenever meaningful progress is made.
