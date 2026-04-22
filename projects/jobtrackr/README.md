# JobTrakr Requirements Workspace

Product requirements, PRDs, user stories, milestone plans, and engineering handoff specs for JobTrakr.

## Current Status

JobTrakr is in **Phase 3: Engineering handoff and kickoff**, with the first real implementation lane now centered on the authenticated auth to Gmail to persisted-jobs vertical slice.

This repository is no longer discovery-only. Engineers should treat the canonical PM memo, API contract, Gate A reconciliation matrix, active development plan, and current handoff package as the source of truth for implementation pickup.

## Start Here

If you are picking up implementation or reconciliation work, use this recovery order first:

1. `projects/jobtrackr/DEVELOPMENT_PLAN.md`
2. `projects/jobtrackr/PROJECT.md`
3. `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`
4. `projects/jobtrackr/specs/jobtrackr-pm-decision-memo-2026-04-19.md`
5. `projects/jobtrackr/specs/jobtrackr-gate-a-reconciliation-matrix-2026-04-20.md`
6. `projects/jobtrackr/specs/jobtrackr-api-contract.md`

If an hourly kickoff prompt still points to `~/Documents/project-requirements/DEVELOPMENT_PLAN.md`, treat that as stale drift and recover to the project-root files above.

## Kickoff Build Slice

The original kickoff UI slice started with:
- Google login
- authenticated session creation
- protected dashboard shell
- jobs dashboard table with mock data

The **current implementation pickup direction** extends that into the first real vertical slice:
- Google login
- authenticated session creation
- Gmail readonly connection
- persisted jobs retrieval
- jobs dashboard rendering server-backed results

Current lane split for this slice:
- Alice and Marcus: frontend owners for the current slice
- remaining SWE lanes: backend and shared contract follow-through

Use `projects/jobtrackr/specs/jobtrackr-first-build-slice-v1.md` as historical kickoff context only, and use `projects/jobtrackr/DEVELOPMENT_PLAN.md` plus `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md` for the current lane and ownership guidance. Frontend pickup should also treat these detail-continuity docs as part of the live kickoff set:
- `projects/jobtrackr/specs/jobtrackr-detail-view-contract-2026-04-20.md`
- `projects/jobtrackr/specs/jobtrackr-list-detail-contract-examples-2026-04-20.md`
- `projects/jobtrackr/specs/jobtrackr-workspace-ux-contract-2026-04-20.md`

All work should still follow canonical workflow and fit-signal language:
- workflow status is only `new`, `interested`, `applied`, `interviewing`, `offer`, `rejected`
- match rating and low-fit are fit signals, not workflow states
- archive behavior remains separate from workflow status

## Structure

All live kickoff files below are project-root paths, not stale workspace-root shorthand:

```
projects/jobtrackr/
├── README.md            — workspace entrypoint and source-of-truth pointers
├── PROJECT.md           — project overview, roadmap, and status
├── DEVELOPMENT_PLAN.md  — active engineering task tracker and milestone plan
├── prd/                 — Product Requirements Documents
├── stories/             — User stories and acceptance criteria
├── specs/               — Technical specs, contracts, and handoff docs
└── BLOCKERS.md          — active blocker tracker
```

## Roles

| Agent | Role |
|---|---|
| PM | Owns PRDs, user stories, backlog, and canonical product decisions |
| Engineer | Owns specs, contracts, implementation planning, and reconciliation work |
| QA | Owns test plans, blocker validation, and acceptance review |


## Frontend continuity reminder

For the current frontend slice, treat drawer and full-page detail as one behavior surface. Notes must use explicit save/cancel, tags must edit in context, and selected-row continuity should follow the canonical list-to-detail examples instead of older preserved wording.

## Ticket Tracking

GitHub issues are now the primary ticket system for JobTrakr.

- Repo: `kthgff/project-requirements`
- Use GitHub issues for active ticket assignment, status, and discussion
- The markdown development plan remains useful as a planning and milestone artifact, but GitHub should be treated as the operational source for tickets
- Legacy `T-###` IDs may remain in docs for continuity, but new execution updates should point to GitHub issue numbers and links
