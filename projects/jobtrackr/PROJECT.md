# PROJECT.md

## Project

- **Name:** JobTrakr
- **Status:** Discovery complete, MVP specification and engineering handoff largely complete
- **Owner:** Keith Goff
- **PM:** OC
- **Started:** April 2026

## Current Position

JobTrakr has moved from vague concept into defined MVP discovery. Core product direction is now clear, the canonical PM contracts are locked for engineering pickup, and the active implementation lane is the Next.js web plus Go API slice covering Google auth, app session, Gmail readonly connect, and persisted jobs retrieval.

Current lane split for this slice:
- Alice and Marcus: frontend owners for the current slice
- Alice: primary frontend delivery on the auth -> session -> Gmail readonly connect -> persisted-jobs experience
- Marcus: primary frontend continuity owner for kickoff docs, detail continuity entrypoints, and safe hourly pickup recovery
- remaining SWE lanes: backend and shared contract follow-through

Frontend continuity for this slice should stay anchored to:
- `projects/jobtrackr/specs/jobtrackr-detail-view-contract-2026-04-20.md`
- `projects/jobtrackr/specs/jobtrackr-list-detail-contract-examples-2026-04-20.md`
- `projects/jobtrackr/specs/jobtrackr-workspace-ux-contract-2026-04-20.md`

Latest frontend handoff checkpoints from Jimmy's current plan:
- T-106 is the live frontend handoff for the server-backed dashboard and jobs workspace slice
- T-107 is the current QA handoff summary for that frontend slice
- the remaining QA blocker story is still narrowed to T-095 fixture-backed validation, plus stale external kickoff drift when prompts reference the dead root-level development-plan path

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
- Local/operator Gmail discovery may use the **gog CLI** to search job-related emails and persist them as source-email records before parsing into jobs
- Gmail job-alert discovery should support **GOG-powered search of job-related emails** so inbox search can reliably find digest and alert messages before parsing and persistence
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
- kickoff entrypoint cleanup so README, handoff docs, and milestone guidance all point at the same current implementation slice and valid project-root paths

### Engineering kickoff
In progress:
- engineering development plan actively populated with implementation tasks
- dependency map and milestone sequencing being finalized
- auth to Gmail to persisted-jobs vertical slice is now the active implementation lane
- Alice and Marcus are now explicitly split onto frontend work, while the remaining SWE lanes stay focused on backend and shared contract follow-through
- kickoff entrypoints are being reconciled so engineers do not start from stale mock-only framing

## What Is Not Started

### Build phase not yet verified complete
- Gmail integration implementation
- GOG-powered Gmail search and ingestion pipeline implementation
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
- the current ingestion docs need to explicitly call out how GOG-based Gmail search feeds the persisted jobs database so implementation does not stop at inbox search only
- ~~Google auth access policy is not yet finalized, single approved account vs broader sign-in~~ **Answered: allow anyone with Google account**

## Next Recommended Steps

1. Finish reconciling PROJECT, README, milestone, and handoff references against the canonical workflow, fit-signal contract, and current auth to Gmail to persisted-jobs pickup lane
2. Keep the frontend ownership split explicit in kickoff-facing docs so Marcus and Alice land on the same live source-of-truth set during hourly recovery
3. Keep kickoff-facing docs pointing frontend pickup back to T-106, T-107, and the canonical detail-view, list-to-detail, and workspace continuity contracts so drawer, route, notes, tags, and row-selection behavior do not drift
4. Lock the first shared schema and API interfaces for the raw-ingestion and persisted-jobs vertical slice
5. Add explicit product and engineering guidance for GOG-based Gmail job-email discovery, raw message capture, parsing, deduplication, and persistence into the jobs database
6. Keep the engineering handoff entrypoints current as implementation pickup accelerates
7. Move the current auth and persisted-jobs slice forward with verifiable completion states

## Key Files

- `projects/jobtrackr/COMPETITOR_NOTES_AIAPPLY.md`
- `projects/jobtrackr/ROADMAP.md`
- `projects/jobtrackr/DEVELOPMENT_PLAN.md`
- `projects/jobtrackr/specs/jobtrackr-pm-decision-memo-2026-04-19.md`
- `projects/jobtrackr/specs/jobtrackr-gate-a-reconciliation-matrix-2026-04-20.md`
- `projects/jobtrackr/specs/jobtrackr-api-contract.md`
- `projects/jobtrackr/specs/jobtrackr-detail-view-contract-2026-04-20.md`
- `projects/jobtrackr/specs/jobtrackr-list-detail-contract-examples-2026-04-20.md`
- `projects/jobtrackr/specs/jobtrackr-workspace-ux-contract-2026-04-20.md`
- `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`
- `projects/jobtrackr/specs/jobtrackr-frontend-plan-v1.md` (preserved, should defer to the canonical detail and list-to-detail contracts)
- `projects/jobtrackr/specs/jobtrackr-table-ui-spec-v1.md` (preserved, should defer to the canonical detail and list-to-detail contracts)

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
- [ ] Implement Gmail auth, GOG-powered inbox search, and inbox scanning
- [ ] Implement email classification and extraction
- [ ] Implement raw Gmail message capture plus parser pipeline for job-alert emails
- [ ] Implement job enrichment and deduplication
- [ ] Implement database schema and persistence for raw source emails, parsed jobs, and email-to-job linkage
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
1. `projects/jobtrackr/DEVELOPMENT_PLAN.md`
2. `projects/jobtrackr/PROJECT.md`
3. `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`
4. `projects/jobtrackr/specs/jobtrackr-pm-decision-memo-2026-04-19.md`
5. `projects/jobtrackr/specs/jobtrackr-gate-a-reconciliation-matrix-2026-04-20.md`
6. `projects/jobtrackr/specs/jobtrackr-api-contract.md`

Current lane split for this slice:
- Alice and Marcus: frontend owners for the current slice
- Alice: primary frontend delivery on the auth -> session -> Gmail readonly connect -> persisted-jobs experience
- Marcus: primary frontend continuity owner for kickoff docs, detail continuity entrypoints, and safe hourly pickup recovery
- remaining SWE lanes: backend and shared contract follow-through

If an hourly kickoff or preserved note still points to `~/Documents/project-requirements/DEVELOPMENT_PLAN.md`, treat that as stale automation drift and recover to the ordered project-root files above.

If you are resuming frontend continuity after the latest server-backed workspace handoff, check T-106 and T-107 in `projects/jobtrackr/DEVELOPMENT_PLAN.md` first, then move into the canonical detail-view, list-to-detail, and workspace continuity contracts before touching preserved frontend planning notes.

Historical references to fit flags, flagged jobs, or older workflow examples should be read as legacy shorthand only, not as current product-state vocabulary.

## Notes

This file is the live source of truth for where the JobTrakr project stands. It should be updated whenever meaningful progress is made.
