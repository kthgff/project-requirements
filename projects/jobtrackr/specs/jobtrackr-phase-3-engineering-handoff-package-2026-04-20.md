# JobTrakr Phase 3 Engineering Handoff Package — 2026-04-20

## Purpose

Provide one source-of-truth handoff package for engineering so hourly kickoff and task pickup do not require reconstructing state from multiple files.

## Current Delivery Status

- Project phase: Phase 3 engineering handoff and kickoff, with the first real implementation lane now active
- Active handoff owner: Frank
- Immediate goal: close the gap between PM-approved contracts and implementation pickup guidance while keeping kickoff entrypoints aligned to the current auth to Gmail to persisted-jobs slice
- Primary risk: contract drift across older docs if teams implement from stale assumptions or from stale kickoff paths

## Canonical Source of Truth

Engineers should use these files in this order when there is any conflict.

Recovery note for automation-facing handoffs:
- if an hourly kickoff prompt still points to `~/Documents/project-requirements/DEVELOPMENT_PLAN.md`, treat that path as stale drift and recover in this order:
  1. `~/Documents/project-requirements/projects/jobtrackr/DEVELOPMENT_PLAN.md`
  2. `~/Documents/project-requirements/projects/jobtrackr/PROJECT.md`
  3. `~/Documents/project-requirements/projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`
- when a kickoff note says to start with PROJECT, use `~/Documents/project-requirements/projects/jobtrackr/PROJECT.md` so the recovery path stays explicit alongside the live development plan
- the current implementation slice for pickup remains Next.js web app -> Go API -> Google auth -> session -> Gmail readonly connect -> persisted jobs UI
- current lane ownership for this slice:
  - Alice and Marcus are the frontend owners for the current slice.
  - Alice: primary frontend delivery on the auth -> session -> Gmail readonly connect -> persisted-jobs experience
  - Marcus: primary frontend continuity owner for kickoff docs, detail continuity entrypoints, and safe hourly pickup recovery
  - Frank: source-of-truth maintenance across roadmap-facing and preserved planning docs so the live kickoff set and current slice wording stay aligned
  - remaining SWE lanes: backend and shared contract follow-through

1. `projects/jobtrackr/DEVELOPMENT_PLAN.md`
   - live task ownership, active branches, dependencies, and delivery sequencing
2. `projects/jobtrackr/PROJECT.md`
   - current project status, phase wording, and kickoff entrypoint guidance
3. `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`
   - single-file engineering handoff entrypoint for hourly recovery
4. `projects/jobtrackr/specs/jobtrackr-pm-decision-memo-2026-04-19.md`
   - canonical product decisions where older specs differ
5. `projects/jobtrackr/specs/jobtrackr-gate-a-reconciliation-matrix-2026-04-20.md`
   - compact Gate A drift check for workflow, archive, fit-nullability, and source-email linkage semantics
6. `projects/jobtrackr/specs/jobtrackr-api-contract.md`
   - shared API payload expectations
7. `projects/jobtrackr/specs/jobtrackr-detail-view-contract-2026-04-20.md`
   - locked detail view structure and editing expectations
8. `projects/jobtrackr/specs/jobtrackr-list-detail-contract-examples-2026-04-20.md`
   - canonical examples for list selection, detail continuity, and edit behavior alignment
   - if you encounter `jobtrackr-list-detail-examples-2026-04-20.md`, treat it as a compatibility shim that defers to this canonical file
9. `projects/jobtrackr/specs/jobtrackr-workspace-ux-contract-2026-04-20.md`
   - locked workspace interaction expectations
10. `projects/jobtrackr/specs/jobtrackr-dependency-map-and-sequencing-2026-04-19.md`
   - milestone dependencies and parallel-safe sequencing
11. `projects/jobtrackr/specs/jobtrackr-milestones-1-to-4-engineering-tickets-2026-04-20.md`
   - implementation-ready ticket map for Milestones 1 to 4
12. `projects/jobtrackr/stories/jobtrackr-in-progress-test-cases-2026-04-19.md`
   - current QA expectations for in-flight slices

## Contract Gates

### Gate A: Canonical schema and workflow correction

**Goal**
Lock shared state semantics before more backend and frontend coupling lands.

**Must agree everywhere**
- workflow states and archive behavior
- fit nullability before analysis runs
- one-source-email-to-many-jobs linkage
- dashboard-facing canonical job fields

**Status**
- In closeout, with T-017 carrying the final reconciliation pass and T-025 now publishing the compact reconciliation matrix engineers can use before pickup

**Engineering rule**
Do not implement new state handling from older model docs until Gate A references are reconciled.
Use `specs/jobtrackr-gate-a-reconciliation-matrix-2026-04-20.md` as the quick drift check before implementation or review.

### Gate B: Shared dashboard query and Gmail connection contracts

**Goal**
Lock request and response semantics before mock-driven UI moves to live backend integration.

**Must agree everywhere**
- jobs list request and response shape
- job detail response shape
- Gmail connection state shape
- sync request and accepted/running response semantics

**Status**
- Mostly defined, but should be treated as locked only through the current 04-20 contract docs plus API contract

