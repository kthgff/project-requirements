# JobTrakr Requirements Workspace

Product requirements, PRDs, user stories, milestone plans, and engineering handoff specs for JobTrakr.

## Current Status

JobTrakr is in **Phase 3: Engineering handoff and kickoff**, with the first real implementation lane now centered on the authenticated auth to Gmail to persisted-jobs vertical slice.

This repository is no longer discovery-only. Engineers should treat the canonical PM memo, API contract, Gate A reconciliation matrix, active development plan, and current handoff package as the source of truth for implementation pickup.

## Start Here

If you are picking up implementation or reconciliation work, read these files first:

1. `DEVELOPMENT_PLAN.md`
2. `PROJECT.md`
3. `specs/jobtrackr-pm-decision-memo-2026-04-19.md`
4. `specs/jobtrackr-gate-a-reconciliation-matrix-2026-04-20.md`
5. `specs/jobtrackr-api-contract.md`
6. `specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`

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

Use `specs/jobtrackr-first-build-slice-v1.md` as historical kickoff context only, and use `DEVELOPMENT_PLAN.md` plus `specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md` for the current lane and ownership guidance. All work should still follow canonical workflow and fit-signal language:
- workflow status is only `new`, `interested`, `applied`, `interviewing`, `offer`, `rejected`
- match rating and low-fit are fit signals, not workflow states
- archive behavior remains separate from workflow status

## Structure

```
/
├── README.md           — workspace entrypoint and source-of-truth pointers
├── PROJECT.md          — project overview, roadmap, and status
├── DEVELOPMENT_PLAN.md — active engineering task tracker and milestone plan
├── prd/               — Product Requirements Documents
├── stories/           — User stories and acceptance criteria
├── specs/             — Technical specs, contracts, and handoff docs
└── BLOCKERS.md        — active blocker tracker
```

## Roles

| Agent | Role |
|---|---|
| PM | Owns PRDs, user stories, backlog, and canonical product decisions |
| Engineer | Owns specs, contracts, implementation planning, and reconciliation work |
| QA | Owns test plans, blocker validation, and acceptance review |
