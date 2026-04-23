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

If an hourly kickoff prompt still points to `~/Documents/project-requirements/DEVELOPMENT_PLAN.md`, treat that as stale drift and recover to the project-root files above. Jimmy's latest hourly note re-confirmed that this dead root-level path is external coordination drift, not a repo-side source-of-truth change.

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
- Alice: primary frontend delivery on the auth -> session -> Gmail readonly connect -> persisted-jobs experience
- Marcus: primary frontend continuity owner for kickoff docs, detail continuity entrypoints, and safe hourly pickup recovery
- remaining SWE lanes: backend and shared contract follow-through

Live frontend recovery note:
- Recover in this order: `projects/jobtrackr/DEVELOPMENT_PLAN.md`, `projects/jobtrackr/PROJECT.md`, `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`
- Jimmy's latest hourly guidance keeps the live QA surface narrowed to T-106 for the server-backed persisted-jobs workspace and T-095 for source-email persistence, with stale external kickoff drift still the only non-repo blocker called out
- Current frontend handoff checkpoints: T-106 is the live server-backed workspace handoff, T-107 is the current QA handoff summary, and the remaining blocker story stays narrowed to T-095 fixture-backed validation plus stale external kickoff drift
- Current frontend QA entrypoint: `projects/jobtrackr/specs/jobtrackr-persisted-jobs-workspace-signoff-checklist-2026-04-23.md` (T-116)
- Current backend/shared-contract QA entrypoint paired with that live frontend gate: `projects/jobtrackr/specs/jobtrackr-source-email-persistence-signoff-checklist-2026-04-22.md` (T-102)
- Canonical frontend continuity docs for follow-through after T-106 and T-107:
  - `projects/jobtrackr/specs/jobtrackr-detail-view-contract-2026-04-20.md`
  - `projects/jobtrackr/specs/jobtrackr-list-detail-contract-examples-2026-04-20.md`
  - `projects/jobtrackr/specs/jobtrackr-workspace-ux-contract-2026-04-20.md`

Live backend/shared-contract recovery note:
- Recover in this order: `projects/jobtrackr/DEVELOPMENT_PLAN.md`, `projects/jobtrackr/PROJECT.md`, `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`
- Current backend/shared-contract pickup: T-091 remains the live source-email provenance and ingestion follow-through lane for Frank
- Current backend/shared-contract QA entrypoint: `projects/jobtrackr/specs/jobtrackr-source-email-persistence-signoff-checklist-2026-04-22.md` (T-102) for the live T-095 fixture-backed QA path
- Current QA and debug handoff docs for provenance work:
  - `projects/jobtrackr/specs/jobtrackr-source-email-persistence-evidence-2026-04-22.md` for the repo-side proof bundle behind T-095
  - `projects/jobtrackr/stories/jobtrackr-in-progress-test-cases-2026-04-19.md` for the active QA coverage and T-108 source-email debug-read test cases

Use `projects/jobtrackr/specs/jobtrackr-first-build-slice-v1.md` as historical kickoff context only, and use `projects/jobtrackr/DEVELOPMENT_PLAN.md` plus `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md` for the current lane and ownership guidance.

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

If you are picking up frontend continuity after the latest server-backed workspace handoff, start from `projects/jobtrackr/DEVELOPMENT_PLAN.md` for T-106 and T-107 context, then use the canonical detail-view, list-to-detail, and workspace continuity docs above before touching preserved frontend planning notes.

## Ticket Tracking

GitHub issues are the long-term operational ticket system for JobTrakr, but hourly recovery still starts from the live markdown handoff set because external kickoff prompts, QA notes, and preserved specs recover through those project-root files first.

- Repo: `kthgff/project-requirements`
- Use GitHub issues for durable ticket assignment, status, discussion, and eventual issue-link handoff
- Use `projects/jobtrackr/DEVELOPMENT_PLAN.md` as the hourly recovery and claim ledger until the automation-facing prompts and implementation-facing docs stop keying off the `T-###` plan flow
- If the live development plan has no truly unclaimed task that fits the current lane, add the smallest safe follow-through task explicitly before claiming it instead of taking over a QA-owned or already-completed row
- Legacy `T-###` IDs may remain in docs for continuity, and recovery updates should keep those IDs explicit until GitHub issue links replace the current hourly plan references end to end
