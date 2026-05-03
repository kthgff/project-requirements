# PROJECT.md

## Project

- **Name:** JobTrakr
- **Status:** Discovery complete, MVP specification and engineering handoff largely complete
- **Owner:** Keith Goff
- **PM:** OC
- **Started:** April 2026

## Current Position

JobTrakr has moved from vague concept into defined MVP discovery. Core product direction is now clear, the canonical PM contracts are locked for engineering pickup, and the active implementation lane is the Next.js web plus Go API slice covering Google auth, app session, Gmail readonly connect, and persisted jobs retrieval.

A new top-priority discovery track is now open for a separate **job source service** that will scrape jobs from Indeed and LinkedIn. This service is explicitly planned as a separate microservice in a new repository, with JobTrakr consuming its normalized job output rather than embedding scraper logic in the main application.

Current lane split for this slice:
- Alice and Marcus: frontend owners for the current slice
- Alice: primary frontend delivery on the auth -> session -> Gmail readonly connect -> persisted-jobs experience
- Marcus: primary frontend continuity owner for kickoff docs, detail continuity entrypoints, and safe hourly pickup recovery
- remaining SWE lanes: backend and shared contract follow-through

Frontend continuity for this slice should stay anchored to:
- `projects/jobtrackr/specs/jobtrackr-detail-view-contract-2026-04-20.md`
- `projects/jobtrackr/specs/jobtrackr-list-detail-contract-examples-2026-04-20.md`
- `projects/jobtrackr/specs/jobtrackr-workspace-ux-contract-2026-04-20.md`
- `projects/jobtrackr/specs/jobtrackr-sourced-jobs-frontend-continuity-contract-2026-05-02.md`

Live frontend recovery note:
- Recover in this order: `projects/jobtrackr/DEVELOPMENT_PLAN.md`, `projects/jobtrackr/PROJECT.md`, `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`
- Jimmy's latest hourly guidance keeps the live QA surface narrowed to T-106 for the server-backed persisted-jobs workspace and T-095 for source-email persistence, with stale external kickoff drift still the only non-repo blocker called out
- Current frontend handoff checkpoints: T-106 is the live server-backed workspace handoff, T-107 is the current QA handoff summary, T-151 is Marcus's locked sourced-jobs continuity contract, T-152 is the completed app-side source chips/source filters handoff now waiting on QA, and the remaining blocker story stays narrowed to T-095 fixture-backed validation plus stale external kickoff drift
- Current frontend QA entrypoint: `projects/jobtrackr/specs/jobtrackr-persisted-jobs-workspace-signoff-checklist-2026-04-23.md` (T-116)
- Current backend/shared-contract QA entrypoint paired with that live frontend gate: `projects/jobtrackr/specs/jobtrackr-source-email-persistence-signoff-checklist-2026-04-22.md` (T-102)
- Canonical frontend continuity docs for follow-through after T-106 and T-107:
  - `projects/jobtrackr/specs/jobtrackr-detail-view-contract-2026-04-20.md`
  - `projects/jobtrackr/specs/jobtrackr-list-detail-contract-examples-2026-04-20.md`
  - `projects/jobtrackr/specs/jobtrackr-workspace-ux-contract-2026-04-20.md`
  - `projects/jobtrackr/specs/jobtrackr-sourced-jobs-frontend-continuity-contract-2026-05-02.md`
- Current sourced-jobs UI handoff: T-152 implemented compact source chips, merged-source labels, source filters, source-aware empty/filter summaries, and README/test coverage in `apps/web`; keep follow-through inside the canonical `/jobs` workspace rather than creating a separate sourced-jobs queue
- Current sourced-jobs UI validation handoff: `projects/jobtrackr/specs/jobtrackr-t154-sourced-jobs-ui-validation-handoff-2026-05-03.md` turns the T-154 lens into an ordered `/jobs` walkthrough for source chips, merged provenance, source filters, empty states, and drawer/full-detail continuity; pair it with `projects/jobtrackr/specs/jobtrackr-t159-sourced-jobs-ui-walkthrough-evidence-pointers-2026-05-03.md` and the T-160 repo-side evidence pass at `projects/jobtrackr/specs/jobtrackr-t160-sourced-jobs-ui-validation-evidence-2026-05-03.md` when recording or reviewing walkthrough evidence
- Current persisted full-detail QA reference for the completed T-133 and T-137 follow-through: `projects/jobtrackr/specs/jobtrackr-persisted-job-detail-signoff-checklist-2026-04-28.md`

