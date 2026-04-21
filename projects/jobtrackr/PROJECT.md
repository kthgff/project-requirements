# PROJECT.md

## Project

- **Name:** JobTrakr
- **Status:** Discovery complete, MVP specification and engineering handoff largely complete
- **Owner:** Keith Goff
- **PM:** OC
- **Started:** April 2026

## Current Position

JobTrakr has moved from vague concept into defined MVP discovery. Core product direction is now clear, the canonical PM contracts are locked for engineering pickup, and TypeScript is the implementation language direction.

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
- Good-fit handling defined as a **fit signal surfaced in the UI**, not auto-apply and not a workflow-state change
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
- final drift cleanup where older specs and handoff notes still differ from the canonical workflow, archive, and fit-signal contracts
- tracker alignment so task status reflects actual implementation progress
- final historical-doc cleanup so preserved drafts and clarification notes point back to the canonical source-of-truth set instead of leaving legacy fit-flag shorthand open to interpretation

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

1. Finish reconciling PROJECT, milestone, and handoff references against the canonical workflow and fit-signal contract
2. Lock the first shared schema and API interfaces for the raw-ingestion vertical slice
3. Keep the engineering handoff entrypoints current as implementation pickup accelerates
4. Move the first build slice into active coding with verifiable completion states

## Key Files

- `projects/jobtrackr/ROADMAP.md`
- `DEVELOPMENT_PLAN.md`
- `specs/jobtrackr-pm-decision-memo-2026-04-19.md`
- `specs/jobtrackr-gate-a-reconciliation-matrix-2026-04-20.md`
- `specs/jobtrackr-api-contract.md`
- `specs/jobtrackr-detail-view-contract-2026-04-20.md`
- `specs/jobtrackr-list-detail-contract-examples-2026-04-20.md`
- `specs/jobtrackr-workspace-ux-contract-2026-04-20.md`
- `specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`

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

## Source-of-Truth Reminder

Use these files as the canonical implementation references when PROJECT, older drafts, or preserved clarification notes summarize the work at a higher level:
- `DEVELOPMENT_PLAN.md`
- `specs/jobtrackr-pm-decision-memo-2026-04-19.md`
- `specs/jobtrackr-gate-a-reconciliation-matrix-2026-04-20.md`
- `specs/jobtrackr-api-contract.md`
- `specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`

Historical references to fit flags, flagged jobs, or older workflow examples should be read as legacy shorthand only, not as current product-state vocabulary.

## Notes

This file is the live source of truth for where the JobTrakr project stands. It should be updated whenever meaningful progress is made.