**Engineering rule**
Frontend may continue on mocks, but should not couple to live endpoints until Gate B fields and examples are accepted.

## Lane-by-Lane Pickup Guidance

### Frank
Focus on PM-to-engineering coordination artifacts.

Priority order:
1. Close T-017 so Gate A semantics are explicitly reconciled across canonical specs
2. Finish T-010 so Milestones 1 to 4 remain implementation-ready, not just milestone labels
3. Finish T-014 so Milestone 4 parser and ingestion follow-through is broken into concrete pickup tasks
4. Keep this handoff package current so the repo always has one single-file engineering entrypoint

### Alice
Focus on the frontend side of the auth -> session -> Gmail readonly connect -> persisted-jobs slice, plus the automation-facing recovery notes that keep hourly kickoff safe.

Priority order:
1. T-022 wire the authenticated frontend shell to the auth session and Gmail connection endpoints
2. T-029 authenticated persisted-jobs vertical slice once auth and connection state are usable
3. T-055 surface canonical persisted-jobs fallback reasons in the frontend while fit data is still incomplete
4. T-064 and T-069 automation-facing kickoff recovery cleanup where stale root-level path examples could misroute pickup
5. T-011 after auth, Gmail connection state, and persisted-job rendering are stable

### Marcus
Focus on frontend continuity, kickoff-entrypoint alignment, and safe hourly recovery so frontend pickup stays anchored to the canonical detail-view, list-to-detail, and workspace continuity contracts.

Priority order:
1. T-078 normalize README, PROJECT, and the handoff package so Marcus and Alice show up explicitly as the frontend owners for the current slice, with Marcus called out as the frontend continuity owner for kickoff-facing recovery
2. T-061 reconcile README, PROJECT, and kickoff handoff entrypoints around the live auth -> session -> Gmail readonly connect -> persisted jobs slice
3. Keep README, PROJECT, and the handoff package pointing frontend pickup back to the canonical detail-view, list-to-detail, and workspace continuity contracts
4. T-065 keep preserved kickoff docs clearly historical and recoverable to the live project-root source set
5. T-068 normalize README, PROJECT, and handoff package path examples so they all use the same explicit `projects/jobtrackr/...` entrypoints
6. T-072 remove remaining bare-path and lane-wording ambiguity across kickoff-facing docs during hourly recovery
7. Keep T-001 and T-004 aligned so list/detail payload semantics do not drift while kickoff docs stabilize

### Priya
Focus on workspace behavior in the mock-data frontend slice.

Priority order:
1. T-016 workspace UX contract as frontend source of truth
2. T-020 row-selection continuity against that contract
3. T-013 active-filter context and zero-result recovery

## Immediate Team-Wide Rules

- Latest contract docs override older assumptions.
- Frontend can keep moving with fixtures while backend contracts harden.
- No one should implement from stale workflow states, archive semantics, or fit defaults.
- Empty results are not the same as no-data state.
- Detail view should follow the locked section order.
- Row-selection persistence should follow the workspace continuity rules.
- Frontend kickoff docs should point engineers at the canonical detail-view, list-to-detail, and workspace continuity contracts before they rely on preserved frontend plans.

## Milestones 1 to 4 Summary

### Milestone 1
Auth foundation only.

### Milestone 2
Protected shell and dashboard UI with fixtures.

### Milestone 3
Schema correction, canonical jobs contracts, repository/query layer, then frontend switch from mocks.

### Milestone 4
Gmail connection state contract, readonly OAuth wiring, source-email ingestion, extraction plus dedupe, and dashboard refresh against real ingestion output.

### Current kickoff reality
The team is no longer only preparing a mock-first shell. The active cross-lane direction is the authenticated auth to Gmail to persisted-jobs slice, with README, PROJECT, and kickoff docs expected to reflect that current pickup lane.

## Recommended Next Picks By Lane

- Frank: finish Gate A reconciliation and keep engineering package current
- Alice: auth callback, session flow, Gmail readonly connect path
- Marcus: kickoff-entrypoint path normalization, preserved-doc recovery cleanup, and explicit lane wording alignment so hourly recovery always lands on the live project-root source set
- Priya: row-selection continuity, active-filter context, empty-result recovery

## Open Risks To Watch

- Older docs may still imply stale workflow semantics or archive behavior, especially the auto-close and older story docs called out in the Gate A reconciliation matrix
- Gmail connection state can drift if reconnect and error handling fields are not shared consistently
- Job detail UI and API can drift if detail examples diverge from the locked contract
- Hourly kickoff becomes noisy if this package stops reflecting the actual latest canonical files
- Engineers can still pick up stale workflow or fit-language assumptions if PROJECT, milestone docs, and handoff docs drift apart

## Definition of a Ready Engineering Handoff

The handoff is ready when:
- source-of-truth files are explicit and ordered
- Gate A and Gate B status is clear
- each lane has a concrete next pickup
- milestone tickets map to actual engineering work
- QA expectations are linked before live integration accelerates

## Maintenance Note

Update this file whenever canonical source files change, new gate blockers appear, pickup guidance changes meaningfully, or entrypoint-doc reconciliation changes what engineers should read first.