Live backend/shared-contract recovery note:
- Recover in this order: `projects/jobtrackr/DEVELOPMENT_PLAN.md`, `projects/jobtrackr/PROJECT.md`, `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`
- Current backend/shared-contract pickup: T-091 remains the live source-email provenance and ingestion follow-through lane for Frank
- Current backend/shared-contract QA entrypoint: `projects/jobtrackr/specs/jobtrackr-source-email-persistence-signoff-checklist-2026-04-22.md` (T-102) for the live T-095 fixture-backed QA path
- Current QA and debug handoff docs for provenance work:
  - `projects/jobtrackr/specs/jobtrackr-source-email-persistence-evidence-2026-04-22.md` for the repo-side proof bundle behind T-095
  - `projects/jobtrackr/stories/jobtrackr-in-progress-test-cases-2026-04-19.md` for the active QA coverage and T-108 source-email debug-read test cases
  - `projects/jobtrackr/specs/jobtrackr-t144-sourced-jobs-schema-provenance-validation-lens-2026-05-02.md` for the T-144 sourced-jobs schema/provenance QA validation lens
  - `projects/jobtrackr/specs/jobtrackr-t152-sourced-jobs-ui-continuity-validation-lens-2026-05-03.md` for the T-152 source-chip, merged-provenance, and source-filter QA validation lens
  - `projects/jobtrackr/specs/jobtrackr-t154-sourced-jobs-ui-validation-handoff-2026-05-03.md` for the T-154 execution handoff that turns the lens into a concrete `/jobs` walkthrough sequence
  - `projects/jobtrackr/specs/jobtrackr-t158-source-service-provenance-validation-handoff-2026-05-03.md` for the backend/shared-contract validation path across T-144 schema provenance, T-149 cross-source merge, T-150 connector provenance, and T-155 debug-read alignment

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

## New Top-Priority Discovery Track

### Job source service
- Goal: add a second acquisition lane beyond Gmail alerts by sourcing jobs directly from Indeed and LinkedIn
- Boundary: separate microservice, separate repository, outside the main JobTrakr application codebase
- Current state: discovery answers are now captured in the project plan, PRD, MVP stories, shared-schema/provenance spec, milestone plan, and GitHub ticket breakdown:
  - `projects/jobtrackr/specs/jobtrackr-job-source-service-project-plan-2026-05-02.md`
  - `projects/jobtrackr/prd/jobtrackr-job-source-service-prd-2026-05-02.md`
  - `projects/jobtrackr/stories/jobtrackr-job-source-service-mvp-stories-2026-05-02.md`
  - `projects/jobtrackr/specs/jobtrackr-job-source-schema-and-provenance-spec-2026-05-02.md`
  - `projects/jobtrackr/specs/jobtrackr-job-source-service-milestone-plan-2026-05-02.md`
  - `projects/jobtrackr/specs/jobtrackr-job-source-service-ticket-breakdown-2026-05-02.md`
- Locked so far: direct writes into the JobTrakr database, shared jobs model with email-ingested jobs, per-user search ownership, self-serve in-app search configuration for MVP, broad search-profile fields, unlimited/pauseable profiles, automatic once-daily sourcing per user, no MVP manual run-now control, dashboard-only surfacing for newly found jobs, profile matching across all jobs in the database, all matched-profile labels preserved per job, aggressive full-detail scraping, duplicate merge behavior across sources, normalized-fields-only persistence, source filtering plus best-fit sorting in `/jobs`, archive-on-disappear behavior, quality as the MVP success target, light observability, a canonical MVP UI palette in `projects/jobtrackr/specs/jobtrackr-ui-palette-direction-2026-05-02.md`, and `/jobs` workspace behavior that uses a full-width grid with a right-side sliding detail drawer
- Immediate next step: continue execution from the completed T-144 through T-152 sourcing spine by validating the T-152 app-side source chips/filter handoff, then pick the next smallest `/jobs` continuity follow-through without forking sourced jobs into a separate queue

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
- `projects/jobtrackr/specs/jobtrackr-digitalocean-production-hosting-plan-2026-04-23.md`
- `projects/jobtrackr/specs/jobtrackr-job-source-service-project-plan-2026-05-02.md`
- `projects/jobtrackr/prd/jobtrackr-job-source-service-prd-2026-05-02.md`
- `projects/jobtrackr/stories/jobtrackr-job-source-service-mvp-stories-2026-05-02.md`
- `projects/jobtrackr/specs/jobtrackr-job-source-schema-and-provenance-spec-2026-05-02.md`
- `projects/jobtrackr/specs/jobtrackr-job-source-service-milestone-plan-2026-05-02.md`
- `projects/jobtrackr/specs/jobtrackr-job-source-service-ticket-breakdown-2026-05-02.md`
- `projects/jobtrackr/specs/jobtrackr-ui-palette-direction-2026-05-02.md`
- `projects/jobtrackr/specs/jobtrackr-t158-source-service-provenance-validation-handoff-2026-05-03.md`
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

If an hourly kickoff or preserved note still points to `~/Documents/project-requirements/DEVELOPMENT_PLAN.md`, treat that as stale automation drift and recover to the ordered project-root files above. Jimmy's latest hourly note re-confirmed that this dead root-level path is external coordination drift, not a repo-side source-of-truth change.

If you are resuming frontend continuity after the latest server-backed workspace handoff, check T-106 and T-107 in `projects/jobtrackr/DEVELOPMENT_PLAN.md` first, then move into the canonical detail-view, list-to-detail, and workspace continuity contracts before touching preserved frontend planning notes.

Historical references to fit flags, flagged jobs, or older workflow examples should be read as legacy shorthand only, not as current product-state vocabulary.

## Notes

This file is the live source of truth for where the JobTrakr project stands. It should be updated whenever meaningful progress is made.
