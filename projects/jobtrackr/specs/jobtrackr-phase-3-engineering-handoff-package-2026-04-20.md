# JobTrakr Phase 3 Engineering Handoff Package — 2026-04-20

## Purpose

Provide one source-of-truth handoff package for engineering so hourly kickoff and task pickup do not require reconstructing state from multiple files.

## Current Delivery Status

- Project phase: MVP specification finishing, engineering handoff package being finalized
- Active handoff owner: Frank
- Immediate goal: close the gap between PM-approved contracts and implementation pickup guidance
- Primary risk: contract drift across older docs if teams implement from stale assumptions

## Canonical Source of Truth

Engineers should use these files in this order when there is any conflict.

1. `projects/jobtrackr/DEVELOPMENT_PLAN.md`
   - live task ownership, active branches, dependencies, and delivery sequencing
2. `projects/jobtrackr/specs/jobtrackr-pm-decision-memo-2026-04-19.md`
   - canonical product decisions where older specs differ
3. `projects/jobtrackr/specs/jobtrackr-api-contract.md`
   - shared API payload expectations
4. `projects/jobtrackr/specs/jobtrackr-detail-view-contract-2026-04-20.md`
   - locked detail view structure and editing expectations
5. `projects/jobtrackr/specs/jobtrackr-workspace-ux-contract-2026-04-20.md`
   - locked workspace interaction expectations
6. `projects/jobtrackr/specs/jobtrackr-dependency-map-and-sequencing-2026-04-19.md`
   - milestone dependencies and parallel-safe sequencing
7. `projects/jobtrackr/specs/jobtrackr-milestones-1-to-4-engineering-tickets-2026-04-20.md`
   - implementation-ready ticket map for Milestones 1 to 4
8. `projects/jobtrackr/stories/jobtrackr-in-progress-test-cases-2026-04-19.md`
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
- In closeout, with T-017 carrying the final reconciliation pass

**Engineering rule**
Do not implement new state handling from older model docs until Gate A references are reconciled.

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
Focus on the first backend vertical slice.

Priority order:
1. T-018 auth callback, session flow, Gmail readonly connection path
2. T-002 only where it directly supports the first end-to-end slice
3. T-011 after auth and Gmail connection state are stable

### Marcus
Focus on shared contracts that unblock backend and detail UI alignment.

Priority order:
1. T-015 detail-view contract as the shared source for section order and editing behavior
2. T-019 detail UI alignment against that contract
3. Keep T-001 and T-004 aligned so list/detail payload semantics do not drift

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

## Milestones 1 to 4 Summary

### Milestone 1
Auth foundation only.

### Milestone 2
Protected shell and dashboard UI with fixtures.

### Milestone 3
Schema correction, canonical jobs contracts, repository/query layer, then frontend switch from mocks.

### Milestone 4
Gmail connection state contract, readonly OAuth wiring, source-email ingestion, extraction plus dedupe, and dashboard refresh against real ingestion output.

## Recommended Next Picks By Lane

- Frank: finish Gate A reconciliation and keep engineering package current
- Alice: auth callback, session flow, Gmail readonly connect path
- Marcus: detail contract examples and detail editing alignment
- Priya: row-selection continuity, active-filter context, empty-result recovery

## Open Risks To Watch

- Older docs may still imply stale workflow semantics or archive behavior
- Gmail connection state can drift if reconnect and error handling fields are not shared consistently
- Job detail UI and API can drift if detail examples diverge from the locked contract
- Hourly kickoff becomes noisy if this package stops reflecting the actual latest canonical files

## Definition of a Ready Engineering Handoff

The handoff is ready when:
- source-of-truth files are explicit and ordered
- Gate A and Gate B status is clear
- each lane has a concrete next pickup
- milestone tickets map to actual engineering work
- QA expectations are linked before live integration accelerates

## Maintenance Note

Update this file whenever canonical source files change, new gate blockers appear, or pickup guidance changes meaningfully.
