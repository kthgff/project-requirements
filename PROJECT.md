# PROJECT.md

## Project

- **Name:** JobTrakr
- **Status:** Discovery complete, MVP specification in progress
- **Owner:** Keith Goff
- **PM:** OC
- **Started:** April 2026

## Current Position

JobTrakr has moved from vague concept into defined MVP discovery. Core product direction is now clear, and the foundational PM specs are being assembled.

## Current Phase

**Phase 2: MVP specification**

## Overall Progress

- Phase 1: Discovery and product definition, **complete**
- Phase 2: MVP requirements and specs, **in progress**
- Phase 3: Engineering handoff package, **not started**
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
- Good-fit action defined as **flag it**, not auto-apply
- Initial statuses defined:
  - `new`
  - `flagged`
  - `reviewing`
  - `skipped`
  - `applied`
  - `interview`
  - `rejected`
  - `offer`

### PM artifacts completed
- PRD v1 created
- MVP stories v1 created
- Data model v1 created
- Gmail ingestion spec v1 created
- Fit analysis spec v1 created

## What Is In Progress

### MVP specification completion
Still needed to complete the core PM handoff package:
- table UI spec
- engineering handoff summary
- backlog breakdown by implementation phase
- open decision cleanup

## What Is Not Started

### Engineering handoff
- API and worker contract recommendations
- implementation sequencing guidance
- dependency map
- milestone-based delivery plan

### Build phase
- repo and architecture decisions
- Gmail integration implementation
- ingestion pipeline implementation
- resume upload implementation
- fit analysis implementation
- job table UI implementation

### QA and launch
- test plan
- acceptance review
- bug triage
- launch checklist
- post-launch metrics review

## Active Risks / Gaps

- job detail enrichment source is not yet fully specified
- deduplication rules are defined conceptually but not yet translated into implementation guidance
- table UI behavior is not yet specified in detail
- engineering sequence and milestones are not yet defined

## Next Recommended Steps

1. Create the table UI spec
2. Create the engineering handoff summary
3. Break MVP into implementation milestones
4. Hand off to Engineering

## Key Files

- `prd/jobtrakr-prd-v1.md`
- `stories/jobtrakr-mvp-stories-v1.md`
- `specs/jobtrakr-data-model-v1.md`
- `specs/jobtrakr-gmail-ingestion-spec-v1.md`
- `specs/jobtrakr-fit-analysis-spec-v1.md`

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
- [ ] Define table UI spec
- [ ] Resolve remaining open product questions

### Phase 3. Engineering handoff
- [ ] Create engineering handoff summary
- [ ] Break work into implementation milestones
- [ ] Define recommended build order
- [ ] Identify dependencies and blockers
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
