# JobTrackr In-Progress and Recent Completion Test Cases — 2026-04-22

## Purpose
Test cases for work currently marked `in-progress` in `projects/jobtrackr/DEVELOPMENT_PLAN.md`, updated for Jimmy's latest kickoff emphasis on QA recovery wording normalization, kickoff-entrypoint safety, and the active auth -> session -> Gmail readonly connect -> persisted jobs implementation slice.

## Shared QA recovery note:
- If a kickoff prompt or older note still points to `~/Documents/project-requirements/DEVELOPMENT_PLAN.md`, treat that as stale external drift.
- Recover in this order: `projects/jobtrackr/DEVELOPMENT_PLAN.md`, `projects/jobtrackr/PROJECT.md`, `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`.
- The active implementation context is Next.js web plus Go API on the current auth -> session -> Gmail readonly connect -> persisted jobs slice.
- Older local-Go-app wording should be treated as historical unless a task explicitly scopes back to that earlier path.

## Hourly QA review — 2026-04-23 15:20 America/Chicago

### Results
- PASS: Jimmy's latest 2:02 PM plan keeps the live QA gate pair unchanged, with T-106 as the server-backed persisted-jobs workspace handoff and T-095 as the provenance-first source-email persistence gate.
- PASS: Jimmy's latest lane guidance explicitly treats the root-level `~/Documents/project-requirements/DEVELOPMENT_PLAN.md` reference as stale external drift, and the current repo recovery order remains `projects/jobtrackr/DEVELOPMENT_PLAN.md`, `projects/jobtrackr/PROJECT.md`, then `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`.
- PASS: `projects/jobtrackr/DEVELOPMENT_PLAN.md` now records T-130 as complete, which keeps product-facing recovery notes aligned with the same live QA gate pair and sign-off entrypoints already used by the blocker checklist and this active QA coverage file.
- PASS: The live repo state still narrows QA execution to two concrete sign-off paths only: `projects/jobtrackr/specs/jobtrackr-persisted-jobs-workspace-signoff-checklist-2026-04-23.md` for T-106 and `projects/jobtrackr/specs/jobtrackr-source-email-persistence-signoff-checklist-2026-04-22.md` for T-095.
- GAP: T-106 still needs executed QA evidence for API-up `/dashboard` and `/jobs`, safe API-down mock fallback, and visible pending-fit or workflow-normalization notices before it can move from QA to Completed.
- GAP: T-095 still needs fixture-backed execution against `source_emails`, `job_source_emails`, and repeat-sync idempotence before the provenance-first sign-off gate can close.
- GAP: The external hourly kickoff prompt still references the dead root-level path `~/Documents/project-requirements/DEVELOPMENT_PLAN.md`, so automation-facing prompt drift remains unresolved outside the repo.

## Hourly QA review — 2026-04-23 12:50 America/Chicago

### Results
- PASS: Jimmy's latest 12:02 PM plan still keeps the repo-side QA surface narrowed to T-106 for the server-backed persisted-jobs workspace and T-095 for source-email persistence, with stale root-path wording still framed as external coordination drift instead of repo ambiguity.
- PASS: `projects/jobtrackr/prd/jobtrackr-prd-v2.md` now exposes the same live QA gate pair and sign-off entrypoints already used in the blocker checklist, so product-facing recovery notes no longer require QA to infer T-116 and T-102 from other files.
- PASS: `projects/jobtrackr/DEVELOPMENT_PLAN.md` now records T-130 as the PRD follow-through for that QA-gate alignment, which closes the remaining repo-side gap between the shared QA recovery note and the explicit sign-off entrypoint block.
- PASS: The shared QA recovery note remains byte-aligned across PRD v2, the blocker checklist, and active QA coverage, and all three still describe the active implementation as the Next.js web plus Go API auth -> session -> Gmail readonly connect -> persisted jobs slice.
- GAP: T-106 still needs executed QA evidence for API-up `/dashboard` and `/jobs`, safe API-down mock fallback, and visible pending-fit or workflow-normalization notices before it can move from QA to Completed.
- GAP: T-095 still needs fixture-backed execution against `source_emails`, `job_source_emails`, and repeat-sync idempotence before the provenance-first sign-off gate can close.
- GAP: The external hourly kickoff prompt still references the dead root-level path `~/Documents/project-requirements/DEVELOPMENT_PLAN.md`, so automation-facing prompt drift remains unresolved outside the repo.

## Hourly QA review — 2026-04-23 12:20 America/Chicago

### Results
- PASS: Jimmy's latest 12:02 PM plan still keeps the repo-side QA surface narrow and explicit, with T-106 as the live frontend handoff, T-095 as the remaining provenance sign-off gate, and stale external kickoff drift still framed as an external coordination problem instead of repo ambiguity.
- PASS: `projects/jobtrackr/DEVELOPMENT_PLAN.md` now shows T-073 as completed, and its completion note matches Jimmy's latest requirement that preserved handoff docs recover to `projects/jobtrackr/DEVELOPMENT_PLAN.md`, `projects/jobtrackr/PROJECT.md`, and the live Phase 3 handoff package in that same order.
- PASS: The live plan still shows T-070 in progress, and the active QA recovery note in this file plus the QA blocker checklist still matches Jimmy's latest wording goal: explicit project-root entrypoints plus the current Next.js web plus Go API implementation slice.
- PASS: The live plan still shows T-108 in QA, and the active test coverage already includes endpoint-level checks for `GET /api/v1/source-emails` with `limit`, `matchedAsJob`, `fromEmail`, and `search` filters, which stays aligned with the current backend/shared-contract handoff lane.
- GAP: T-106 still needs executed QA evidence for API-up `/dashboard` and `/jobs`, safe API-down mock fallback, and visible pending-fit or workflow-normalization notices before it can move from QA to Completed.
- GAP: T-095 still needs fixture-backed execution against `source_emails`, `job_source_emails`, and repeat-sync idempotence before the provenance-first sign-off gate can close.
- GAP: The external hourly kickoff prompt still references the dead root-level path `~/Documents/project-requirements/DEVELOPMENT_PLAN.md`, so automation-facing prompt drift remains unresolved outside the repo.

## Hourly QA review — 2026-04-23 09:50 America/Chicago

### Results
- PASS: Jimmy's latest 08:02 handoff still narrows live QA to the same two real gates, T-106 for the server-backed persisted-jobs workspace and T-095 for source-email persistence, with no new repo-side engineering blocker opened.
- PASS: The T-106 sign-off checklist no longer leaves the implementation proof ambiguous. QA can now anchor frontend evidence to commit `858127d` on `feat/jobtrackr-persisted-jobs-workspace`, which is the commit that wired `/dashboard` and `/jobs` to the persisted-jobs data layer and added API-mapping coverage.
- PASS: `projects/jobtrackr/DEVELOPMENT_PLAN.md` now records T-125 as completed, keeping the live frontend QA handoff centered on one deterministic checklist plus one concrete frontend evidence commit instead of branch-history reconstruction.
- GAP: T-106 still needs executed QA evidence for API-up `/dashboard` and `/jobs`, safe API-down mock fallback, and visible pending-fit or workflow-normalization notices before it can move from QA to Completed.
- GAP: T-095 still needs fixture-backed execution against `source_emails`, `job_source_emails`, and repeat-sync idempotence before the provenance-first sign-off gate can close.
- GAP: The external hourly kickoff prompt still references the dead root-level path `~/Documents/project-requirements/DEVELOPMENT_PLAN.md`, so automation-facing prompt drift remains unresolved outside the repo.

## Hourly QA review — 2026-04-23 09:20 America/Chicago

### Results
- PASS: Jimmy's latest 08:02 handoff still narrows live QA to the same two real gates, T-106 for the server-backed persisted-jobs workspace and T-095 for source-email persistence, with no new repo-side engineering blocker opened.
- PASS: Jimmy's latest pickup note makes the stale-path recovery rule explicit again, confirming QA should ignore `~/Documents/project-requirements/DEVELOPMENT_PLAN.md` and recover through `projects/jobtrackr/DEVELOPMENT_PLAN.md`, `projects/jobtrackr/PROJECT.md`, and `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`.
- PASS: `projects/jobtrackr/DEVELOPMENT_PLAN.md` now records T-117, T-116, and T-115 as completed, which keeps the live frontend QA entrypoint centered on the dedicated T-106 sign-off checklist and the refreshed persisted-jobs-first dashboard copy instead of older mock-only shell wording.
- PASS: The latest plan still points frontend continuity back to the canonical detail-view, list-to-detail, and workspace continuity contracts after the live T-106 and T-107 handoff checkpoints, so QA coverage can stay anchored to the current workspace behavior contract instead of preserved doc drift.
- GAP: T-106 still needs executed QA evidence for API-up `/dashboard` and `/jobs`, safe API-down mock fallback, and visible pending-fit or workflow-normalization notices before it can move from QA to Completed.
- GAP: T-095 still needs fixture-backed execution against `source_emails`, `job_source_emails`, and repeat-sync idempotence before the provenance-first sign-off gate can close.
- GAP: The external hourly kickoff prompt still references the dead root-level path `~/Documents/project-requirements/DEVELOPMENT_PLAN.md`, so automation-facing prompt drift remains unresolved outside the repo.

## Hourly QA review — 2026-04-23 06:50 America/Chicago

### Results
- PASS: Jimmy's latest 06:03 plan still keeps QA narrowed to the same two real gates, T-106 for the live frontend handoff and T-095 for source-email persistence sign-off, with no new repo-side blocker introduced.
- PASS: Jimmy's recovery order now clearly points QA back to `projects/jobtrackr/DEVELOPMENT_PLAN.md`, `projects/jobtrackr/PROJECT.md`, and `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`, and the shared QA recovery note in the PRD, blocker checklist, and active QA coverage has been updated to match that live order.
- PASS: The QA blocker checklist now exposes one shared live QA sign-off entrypoints block that calls out the two active checklists directly: T-095 for source-email persistence and T-106 for the persisted-jobs workspace handoff.
- PASS: T-116 remains the correct implementation-facing QA entrypoint for T-106 frontend verification, and T-102 plus the T-095 evidence bundle remain the correct implementation-facing proof set for provenance-first persistence.
- PASS: The live plan still shows T-106 in `QA` and T-095 as the remaining source-email persistence sign-off gate, which matches Jimmy's latest plan note that these are the only open QA surfaces worth active validation.
- GAP: The external hourly kickoff prompt still references the dead root-level path `~/Documents/project-requirements/DEVELOPMENT_PLAN.md`, so automation-facing prompt drift remains unresolved outside the repo.
- GAP: T-106 still needs executed QA evidence for persisted `/dashboard` and `/jobs`, safe API-down mock fallback, and visible pending-fit or workflow-normalization notices before it can move from QA to Completed.
- GAP: T-095 still needs fixture-backed execution against `source_emails`, `job_source_emails`, and repeat-sync idempotence before the provenance-first sign-off gate can close.

## Hourly QA review — 2026-04-23 06:20 America/Chicago

### Results
- PASS: Jimmy's 06:02 plan still keeps QA narrowed to the same two real gates, T-106 for the live frontend handoff and T-095 for source-email persistence sign-off, with no new repo-side blocker introduced.
- PASS: Jimmy's latest recovery order matches the live repo state: `projects/jobtrackr/DEVELOPMENT_PLAN.md`, `projects/jobtrackr/PROJECT.md`, and `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md` are still the correct ordered entrypoints.
- PASS: `projects/jobtrackr/DEVELOPMENT_PLAN.md` now records T-117 as completed, explicitly clarifying that hourly recovery should keep using the project-root markdown handoff set even while GitHub remains the longer-term tracker.
- PASS: `projects/jobtrackr/DEVELOPMENT_PLAN.md` now records T-118 as completed, and the live kickoff-facing docs now route Frank's backend/shared-contract recovery through the current T-091, T-095, and T-108 provenance handoff set.
- PASS: T-116 remains the correct implementation-facing QA entrypoint for T-106 frontend verification, and the checklist still cleanly scopes API-up persisted-jobs behavior, API-down fallback, and canonical fallback-notice checks.
- PASS: The live plan still shows T-106 in `QA` and T-095 as the remaining source-email persistence sign-off gate, which matches Jimmy's latest plan note that these are the only open QA surfaces worth active validation.
- GAP: The external hourly kickoff prompt still references the dead root-level path `~/Documents/project-requirements/DEVELOPMENT_PLAN.md`, so automation-facing prompt drift remains unresolved outside the repo.
- GAP: T-106 still needs executed QA evidence for persisted `/dashboard` and `/jobs`, safe API-down mock fallback, and visible pending-fit or workflow-normalization notices before it can move from QA to Completed.
- GAP: T-095 still needs fixture-backed execution against `source_emails`, `job_source_emails`, and repeat-sync idempotence before the provenance-first sign-off gate can close.

## Hourly QA review — 2026-04-23 03:20 America/Chicago

### Results
- PASS: Jimmy's latest plan still keeps QA tightly scoped to the two real open gates, T-095 fixture-backed source-email persistence sign-off and T-106 frontend handoff verification, which matches the current repo state.
- PASS: `projects/jobtrackr/DEVELOPMENT_PLAN.md` still shows T-106 in `QA`, while recent supporting handoff cleanup work is now recorded as completed, including T-107, T-109, T-110, T-114, and the new T-116 T-106 sign-off checklist publish step.
- PASS: The live recovery guidance remains explicit that `projects/jobtrackr/DEVELOPMENT_PLAN.md`, `projects/jobtrackr/PROJECT.md`, and `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md` are the correct ordered entrypoints.
- PASS: The T-095 sign-off checklist and evidence bundle are still the right implementation-facing proof set for provenance-first persistence, linkage preservation, and repeat-sync idempotence validation.
- PASS: T-106 remains the correct frontend QA target for server-backed dashboard and jobs-workspace validation, specifically persisted-job rendering, safe mock fallback behavior, pending-fit messaging, and workflow-normalization visibility.
- PASS: T-106 now has a dedicated implementation-facing sign-off path at `projects/jobtrackr/specs/jobtrackr-persisted-jobs-workspace-signoff-checklist-2026-04-23.md`, so Sam no longer needs to reconstruct the frontend QA proof set from the plan, README, and hourly coverage notes.
- PASS: Jimmy's latest note that T-113 already pruned stale carry-forward history matches the live plan, so the remaining QA narrative is now cleanly focused on open verification instead of stale kickoff drift inside repo docs.
- GAP: The external hourly kickoff prompt still references the dead root-level path `~/Documents/project-requirements/DEVELOPMENT_PLAN.md`, so automation-facing wording remains stale outside the repo even though the repo recovery notes are aligned.
- GAP: T-095 still needs fixture-backed execution against `source_emails`, `job_source_emails`, and repeat-sync behavior before QA can close the provenance-first persistence gate.
- GAP: T-106 still needs executed frontend QA evidence covering API-up behavior, API-down mock fallback behavior, and visible fallback notices for canonicalized workflow or pending-fit defaults before it can move out of QA.

## Hourly QA review — 2026-04-22 21:50 America/Chicago

### Results
- PASS: Jimmy's latest plan still centers the live auth -> session -> Gmail readonly connect -> persisted jobs slice, and it explicitly points the team back to `projects/jobtrackr/DEVELOPMENT_PLAN.md` and `projects/jobtrackr/PROJECT.md` instead of the dead root-level path.
- PASS: Jimmy's latest lane split keeps Alice and Marcus on frontend continuity and vertical-slice delivery, keeps Frank on T-091 source-email persistence follow-through, and keeps Priya on QA recovery alignment with T-095 sign-off support.
- PASS: `projects/jobtrackr/DEVELOPMENT_PLAN.md` now shows T-106 in QA as the new frontend handoff target for persisted-jobs workspace validation, and it also records T-107 as the latest Priya-owned QA coverage refresh.
- PASS: The live plan language still frames the current implementation context as Next.js web plus Go API, and it keeps the stale `~/Documents/project-requirements/DEVELOPMENT_PLAN.md` prompt wording categorized as external coordination drift rather than repo truth.
- PASS: Existing T-095 coverage in this file still matches the highest-risk open QA target this hour, specifically provenance-first persistence, duplicate-linkage idempotence, reprocess safety, and operator-visible GOG discovery behavior.
- PASS: The new T-106 handoff narrows the frontend QA surface cleanly to server-backed dashboard and jobs-workspace behavior, mock fallback visibility, and persisted-job normalization messaging without reopening the older recovery-note cleanup lane.
- GAP: The hourly QA cron prompt still references the dead root-level path `~/Documents/project-requirements/DEVELOPMENT_PLAN.md`, so the external kickoff wording remains stale even though repo docs are aligned.
- GAP: T-095 still needs fixture-backed verification of `source_emails`, `job_source_emails`, and repeat-sync idempotence before QA can move it from waiting-on-sign-off to completed.
- GAP: T-106 is implementation-complete but still needs QA execution coverage for persisted-job API availability, mock fallback visibility, and canonicalized workflow or pending-fit notices in the web UI before it can move beyond QA.

## Source tasks covered
- T-001: Finalize PM decision alignment in JobTrackr API contract and debug semantics
- T-002: Build local Go web app for Gmail inbox job search, protected shell, and raw source-email ingestion
- T-003: Produce JobTrakr dependency map and milestone sequencing pass for parallel-safe delivery
- T-004: Lock jobs dashboard contract for match rating, flagged semantics, and table payload shape
- T-005: Scaffold protected jobs dashboard web shell with mock table data for the first vertical slice
- T-006: Implement canonical low-fit handling for jobs with match score below 60 without workflow mutation
- T-007: Implement view all jobs page with full job details and filtering
- T-008: Implement job details page to view full job information
- T-009: Polish jobs table interaction states and shared dashboard UX for the mock-data frontend slice
- T-010: Break the approved dependency sequencing into implementation-ready engineering tickets for Milestones 1 to 4
- T-011: Improve the responsive jobs list UX with mobile cards, clearer active filter context, and compact results navigation
- T-012: Add editable notes and tag management interactions to the job details page using the agreed detail-view contract
- T-013: Add visible active-filter context and empty-result recovery controls to the mock jobs workspace
- T-014: Break the finalized parser and ingestion contract changes into implementation-ready tickets after the jobs dashboard payload is locked
- T-015: Define the job detail view contract for field grouping, edit interactions, and API expectations across T-008 and T-012
- T-016: Define the mock jobs workspace UX contract for active-filter visibility, empty-result recovery, and dashboard-to-jobs navigation
- T-017: Close Gate A documentation by reconciling schema, workflow/archive semantics, fit nullability, and source-email linkage across canonical specs
- T-018: Implement the Google auth callback, session flow, and Gmail readonly connection path for the first end-to-end vertical slice
- T-019: Align the job details UI with the locked detail-view contract, including section order, editing affordances, and list/detail behavior examples
- T-020: Lock row-selection continuity rules for the mock jobs workspace across filter changes, sorting, and dashboard return flows
- T-021: Assemble the final Phase 3 engineering handoff package with source-of-truth links, dependency gates, owner mapping, and immediate pickup guidance
- T-022: Wire the authenticated frontend shell to the new auth session and Gmail connection endpoints for the first vertical slice
- T-023: Add canonical list-to-detail contract examples so the jobs workspace and detail UI stay aligned with the locked section order and edit model
- T-024: Add canonical workspace session-state examples for deterministic row-selection continuity across filters, sorting, and dashboard return flows
- T-025: Publish a canonical Gate A reconciliation matrix so engineering can resolve workflow/archive, fit nullability, and source-email linkage drift from one file
- T-026: Add persisted Gmail search results and first-pass server-side filtering endpoints for the inbox job search slice
- T-027: Canonicalize the list-to-detail example docs so API, frontend, and QA reference one authoritative detail continuity example set
- T-028: Add a canonical workspace continuity QA matrix for filter, sort, dashboard return, and responsive selection behavior
- T-029: Deliver the first authenticated persisted-jobs vertical slice by wiring session-backed Gmail search results into the jobs UI
- T-030: Harden the job details editing UI with contract-aligned save/cancel notes flow, in-context tag edits, and shared section structure across drawer and page views
- T-031: Implement deterministic row-selection continuity in the mock jobs workspace across filter changes, pagination, and dashboard return flows
- T-032: Rewrite stale Gate A docs so legacy workflow statuses and auto-close behavior no longer conflict with the canonical PM memo, API contract, and reconciliation matrix
- T-033: Reconcile DEVELOPMENT_PLAN ticket language with the canonical workflow, fit, and table-contract decisions so engineers stop inheriting stale implementation assumptions
- T-034: Audit and reconcile milestone and ticket wording in DEVELOPMENT_PLAN so canonical workflow, fit, and table semantics stay consistent after the ticket rewrite pass
- T-045: Reconcile PROJECT, milestone, and handoff references so the plan rewrite stays aligned outside the task table
- T-046: Reconcile PRD and QA test-case references so legacy workflow and auto-close language no longer conflicts with the canonical workflow and fit model
- T-047: Reconcile remaining product/spec references so legacy flagged and stale workflow language no longer conflicts with the canonical workflow and fit-signal model
- T-048: Reconcile parser and ingestion ticket wording so milestone follow-through docs stop implying deprecated auto-close or workflow-state behavior for low-fit jobs
- T-049: Remove legacy workflow alias handling from the persisted jobs UI mapping and surface canonical fit-signal defaults for server-backed job results
- T-050: Reconcile PRD v2 dashboard, status, and fit-language sections plus in-progress QA coverage so archived stays out of workflow state and low-fit remains a fit signal
- T-051: Reconcile remaining fit-signal wording in API, frontend, and clarification docs so `flagged` and `unflagged` stop reading like canonical product states
- T-052: Harden the persisted jobs frontend mapping so server-backed results stay canonical when fit analysis is missing or legacy workflow aliases leak through old data
- T-053: Reconcile remaining legacy workflow and fit-signal wording in older stories and superseded PRD/spec drafts so stale docs stop leaking non-canonical states back into implementation
- T-054: Reconcile entrypoint and clarification docs so historical context keeps pointing engineers back to the canonical workflow and fit-signal vocabulary
- T-055: Surface canonical persisted-jobs fallback reasons in the frontend so missing fit data and normalized workflow values are visible per result set without implying stale workflow aliases
- T-056: Reconcile kickoff entrypoint docs so README and first-build-slice guidance point engineers at the canonical auth/dashboard vertical slice and current project phase
- T-057: Reconcile PRD v2 and QA blocker checklist source-of-truth references so archive and fit-signal guidance stay canonical in implementation-facing QA docs
- T-058: Reconcile roadmap fit-language and release exit criteria so release sequencing keeps using canonical fit signals instead of legacy flagged wording
- T-059: Add a protected local session shell and explicit Gmail disconnect flow for the Gmail job search vertical slice
- T-060: Reconcile lingering legacy fit-flag shorthand in preserved story drafts and clarification docs so historical references stop leaking stale product vocabulary
- T-061: Reconcile README, PROJECT, and kickoff handoff entrypoints so engineers land on the current auth to Gmail to persisted-jobs slice with correct project-root paths
- T-062: Reconcile QA-owned source-path and environment notes so implementation-facing test guidance points at the live project root and current web-plus-API architecture
- T-063: Reconcile roadmap and legacy review-doc source references so preserved planning docs point back to the current auth -> Gmail -> persisted-jobs source-of-truth set
- T-064: Reconcile automation-facing kickoff references so hourly Alice and QA prompts stop pointing at the dead root-level development-plan path and instead call out the live project-root source-of-truth set
- T-065: Reconcile historical kickoff slice guidance so preserved first-build docs explicitly recover to the live project-root source files and current auth -> session -> Gmail readonly connect -> persisted jobs lane
- T-066: Reconcile remaining QA recovery references in PRD v2 and in-progress test coverage so QA can recover from stale automation prompts without guessing the live project-root entrypoints or current web-plus-API slice
- T-067: Reconcile roadmap and legacy review-doc kickoff notes so planning artifacts explicitly recover to the live project-root source files and current Next.js web plus Go API delivery slice
- T-068: Normalize kickoff entrypoint path examples so README, PROJECT, and the Phase 3 handoff package all point to the same explicit `projects/jobtrackr/...` source-of-truth files during hourly recovery
- T-069: Normalize automation-facing recovery examples so hourly kickoff prompts can recover from the stale root-level development-plan path without ambiguity about the current project-root source set or live web-plus-API slice
- T-070: Align QA recovery wording across PRD v2, the QA blocker checklist, and in-progress QA coverage so all three use the same explicit project-root entrypoints and current Next.js web plus Go API framing
- T-071: Reconcile preserved engineering handoff guidance so legacy kickoff docs recover to the live project-root source files and current Next.js web plus Go API delivery slice
- T-072: Normalize kickoff-facing path examples and lane wording so README, PROJECT, and the handoff package use the same explicit recovery order and active implementation language
- T-073: Normalize preserved engineering handoff recovery wording so older handoff docs use the same explicit project-root entrypoints, legacy-context labeling, and current Next.js web plus Go API pickup language
- T-074: Normalize preserved implementation-facing recovery notes so older handoff and QA review docs use the same explicit absolute-path recovery order and current Next.js web plus Go API lane wording
- T-075: Normalize the shared QA recovery note across PRD v2, the blocker checklist, and in-progress QA coverage so the path-recovery order and current implementation lane wording are byte-for-byte aligned
- T-076: Normalize preserved planning and handoff recovery notes so ROADMAP, the PM memo, QA testability review, and legacy handoff docs share the same explicit recovery order and active implementation lane wording

---

## T-001 Contract Alignment Test Cases

### TC-001 Status model excludes archived
**Precondition:** API contract and implementation-facing specs updated to PM memo semantics.

**Steps**
1. Review documented status enum in API contract and related specs.
2. Attempt to identify any endpoint, example, or enum that still includes `archived` as a workflow status.
3. Review documented list/view semantics for inbox, all jobs, saved, and archived.

**Expected**
- Allowed workflow statuses are only `new`, `interested`, `applied`, `interviewing`, `offer`, `rejected`.
- `archived` is absent from workflow status enums.
- Archive behavior is defined only via `archivedAt`.
- Saved behavior is defined only via `saved: boolean`.

### TC-002 Search and filter semantics are canonical and testable
**Steps**
1. Review list endpoint request semantics and examples.
2. Confirm keyword search scope.
3. Confirm AND-across-filter families and OR-within-repeated-values behavior.
4. Confirm default sort and date boundary semantics.

**Expected**
- Search only covers `title`, `company`, and `descriptionSnippet`.
- Filters combine with AND logic.
- Repeated params inside a filter family combine with OR logic.
- `dateFrom` and `dateTo` are inclusive.
- Default sort is `dateReceived:desc`.

### TC-003 Gmail connection state contract matches PM decision
**Steps**
1. Review Gmail connection/account response shape.
2. Confirm supported state values and reconnect semantics.
3. Confirm readonly Gmail scope in specs.

**Expected**
- Response exposes `connected`, `needsReconnect`, `state`, `lastSyncedAt`, `lastErrorCode`, `lastErrorMessage`.
- Allowed `state` values are `connected`, `disconnected`, `expired`, `revoked`, `denied`.
- `needsReconnect` is true for `expired` and `revoked`.
- OAuth scope remains `gmail.readonly` only for MVP.

### TC-004 One source email can link to many jobs
**Steps**
1. Review data-model and debug-linkage documentation.
2. Inspect whether linkage is modeled as one-to-many or many-to-many.

**Expected**
- Specs explicitly allow one source email to link to multiple jobs.
- Linkage model supports durable source-to-job traceability.

---

## T-002 Web App and Ingestion Kickoff Slice Test Cases

### TC-101 Login page is reachable
**Steps**
1. Open `/login` as an unauthenticated user.

**Expected**
- Login page renders successfully.
- Gmail/Google sign-in affordance is visible.

### TC-102 Unauthenticated user is blocked from dashboard
**Steps**
1. Open `/dashboard` without an authenticated session.

**Expected**
- User is redirected or denied access consistently.
- Protected content is not visible before auth completes.

### TC-103 Successful Google sign-in establishes session
**Steps**
1. Start Google sign-in from `/login`.
2. Complete consent with the required scopes.
3. Return to the app.

**Expected**
- Session is created.
- User lands in authenticated experience.
- User record is created or loaded successfully.

### TC-104 Dashboard shell renders with required table columns
**Precondition:** Authenticated session exists.

**Steps**
1. Open `/dashboard`.
2. Inspect visible jobs table columns.

**Expected**
- Table includes `Fit`, `Company`, `Title`, `Location`, `Source`, `Date Found`, and `Status`.
- Table renders mock data if live data is not yet wired.

### TC-105 Fit column supports numeric score plus fit-signal state
**Steps**
1. Review dashboard fixtures or rendered rows.
2. Compare rows with strong-fit, low-fit, and pending fit states.

**Expected**
- Fit cell can display match rating from 0 to 100.
- Fit-state treatment is visually distinct without implying workflow status.
- Pending or unavailable fit state does not show a misleading numeric value.

### TC-106 Empty state is safe and actionable
**Steps**
1. Render dashboard with zero jobs.
2. Render dashboard with filters that return no rows.

**Expected**
- No-jobs state explains how jobs will appear.
- No-results state distinguishes filter mismatch from missing data.
- Clear recovery action is present.

### TC-107 Logout clears session
**Steps**
1. Start from authenticated dashboard.
2. Trigger logout.
3. Attempt to revisit `/dashboard`.

**Expected**
- Session is cleared.
- User cannot access protected dashboard after logout.

### TC-108 Gmail initial sync defaults are honored
**Steps**
1. Trigger initial sync for a newly connected account.
2. Inspect sync request/response and resulting source-email coverage.

**Expected**
- Initial sync targets the last 30 days by default.
- Response is accepted/running rather than blocking to completion.
- Clearly irrelevant mail is not persisted by default.

### TC-109 Below-threshold emails persist as source-email-only records
**Steps**
1. Process a relevant but weakly structured job email that fails parsed-job thresholds.
2. Inspect persisted outcomes.

**Expected**
- Source email is retained.
- No junk job row is created.
- Extraction outcome is available for debugging.

---

## T-019 Detail UI Alignment Test Cases

### TC-1901 Detail uses locked section order in both drawer and route patterns
**Steps**
1. Review the detail-view contract and list-to-detail examples doc.
2. Compare drawer and full-page detail states.

**Expected**
- Both patterns use the same six-section order.
- The UI does not invent a separate edit screen for notes or tags.

### TC-1902 Status edit reflects back into the selected list row
**Steps**
1. Start from a selected job in the jobs workspace.
2. Change the job status in the detail surface.
3. Re-check the selected list row state.

**Expected**
- Status updates in the detail UI.
- The same selected job remains active.
- The list row reflects the updated status without losing workspace context.

### TC-1903 Hidden selected row clears and restores deterministically
**Steps**
1. Select a job from the list.
2. Apply filters that hide that job.
3. Relax filters so the same job becomes visible again.

**Expected**
- Active selection clears as soon as the selected row leaves the visible set.
- Helper copy explains why the detail state disappeared.
- The most recently selected job restores automatically when it becomes visible again in the same session.

## T-020 Row-Selection Continuity Test Cases

### TC-1910 Sorting preserves selection by job id
**Steps**
1. Select a job row in the workspace.
2. Change sort order one or more times.
3. Inspect the selected state after each sort.

**Expected**
- Selection persists by job id, not row index.
- The selected row stays visibly selected after sort changes.
- Returning from detail lands near the selected row.

### TC-1911 Dashboard return preserves workspace snapshot without stale selection
**Steps**
1. Enter the jobs workspace from dashboard.
2. Apply filters and select a job.
3. Return to dashboard, then re-enter jobs in the same session.

**Expected**
- Active filters and sort are preserved.
- Selection is restored only if the selected job still matches the active result set.
- If the job no longer matches, selection stays cleared and helper copy explains why.

## T-024 Workspace Session-State Example Test Cases

### TC-1920 Hidden selection keeps lastSelectedJobId for same-session restoration
**Steps**
1. Select a job in the workspace.
2. Apply filters that hide it.
3. Inspect the stored workspace session state.

**Expected**
- `selectedJobId` clears immediately.
- `lastSelectedJobId` still points to the hidden job.
- Helper mode is set to a hidden-selection state instead of picking a replacement row.

### TC-1921 Dashboard re-entry restores snapshot before selection logic runs
**Steps**
1. Enter the jobs workspace from dashboard.
2. Apply filters, pick a job, and return to dashboard.
3. Re-enter the jobs workspace in the same session.

**Expected**
- Filters and sort restore first.
- Selection restores only if the last selected job is visible in the restored result set.
- The workspace does not open stale detail for a hidden job.

### TC-1922 Mobile detail return preserves list context by job id
**Steps**
1. Open a job from the mobile jobs list.
2. Return from full-page detail to the list.
3. Compare selection and scroll behavior before and after the return.

**Expected**
- The list restores the same filters and sort.
- The user lands near the previously selected job when it still matches.
- If that job no longer matches, selection clears and helper copy appears.

## T-003 Dependency and Sequencing QA Checks

### TC-201 First slice is independently testable
**Steps**
1. Review milestone sequencing and kickoff slice definition.
2. Verify first slice does not depend on fit analysis or resume upload.

**Expected**
- Auth and protected shell can be validated without downstream fit-analysis completion.
- Early dashboard behavior remains testable with mock or fixture data.

### TC-202 Parallel lane contracts are frozen before integration leaves mocks
**Steps**
1. Review dependency-map blocker list.
2. Check whether schema/state model, dashboard query contract, and Gmail connection state contract are explicitly called out as hardening prerequisites.

**Expected**
- Parallel implementation safety depends on canonical contracts first.
- QA can identify when UI integration should remain mocked versus when real-data validation can begin.

---

## T-004 Jobs Dashboard Contract Test Cases

### TC-301 Table payload supports fit tri-state
**Steps**
1. Review dashboard/table response contract or fixture model.
2. Validate representation for rated-strong-fit, rated-low-fit, and pending/unavailable fit cases.

**Expected**
- Contract supports numeric score plus canonical fit-state signaling together.
- Contract supports null or equivalent pending state when fit is unavailable.

### TC-302 Required table fields are present and sortable where promised
**Steps**
1. Review dashboard payload and UI spec.
2. Validate supported sorts.

**Expected**
- Required fields exist for fit, company, title, location, source, date found, and status.
- Sorting support is defined for date found, company, status, and match rating.
- Default sort is date found descending.

### TC-303 Row interaction is unambiguous
**Steps**
1. Review dashboard table interaction spec.
2. Confirm whether row click, title click, or both open detail.

**Expected**
- One interaction pattern is chosen and documented.
- QA can validate detail access consistently.

### TC-304 Accessibility basics are testable
**Steps**
1. Review component acceptance expectations.
2. Validate keyboard navigation and visible labels requirements are explicitly documented.

**Expected**
- Table keyboard navigation is required.
- Interactive controls have visible labels.
- Status and fit are not color-only signals.

---

## T-005 Dashboard Shell Test Cases

### TC-401 Authenticated user lands on protected jobs dashboard shell
**Precondition:** Valid authenticated session exists.

**Steps**
1. Open the dashboard route after login.
2. Verify the user is not redirected away.
3. Inspect the initial shell before real API data is wired.

**Expected**
- Authenticated user reaches the dashboard shell.
- Core layout is visible with page header, jobs area, and account/logout controls.
- Mock data is allowed, but the protected shell must render without console-visible auth errors.

### TC-402 Mock jobs table renders required visible columns
**Steps**
1. Open the mock-data dashboard state.
2. Inspect the table header and first page of rows.

**Expected**
- Table shows the required dashboard columns promised for the current slice.
- Mock rows populate consistently enough for layout and interaction testing.
- Column labeling is stable and human-readable.

### TC-403 Dashboard shell supports loading and empty states
**Steps**
1. Simulate a loading state.
2. Simulate zero jobs.
3. Simulate a filter result with no rows.

**Expected**
- Loading state is visually distinct from empty state.
- Empty state explains how jobs appear in the app.
- No-results state offers a clear filter-reset or recovery action.

### TC-404 Dashboard shell is responsive on mobile and tablet breakpoints
**Steps**
1. Render the dashboard at desktop, tablet, and mobile widths.
2. Verify table overflow, stacking, or scroll behavior.

**Expected**
- Layout remains usable on mobile and tablet.
- Critical controls are still reachable.
- Table content does not become unreadable or irrecoverably clipped.

---

## T-006 Auto-Close Logic Test Cases

### TC-501 Canonical low-fit handling does not invent a workflow transition
**Steps**
1. Create or simulate jobs in `new` and `interested` states.
2. Write fit results below 60.
3. Observe resulting job status, fit metadata, and audit trail.

**Expected**
- Workflow status remains unchanged.
- Low-fit treatment is represented through canonical fit metadata or helper signals, not a synthetic workflow status.
- Structured logging records the low-fit handling path.

### TC-502 Auto-close does not trigger for score 60 or above
**Steps**
1. Analyze jobs with scores of 60 and 61.
2. Inspect status, history, and logs.

**Expected**
- Status remains unchanged.
- No low-fit workflow mutation or synthetic status-history row is added.
- Log path records above-threshold behavior.

### TC-503 Null fit scores do not change workflow state
**Steps**
1. Run fit analysis with `fit_score = null`.
2. Inspect job status and logs.

**Expected**
- Job status remains unchanged.
- No auto-close history row is written.
- Skip reason is logged as null-score behavior.

### TC-504 Manual or user-advanced states are protected from low-fit overwrite
**Steps**
1. Set jobs to `applied`, `interviewing`, `rejected`, and `offer`.
2. Re-run analysis with a score below 60.

**Expected**
- Protected statuses remain unchanged.
- Logs capture protected-state handling behavior.
- No duplicate or destructive transition occurs.

### TC-505 QA blocker: low-fit implementation docs conflict with the canonical workflow model
**Steps**
1. Compare `jobtrackr-auto-close-logic-spec-v1.md` with the PM decision memo, reconciliation matrix, and current API contract.
2. Inspect whether `flagged`, `reviewing`, `skipped`, `interview`, and `not a match` are still presented as canonical workflow statuses or workflow outcomes.

**Expected**
- Any mismatch is treated as a release blocker for implementation sign-off.
- QA requires one canonical workflow status model plus one canonical low-fit handling model before implementation is approved.

---

## T-007 View All Jobs Page Test Cases

### TC-601 View-all page lists persisted jobs with full-page browsing controls
**Steps**
1. Open the all-jobs page with seeded job data.
2. Inspect the list layout, pagination controls, and default ordering.

**Expected**
- Page renders more than the limited dashboard preview state.
- Pagination or equivalent page navigation is present.
- Default sort is documented and behaves consistently.

### TC-602 Filtering works across status, fit, date, and source inputs
**Steps**
1. Apply one filter at a time.
2. Apply combined filters.
3. Clear filters.

**Expected**
- Single filters narrow results correctly.
- Combined filters respect AND-across-filter-family behavior.
- Clear/reset returns the default list view.

### TC-603 Search returns matching jobs across supported fields only
**Steps**
1. Search by title, company, and description snippet text.
2. Search by notes and tags to verify unsupported-field handling.

**Expected**
- Matches are returned for supported search fields only.
- Notes and tags are not silently included if still out of scope for MVP.
- Empty search results are handled cleanly.

### TC-604 Pagination preserves current filter and search state
**Steps**
1. Apply filters and search text.
2. Navigate across pages.
3. Return to a prior page.

**Expected**
- Active filter/search state is preserved during pagination.
- Back/forward navigation does not lose the browsing context unexpectedly.

---

## T-008 Job Details Page Test Cases

### TC-701 Job details page displays all required fields
**Steps**
1. Open a job details page from the list or dashboard.
2. Inspect visible fields and metadata.

**Expected**
- Page shows description, skills, tags, notes, and source link.
- Core metadata like title, company, location, status, and fit are visible.
- Missing optional data is handled without broken layout.

### TC-702 Source link opens safely and predictably
**Steps**
1. Click the source/job link from the details page.
2. Observe browser behavior.

**Expected**
- Link opens in a new tab or otherwise follows the locked product behavior.
- The details page remains intact in the original session.

### TC-703 Notes can be edited and persisted without clobbering unrelated fields
**Steps**
1. Update the notes field.
2. Save changes.
3. Refresh the page.

**Expected**
- Notes persist after refresh.
- Unrelated fields are unchanged.
- Validation or error feedback is shown if save fails.

### TC-704 Tags can be added and removed cleanly
**Steps**
1. Add a new tag.
2. Remove an existing tag.
3. Refresh the page.

**Expected**
- Added tags persist.
- Removed tags stay removed.
- Duplicate tags are prevented or normalized.

---

## T-009 Dashboard Table Polish Test Cases

### TC-801 Row selection and detail affordance are obvious
**Steps**
1. Hover, focus, and click on table rows and title links.
2. Verify selected-row treatment.

**Expected**
- Selected row state is visually obvious.
- Users can tell what is clickable.
- The interaction does not rely on color alone.

### TC-802 Dashboard and jobs views share consistent empty and loading shells
**Steps**
1. Compare dashboard and jobs-page loading states.
2. Compare dashboard and jobs-page empty states.

**Expected**
- Loading placeholders follow the same visual/system pattern.
- Empty states feel consistent across views.
- Copy differences are purposeful, not accidental drift.

### TC-803 Dashboard-to-jobs navigation is smooth and state-aware
**Steps**
1. Navigate from the dashboard summary view to the full jobs view.
2. Observe whether relevant context carries over.

**Expected**
- Navigation is obvious and not jarring.
- If filters or selected context are meant to carry over, they do so predictably.
- No dead-end navigation path exists.

### TC-804 Keyboard and focus behavior remain usable after polish changes
**Steps**
1. Navigate the table and controls with keyboard only.
2. Trigger row/detail actions without a mouse.

**Expected**
- Focus states are visible.
- Keyboard navigation still works after visual polish changes.
- Interactive table affordances remain accessible.

---

## T-010 Milestone Ticket Breakdown QA Checks

### TC-901 Milestones 1 to 4 ticket breakdown preserves contract prerequisites
**Steps**
1. Review the implementation-ready ticket breakdown for Milestones 1 to 4.
2. Trace dependencies back to T-001 and T-004.
3. Verify auth, dashboard, persisted jobs, and Gmail ingestion work are sequenced without bypassing contract-lock prerequisites.

**Expected**
- Contract-dependent work is explicitly blocked on T-001 and T-004 where required.
- Tickets are small enough for engineering handoff and QA verification.
- Dependency notes make parallel execution safe rather than implied.

### TC-902 Ticket breakdown includes QA-verifiable acceptance criteria and handoffs
**Steps**
1. Inspect milestone tickets for acceptance criteria quality.
2. Check whether each ticket exposes QA entry points, test data needs, and handoff notes.

**Expected**
- Each ticket has measurable acceptance criteria.
- QA can tell what must be demonstrated before sign-off.
- Hidden prerequisites and ambiguous “done” language are absent.

---

## T-011 Responsive Jobs List UX Test Cases

### TC-1001 Mobile jobs view preserves essential job context
**Steps**
1. Open the jobs view at tablet and mobile breakpoints.
2. Inspect each responsive row or card.
3. Compare the compact layout to the desktop job list.

**Expected**
- Each item still exposes title, company, status, and fit context.
- A user can open the job detail view without relying on hover-only controls.
- Compact navigation does not hide the currently selected or active job context.

### TC-1002 Active filter context remains visible and removable on smaller screens
**Steps**
1. Apply multiple filters on the jobs page.
2. Inspect the active filter summary at desktop, tablet, and mobile widths.
3. Remove one filter, then clear all filters.

**Expected**
- Active filters remain visible without reopening every filter control.
- Individual filters can be removed directly from the summary state.
- The summary and results stay in sync after each change.

### TC-1003 Compact results navigation preserves browsing context
**Steps**
1. Apply filters and search text on the jobs page.
2. Move between result items using the compact navigation affordance.
3. Open a job detail view, then return to the list.

**Expected**
- Users can move through filtered results without losing place.
- Returning from details preserves list context.
- Compact navigation does not drop active filters or search state.

---

## T-012 Job Details Editing Test Cases

### TC-1101 Notes editing is inline, recoverable, and persistence-ready
**Steps**
1. Open a job details page with existing notes.
2. Edit the notes inline.
3. Save the change, then refresh the page.
4. Repeat with an empty notes field and with a simulated save failure.

**Expected**
- Notes can be edited without leaving the page.
- Successful saves persist after refresh.
- Empty-state notes are supported without breaking the layout.
- Failed saves surface clear error feedback and do not silently discard prior saved content.

### TC-1102 Tag add and remove controls are clear and prevent duplicate state
**Steps**
1. Add a new tag from the details page.
2. Add an already-present tag.
3. Remove a tag, then refresh the page.

**Expected**
- Add and remove controls are obvious and usable.
- Duplicate tags are prevented or normalized.
- The visible tag set remains consistent after refresh.

### TC-1103 Detail editing interactions match the agreed job-detail contract
**Steps**
1. Compare notes and tag interactions to `jobtrackr-api-contract.md` and the detail payload shape.
2. Verify the UI does not invent unsupported fields or save paths.

**Expected**
- Editing behavior maps to the documented detail contract.
- Notes update paths and tag-management flows are implementation-ready for API wiring.
- No UI state depends on fields that are absent from the agreed contract.

---

## T-013 Filter Context and Empty-Result Recovery Test Cases

### TC-1201 Active filters stay visible after apply
**Steps**
1. Apply search, status, and location filters in the jobs workspace.
2. Inspect the results area at desktop, tablet, and mobile widths.

**Expected**
- An `Active filters` summary appears above results.
- Each applied filter is visible as its own removable chip or badge.
- Total visible-result count updates with the applied filter state.

### TC-1202 Empty-result state explains recovery paths
**Steps**
1. Apply filters that produce zero matching jobs.
2. Inspect the empty-result messaging and available actions.

**Expected**
- The table body is replaced by an explicit empty-result panel, not a blank region.
- Primary recovery action is `Clear all filters`.
- Secondary recovery action is `Back to dashboard`.
- If search text is active, the empty-state copy references it.

### TC-1203 Filter reset and dashboard return do not confuse workspace state
**Steps**
1. From a filtered empty state, click `Clear all filters`.
2. Reapply filters, then click `Back to dashboard`.
3. Re-enter the jobs workspace.

**Expected**
- Clear-all returns the user to the default jobs workspace state.
- Back-to-dashboard is available without relying on browser back.
- Re-entering the jobs workspace preserves filters when the contract says that session context should survive.

---

## T-014 Parser and Ingestion Ticket Breakdown QA Checks

### TC-1301 Milestone 4 parser follow-through tickets are blocked on contract lock
**Steps**
1. Review the parser and ingestion ticket breakdown work for T-014.
2. Trace each ticket dependency back to T-001 and T-004.

**Expected**
- Parser and ingestion follow-through work does not bypass unresolved contract work.
- Dependencies explicitly mention dashboard payload and ingestion-contract prerequisites.
- Engineering can tell what must be frozen before implementation starts.

### TC-1302 Parser follow-through tickets expose QA-verifiable exit checks
**Steps**
1. Inspect each T-014 ticket for acceptance language.
2. Check whether ingestion logging, source-email persistence, extraction thresholds, and dedupe outcomes are testable.

**Expected**
- Ticket acceptance criteria are measurable.
- QA can verify source-email retention, dedupe-by-URL, and created-job outcomes without guessing.
- Hidden parser assumptions are called out instead of implied.

---

## T-015 Detail View Contract Test Cases

### TC-1401 Detail section order is locked and reusable
**Steps**
1. Review `jobtrackr-detail-view-contract-2026-04-20.md`.
2. Compare the defined section order with T-008 and T-012 expectations.

**Expected**
- Section order is explicitly documented as header summary, fit and workflow, job content, notes, tags, then source and activity context.
- T-008 and T-012 can build against one shared order instead of divergent layouts.

### TC-1402 Editable versus view-only fields are unambiguous
**Steps**
1. Review field-level interaction rules in the detail contract.
2. Verify whether status, notes, and tags are the only editable MVP fields.

**Expected**
- Editable fields are limited to `status`, `notes`, and `tags`.
- Fit fields, recruiter metadata, and source/activity context remain view-only.
- Notes use explicit `Save` and `Cancel`, not autosave.

### TC-1403 Detail payload example supports real implementation handoff
**Steps**
1. Inspect the example `GET /jobs/:id` payload in the detail contract.
2. Compare it with existing T-008 and T-012 test assumptions.

**Expected**
- Payload includes the fields needed for header, fit, notes, tags, and source/activity rendering.
- API write paths for notes and tags are documented clearly enough for frontend and QA alignment.
- Any missing field that blocks implementation is treated as a contract gap.

---

## T-016 Workspace UX Contract Test Cases

### TC-1501 Filter summary contract is explicit across breakpoints
**Steps**
1. Review `jobtrackr-workspace-ux-contract-2026-04-20.md`.
2. Inspect placement and behavior rules for desktop, tablet, and mobile.

**Expected**
- The active-filter summary placement is specified for each breakpoint.
- Summary is hidden only when no filters are active.
- Removing a chip updates results and summary count immediately.

### TC-1502 Empty-result recovery actions are standardized
**Steps**
1. Review the filtered-empty and no-jobs-yet states.
2. Compare required actions and copy guidance.

**Expected**
- Filtered-empty state and no-jobs-yet state are distinct.
- `Clear all filters` and `Back to dashboard` are required recovery actions for filtered-empty states.
- Empty-result messaging is testable and not left to ad hoc frontend copy.

### TC-1503 Selection and navigation rules preserve user context
**Steps**
1. Review the row-selected and dashboard-to-jobs navigation sections.
2. Check whether selection persistence and return paths are defined clearly enough for QA.

**Expected**
- Selected-row treatment must survive non-destructive changes when the row remains visible.
- Dashboard-to-jobs and back navigation are explicit and testable.
- Mobile return behavior preserves filter context, and selected-row preservation is defined for in-session return flows.

### TC-1504 Selection clears with guidance when filters hide the active row
**Steps**
1. Start from a workspace state with one selected job row.
2. Apply a search or filter that removes that job from the visible results.
3. Inspect the resulting list or empty-result state.

**Expected**
- Active selection is cleared immediately.
- Active filters and sort remain intact.
- Inline helper copy explains that the previously selected job is hidden by current filters.

### TC-1505 Selection restores when the same row becomes visible again in-session
**Steps**
1. Select a job row in the workspace.
2. Apply a filter that hides it, then relax or clear that filter during the same session.
3. Return to the list and inspect the row state.

**Expected**
- The most recently selected visible job is restored by id, not by row index.
- The list returns near the restored row or card.
- Restoration behavior is defined for desktop, tablet, and mobile return flows.

---

## T-017 Gate A Closeout Test Cases

### TC-1601 Workflow, saved, and archive semantics are consistent across canonical specs
**Steps**
1. Compare `jobtrackr-pm-decision-memo-2026-04-19.md`, `jobtrackr-api-contract.md`, `jobtrackr-database-schema.md`, and the Milestones 1 to 4 engineering tickets.
2. Check whether workflow enum values, `saved`, and `archivedAt` semantics agree.
3. Check whether any spec still treats archive as a workflow state.

**Expected**
- Canonical workflow enum is `new`, `interested`, `applied`, `interviewing`, `offer`, `rejected` in all Gate A docs.
- `saved` remains a separate boolean flag.
- `archivedAt` remains the only archive-state field.
- No Gate A document reintroduces `archived` as a status value.

### TC-1602 Fit nullability stays explicit before analysis exists
**Steps**
1. Review the PM memo, API contract, schema spec, and milestone tickets.
2. Compare how pre-analysis jobs represent fit data.

**Expected**
- Gate A docs consistently allow `fitFlag = null`, `fitScore = null`, and `fitSummary = null` before analysis runs.
- No Gate A document requires synthetic default fit values.
- Frontend-facing docs distinguish pending or unavailable fit from low-score fit.

### TC-1603 One-source-email-to-many-jobs linkage is preserved end to end
**Steps**
1. Review linkage modeling in schema, API/debug docs, and milestone tickets.
2. Check whether any doc still implies a single `job_id` on the source-email record as the only linkage path.

**Expected**
- Canonical linkage model supports one source email to many jobs.
- Schema, debug endpoints, and milestone tickets all describe durable traceability for that relationship.
- If a legacy single-job pointer still appears, the doc either removes it or clearly marks it transitional and non-canonical.

## T-018 Google Auth and Gmail Connection Slice Test Cases

### TC-1701 Google auth start returns a usable OAuth URL
**Steps**
1. Call `POST /api/v1/auth/google/start` from an unauthenticated state.
2. Inspect the response payload.

**Expected**
- Response returns a non-empty `authUrl`.
- Requested scopes include `openid`, `email`, `profile`, and `gmail.readonly` only.
- Response shape matches the API contract.

### TC-1702 OAuth callback creates session and redirects into the app
**Steps**
1. Start Google auth.
2. Complete the callback with a valid code and state.
3. Inspect resulting redirect and session state.

**Expected**
- Callback validates state and completes without exposing raw tokens to the browser.
- Session cookie is created.
- User lands in the authenticated app experience.
- `GET /api/v1/auth/session` returns the signed-in user and Gmail connection indicator.

### TC-1703 Protected routes deny unauthenticated access after auth work lands
**Steps**
1. Request a protected route without a valid session.
2. Request the same route with a valid session.

**Expected**
- Unauthenticated access is redirected or rejected consistently.
- Authenticated access succeeds.
- Protected shell behavior matches the Milestone 1 exit checks.

### TC-1704 Gmail account endpoint exposes readonly connection state cleanly
**Steps**
1. Complete auth with Gmail readonly granted.
2. Call `GET /api/v1/gmail/account`.
3. Repeat with disconnected, expired, revoked, and denied fixtures or test states.

**Expected**
- Response exposes `connected`, `needsReconnect`, `state`, `lastSyncedAt`, `lastErrorCode`, and `lastErrorMessage`.
- Allowed states match the PM memo and API contract.
- `needsReconnect` is true where reconnect is required.
- No broader Gmail scope appears in the persisted account state.

### TC-1705 Logout fully clears session state without disconnecting imported Gmail history
**Steps**
1. Sign in and confirm session exists.
2. Call `POST /api/v1/auth/logout`.
3. Recheck protected routes and Gmail account behavior after signing back in.

**Expected**
- Logout clears the active session.
- Protected routes are no longer accessible until re-authentication.
- Historical Gmail connection or imported state is not silently deleted by logout alone.

## T-021 Handoff Package QA Checks

### TC-1801 Final handoff package points to one canonical source-of-truth set
**Steps**
1. Review the Phase 3 handoff package.
2. Check the linked plan, contract, QA, and gate documents.

**Expected**
- One handoff file links the active plan, detail/workspace contracts, milestone ticket docs, and latest QA coverage.
- Gate A and Gate B readiness are called out explicitly.
- A new engineer could find current source-of-truth docs without reconstructing state from multiple channels.

## T-022 Frontend Auth and Gmail Connection Wiring Test Cases

### TC-1810 Frontend reflects signed-out, signed-in, and reconnect states
**Steps**
1. Open the app signed out.
2. Start auth from the UI and complete sign-in.
3. Revisit the app with a disconnected or reconnect-required Gmail state.

**Expected**
- Signed-out state exposes a clear auth entry point.
- Signed-in state shows session-aware UI without manual API calls.
- Gmail connection state renders connected versus reconnect-needed states clearly.

### TC-1811 Logout and reconnect actions are available from the authenticated shell
**Steps**
1. Start from an authenticated shell with Gmail account state loaded.
2. Trigger logout.
3. Sign back in and trigger reconnect if the Gmail state requires it.

**Expected**
- Logout clears the shell back to signed-out behavior.
- Reconnect action is visible only when needed.
- Session and Gmail connection controls do not drift from the backend contract.

## T-023 Canonical List-to-Detail Example Test Cases

### TC-1820 List and detail examples use the same section order and selection model
**Steps**
1. Review the list-to-detail examples doc.
2. Compare it with the detail-view contract and workspace UX contract.

**Expected**
- Example flows preserve the same section order defined in the detail contract.
- Selection continuity matches the workspace UX contract.
- Notes, tags, and status interactions do not contradict the locked edit model.

### TC-1821 Example payloads are concrete enough for frontend, API, and QA alignment
**Steps**
1. Inspect list payload, selected-row state, and detail payload examples.
2. Check for missing fields or ambiguous transitions.

**Expected**
- Examples show how list state maps into detail state without inferred behavior.
- Required fields for notes, tags, status, fit, and source metadata are present.
- Any missing example that blocks implementation is treated as a contract gap.

## T-025 Gate A Reconciliation Matrix Test Cases

### TC-1930 Matrix preserves canonical precedence order
**Steps**
1. Review `jobtrackr-gate-a-reconciliation-matrix-2026-04-20.md`.
2. Compare its precedence list to the current handoff package and live development plan.

**Expected**
- The matrix points engineers first to `projects/jobtrackr/DEVELOPMENT_PLAN.md`.
- PM memo, API contract, schema, milestone tickets, and handoff package all appear in a stable precedence order.
- Engineering can resolve a doc conflict without falling back to stale story files.

### TC-1931 Each Gate A rule names stale references and required engineering behavior
**Steps**
1. Review each Gate A row in the matrix.
2. Check whether workflow, archive, fit-nullability, dashboard payload, and source-email linkage all list stale references plus an explicit engineering action.

**Expected**
- Every Gate A rule identifies at least one risky stale source where drift is still likely.
- Every Gate A rule tells engineering what to implement or avoid.
- The matrix is actionable enough to use as a review checklist, not just a summary.

### TC-1932 Immediate drift callouts align with current QA blockers
**Steps**
1. Compare the matrix drift callouts with the current in-progress QA blockers.
2. Check auto-close semantics, old story docs, and source-email linkage guidance.

**Expected**
- The matrix explicitly flags the stale auto-close status model.
- Older story docs are marked non-canonical for workflow-state implementation.
- Source-email linkage guidance matches the join-table expectation used elsewhere in Gate A docs.

## T-026 Persisted Gmail Search and Filtering Test Cases

### TC-1950 Persisted jobs endpoint returns stored results without rescanning Gmail
**Steps**
1. Trigger one Gmail sync that creates persisted job records.
2. Call the jobs list endpoint twice without starting another sync.
3. Compare response behavior and persistence expectations.

**Expected**
- The jobs endpoint reads from persisted data.
- Repeated list calls do not require a new Gmail scan.
- Response semantics stay deterministic between calls when no new sync runs.

### TC-1951 Server-side filters honor canonical list semantics
**Steps**
1. Seed jobs that vary by status, company, and text content.
2. Call the jobs list endpoint with individual and combined query params.
3. Compare returned rows to the API contract filter rules.

**Expected**
- Supported filters narrow server results correctly.
- Combined filters use AND-across-filter-family behavior.
- Search scope stays limited to canonical searchable fields.

## T-027 Canonical List-to-Detail Example Test Cases

### TC-1960 Only one authoritative list-detail example file remains implementation-facing
**Steps**
1. Review `jobtrackr-list-detail-contract-examples-2026-04-20.md` and any older list-detail example docs.
2. Inspect cross-links from the API contract, detail contract, workspace contract, and handoff package.

**Expected**
- `jobtrackr-list-detail-contract-examples-2026-04-20.md` is the canonical example set.
- Older example files are either compatibility shims or explicitly marked non-canonical.
- New implementation-facing docs point only to the canonical file.

## T-103 Preserved Frontend Detail Continuity Alignment Test Cases

### TC-2160 Preserved frontend specs defer to the canonical detail-view and list-to-detail contracts
**Steps**
1. Review `specs/jobtrackr-frontend-plan-v1.md` and `specs/jobtrackr-table-ui-spec-v1.md`.
2. Compare their detail-flow wording against `specs/jobtrackr-detail-view-contract-2026-04-20.md` and `specs/jobtrackr-list-detail-contract-examples-2026-04-20.md`.

**Expected**
- Both preserved frontend docs explicitly defer to the canonical detail-view and list-to-detail files when older wording drifts.
- The frontend plan no longer presents `strengths`, `gaps`, or `notes/tags later if available` as the active detail model.
- The table UI spec points table-to-detail continuity, drawer-versus-route parity, and selected-row behavior back to the canonical list-to-detail examples.

## T-105 Kickoff-Facing Frontend Continuity Pointer Test Cases

### TC-2161 Kickoff-facing docs route frontend pickup to the canonical detail continuity files
**Steps**
1. Review `README.md`, `PROJECT.md`, and `specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`.
2. Confirm each file explicitly points frontend pickup to `specs/jobtrackr-detail-view-contract-2026-04-20.md`, `specs/jobtrackr-list-detail-contract-examples-2026-04-20.md`, and `specs/jobtrackr-workspace-ux-contract-2026-04-20.md`.

**Expected**
- All three kickoff-facing docs mention the same canonical detail continuity files.
- The handoff package keeps Marcus's lane focused on frontend continuity alignment rather than preserved-doc-only wording.
- An engineer recovering from an hourly kickoff can reach the live detail continuity contracts without starting from preserved frontend-plan language.

## T-028 Workspace Continuity QA Matrix Test Cases

### TC-1940 Continuity matrix covers preserve, clear, restore, and no-fallback rules
**Steps**
1. Review `jobtrackr-workspace-continuity-qa-matrix-2026-04-20.md`.
2. Confirm it includes sort, filter-hide, restore, dashboard return, mobile return, and no-fallback scenarios.

**Expected**
- QA has one canonical matrix for deterministic selection continuity.
- Each scenario names expected `selectedJobId`, `lastSelectedJobId`, and helper-state outcomes.
- Breakpoint parity is explicit instead of implied.

### TC-1941 Workspace continuity references are wired from the UX and session-state docs
**Steps**
1. Review the workspace UX contract and workspace session-state examples.
2. Confirm both point to the continuity QA matrix.

**Expected**
- Engineers can reach the QA matrix from the two canonical workspace docs.
- Reviewers do not need to infer which continuity file is implementation-facing versus QA-facing.

## T-029 Authenticated Persisted-Jobs Vertical Slice Test Cases

### TC-1970 Authenticated user can reach persisted jobs without mock-only fallback
**Steps**
1. Sign in through the auth flow.
2. Confirm Gmail connection is available.
3. Open the jobs UI after persisted jobs exist.

**Expected**
- The jobs UI loads through the authenticated session.
- Results come from persisted backend data rather than mock-only fixtures.
- Loading and empty states reflect real API responses cleanly.

### TC-1971 Session loss blocks persisted job retrieval cleanly
**Steps**
1. Load persisted jobs in an authenticated session.
2. Expire or clear the session.
3. Retry the jobs request and revisit the jobs UI.

**Expected**
- Persisted job retrieval is denied after session loss.
- The UI returns to a signed-out or re-auth-required state.
- No stale protected job data remains visible.

## T-030 Job Details Editing UI Hardening Test Cases

### TC-1980 Notes require explicit save or cancel in both drawer and page views
**Steps**
1. Open a job detail in drawer form.
2. Edit notes, then cancel.
3. Repeat in full-page detail and save.

**Expected**
- Notes never autosave.
- Cancel discards only unsaved changes.
- Save persists the edited notes in both detail patterns.

### TC-1981 Tag editing stays in-context and matches canonical section structure
**Steps**
1. Open detail in drawer and full-page patterns.
2. Add and remove tags in each view.
3. Compare section order and edit affordances.

**Expected**
- Tag edits happen in-context without a separate management screen.
- Drawer and page views keep the same section order.
- Editing affordances match the canonical detail contract.

## T-031 Deterministic Row-Selection Continuity Implementation Test Cases

### TC-1990 Pagination never replaces a hidden selection with a fallback row
**Steps**
1. Select a job in a paginated result set.
2. Change to another page where that job is not visible.
3. Return to the original page or otherwise make the job visible again.

**Expected**
- Selection clears when the job is no longer visible.
- No first-row or nearest-row fallback is auto-selected.
- The exact prior job restores only when it is visible again.

### TC-1991 Dashboard return preserves continuity state across responsive layouts
**Steps**
1. Select a job on desktop or tablet.
2. Leave to dashboard, then re-enter jobs on a different breakpoint.
3. Inspect selection and helper-state behavior.

**Expected**
- Filters and sort restore before selection logic runs.
- Selection restores only when the same job is visible.
- Breakpoint changes do not mutate continuity state by themselves.

## T-032 Stale Gate A Doc Cleanup Test Cases

### TC-2000 Legacy workflow statuses are removed or explicitly superseded in stale docs
**Steps**
1. Review older Gate A-related docs, especially stale auto-close and MVP story references.
2. Check for `flagged`, `reviewing`, `skipped`, `interview`, and `not a match` presented as canonical workflow statuses.

**Expected**
- Legacy workflow statuses are removed from implementation-facing docs or clearly marked superseded.
- Cleaned docs point back to the PM memo, API contract, and reconciliation matrix.
- Engineering cannot reasonably mistake stale semantics for current source of truth.

## T-033 Development Plan Ticket Language Reconciliation Test Cases

### TC-2010 Active ticket language no longer treats fit signals as workflow state
**Steps**
1. Review all active-ticket rows in `projects/jobtrackr/DEVELOPMENT_PLAN.md`.
2. Inspect task names, DoD text, AC text, and gap notes for low-fit, flagged, match-rating, and table-state wording.

**Expected**
- Active-ticket language does not present `flagged` or low-fit handling as workflow status.
- Match rating and low-fit wording are consistently described as fit signals or table semantics.
- Ticket copy points back to canonical source docs when implementation detail depends on locked contracts.

### TC-2011 Reconciled development plan keeps canonical workflow enum intact
**Steps**
1. Review all workflow references in the active-task table and notes sections.
2. Compare them to the canonical workflow enum in the PM memo and API contract.

**Expected**
- Only `new`, `interested`, `applied`, `interviewing`, `offer`, and `rejected` appear as workflow statuses.
- `not a match`, `flagged`, `reviewing`, `skipped`, and `interview` do not appear as canonical workflow outcomes in the development plan.
- Any legacy phrasing is removed or clearly marked superseded.

## T-034 Milestone and Ticket Wording Reconciliation Test Cases

### TC-2020 Milestone steps align with active-ticket semantics after the rewrite pass
**Steps**
1. Compare Milestones 1 to 8 in `DEVELOPMENT_PLAN.md` against the active-ticket table.
2. Inspect workflow, fit, and table semantics across both sections.

**Expected**
- Milestone steps do not contradict the active-ticket wording.
- Low-fit handling remains separate from workflow state throughout milestone text.
- Table payload and match-rating semantics stay consistent between milestone steps and active tasks.

### TC-2021 Milestone 6 low-fit wording stays canonical end to end
**Steps**
1. Review Milestone 6 steps and exit criteria.
2. Compare low-fit handling wording with T-006, T-033, and the Gate A reconciliation docs.

**Expected**
- Milestone 6 does not imply auto-close workflow mutation for low-fit jobs.
- Exit criteria describe fit indicators separately from workflow state.
- Low-fit jobs remain representable without introducing a synthetic workflow enum.

## T-045 PROJECT and Handoff Reference Reconciliation Test Cases

### TC-2030 PROJECT.md uses the same workflow and fit language as DEVELOPMENT_PLAN
**Steps**
1. Review `projects/jobtrackr/PROJECT.md`.
2. Compare status-model, fit, and flagged references with `DEVELOPMENT_PLAN.md` and the PM memo.

**Expected**
- PROJECT.md uses the canonical workflow enum only.
- Fit percentage, low-fit, and flagged behavior are described as separate signals, not workflow transitions.
- Next steps and active risks do not reintroduce stale semantics.

### TC-2031 Handoff-facing references point to reconciled source documents
**Steps**
1. Review PROJECT links to handoff, contract, and reconciliation docs.
2. Verify linked files represent the current canonical source set.

**Expected**
- PROJECT.md points engineers to the reconciled development plan, PM memo, API contract, and handoff package.
- Deprecated or compatibility-shim references are not presented as the preferred implementation path.

## T-046 PRD and QA Reference Reconciliation Test Cases

### TC-2040 PRD no longer exposes legacy workflow or auto-close semantics
**Steps**
1. Review the PRD and related MVP story docs.
2. Inspect workflow, archive, match-rating, low-fit, and flagged language.

**Expected**
- PRD does not present legacy statuses as canonical workflow states.
- PRD does not present `archived` as a workflow status.
- Dashboard language describes match rating and low-fit as fit signals rather than workflow mutation.
- If older language must remain for history, it is clearly marked non-canonical.

### TC-2041 QA-owned test cases align with the canonical workflow and fit model
**Steps**
1. Review in-progress QA test cases and continuity matrices.
2. Inspect whether any QA case still expects stale workflow enums or auto-close behavior.

**Expected**
- QA cases test canonical workflow statuses only.
- QA coverage for low-fit behavior checks fit-signal handling, not fake status transitions.
- QA docs keep archived behavior separate from workflow status expectations.
- QA docs do not keep stale expectations alive after the reconciliation pass.

## T-047 Remaining Product and Spec Reference Reconciliation Test Cases

### TC-2050 Remaining specs stop using flagged as workflow shorthand
**Steps**
1. Review remaining product and implementation specs called out by the reconciliation pass.
2. Inspect flagged references in table UI, fit-analysis, and data-model-facing docs.

**Expected**
- `flagged` is not described as a workflow state.
- Any flagged behavior is clearly framed as shortlist or fit-signal behavior only where canonically supported.
- Spec wording does not let engineering confuse fit indicators with workflow state.

### TC-2051 Cross-spec workflow and fit language is internally consistent
**Steps**
1. Compare PM memo, API contract, schema, table UI spec, fit-analysis spec, and first-slice docs.
2. Check for conflicting workflow, archive, and fit-signal language.

**Expected**
- Cross-spec language is consistent enough for engineering pickup without PM clarification.
- Archive, saved, workflow, and fit concepts remain distinct across all reviewed docs.
- Any surviving stale references are isolated and clearly marked as blockers.

## T-048 Parser and Ingestion Ticket Wording Reconciliation Test Cases

### TC-2060 Parser follow-through docs do not imply parser-driven workflow mutation
**Steps**
1. Review Milestone 4 ticket breakdown and parser follow-through docs.
2. Inspect references to low-fit, rejected outcomes, and parser responsibilities.

**Expected**
- Parser work is limited to ingestion, extraction, dedupe, and persistence concerns.
- Parser docs do not imply `not a match` or any other non-canonical workflow outcome.
- Low-fit handling points back to the canonical fit-analysis contract where relevant.

### TC-2061 Parser and ingestion tickets preserve source-email retention and debug traceability
**Steps**
1. Review parser and ingestion acceptance language after reconciliation.
2. Inspect source-email retention, extraction-threshold, and dedupe wording.

**Expected**
- Tickets still require source-email retention even when no job row is created.
- Debug traceability remains explicit.
- Workflow-state semantics stay separate from parser acceptance criteria.

## T-049 Persisted Jobs UI Mapping Reconciliation Test Cases

### TC-2070 Persisted jobs UI rejects legacy workflow aliases from server-backed data
**Steps**
1. Review persisted-jobs frontend mapping docs and sample payload handling.
2. Inspect how `flagged`, `not a match`, and any other legacy alias values are treated when received from old data.

**Expected**
- UI mapping does not accept legacy workflow aliases as canonical workflow state.
- Legacy alias values are normalized to a safe canonical fallback or blocked with explicit handling.
- The rendered UI never shows `flagged` or `not a match` as workflow statuses.

### TC-2071 Server-backed rows show canonical fit defaults when analysis data is absent
**Steps**
1. Review server-backed job-result examples with null or missing fit fields.
2. Compare the displayed match and low-fit treatment to the canonical fit-signal contract.

**Expected**
- Missing fit analysis produces a deterministic pending-fit or equivalent canonical state.
- UI does not infer low-fit or match rating from freeform text.
- Fallback behavior is documented clearly enough for QA and frontend sign-off.

## T-050 PRD v2 and Active QA Coverage Reconciliation Test Cases

### TC-2080 PRD v2 keeps archived separate from workflow state in dashboard and status sections
**Steps**
1. Review `prd/jobtrackr-prd-v2.md` dashboard, status, and fit-language sections.
2. Compare workflow and archive wording against the PM memo and development plan.

**Expected**
- Archived remains a view or archive-state concept only.
- PRD v2 uses the canonical workflow enum only.
- Match rating and low-fit are described as fit signals, not workflow mutation.

### TC-2081 In-progress QA coverage mirrors PRD v2 after reconciliation
**Steps**
1. Compare active QA test cases with PRD v2 after the reconciliation pass.
2. Inspect whether any QA case still expects stale workflow or auto-close behavior.

**Expected**
- QA cases align with reconciled PRD wording.
- Archived behavior stays separate from workflow status expectations.
- Low-fit coverage remains fit-signal based instead of expecting a status change.

## T-051 Fit-Signal Wording Reconciliation Test Cases

### TC-2085 API, frontend, and clarification docs stop using flagged/unflagged as canonical product states
**Steps**
1. Review `specs/jobtrackr-api-contract.md`, `specs/jobtrackr-frontend-plan-v1.md`, and `specs/jobtrackr-spec-clarifications-needed.md`.
2. Inspect workflow, filters, fit-display, and fallback wording.

**Expected**
- `flagged` and `unflagged` do not appear as canonical workflow, filter, or user-facing state names.
- Canonical fit-signal wording uses match rating, low-fit, strong-fit, fit-ready, or pending-fit where applicable.
- Any retained legacy wording is explicitly labeled historical or superseded.

## T-052 Persisted Jobs Frontend Hardening Test Cases

### TC-2090 Unknown workflow values degrade safely in persisted jobs UI
**Steps**
1. Review frontend fallback handling for persisted job rows containing unknown workflow values.
2. Inspect how the UI renders those rows before and after normalization.

**Expected**
- Unknown workflow values do not surface raw legacy text to users.
- UI falls back to one safe canonical presentation path.
- Fallback handling is deterministic and documented for QA.

### TC-2091 Missing fit analysis preserves pending-fit behavior without alias inference
**Steps**
1. Review persisted job payload examples with missing match rating, low-fit metadata, or fit summary.
2. Inspect UI behavior for those rows.

**Expected**
- UI preserves a pending-fit or equivalent canonical state when fit data is absent.
- It does not infer legacy workflow aliases or synthetic low-fit workflow state.
- Behavior matches the current API and fit-signal contracts.

## T-053 Legacy Story and Draft Spec Reconciliation Test Cases

### TC-2100 Older stories and superseded drafts are marked non-canonical or rewritten cleanly
**Steps**
1. Review older story files and superseded PRD or spec drafts still present in the repo.
2. Inspect workflow, fit-signal, and auto-close wording.

**Expected**
- Historical files are either reconciled or explicitly marked non-canonical.
- They do not present legacy workflow states as current implementation truth.
- Each preserved historical file points readers back to the canonical source set.

## T-054 Entrypoint and Clarification Doc Reconciliation Test Cases

### TC-2110 PROJECT and clarification docs redirect engineers to the canonical source set
**Steps**
1. Review `PROJECT.md` and clarification-style docs.
2. Inspect top-level references to workflow, fit signals, and source-of-truth guidance.

**Expected**
- High-level entrypoint docs point first to the reconciled development plan, PM memo, API contract, and handoff package.
- Historical shorthand like `flagged` or `unflagged` is framed as legacy wording only, if retained at all.
- Engineers are unlikely to start implementation from stale context.

## T-055 Persisted Jobs Fallback Visibility Test Cases

### TC-2115 Persisted-jobs UI exposes canonical fallback reasons without surfacing legacy aliases
**Steps**
1. Review persisted-jobs UI states or examples that cover missing fit data and normalized workflow values.
2. Inspect per-row or result-set messaging for fallback explanation.

**Expected**
- Users can tell when a row is pending fit versus using normalized canonical workflow output.
- Fallback copy avoids legacy aliases like `flagged` or `not a match`.
- The explanation is specific enough for QA to assert against deterministic UI text or state.

## T-056 Kickoff Entrypoint Reconciliation Test Cases

### TC-2116 README and first-build-slice docs point engineers at the active auth-to-dashboard vertical slice
**Steps**
1. Review `README.md` and `specs/jobtrackr-first-build-slice-v1.md`.
2. Compare the kickoff guidance to `PROJECT.md`, `DEVELOPMENT_PLAN.md`, and the engineering handoff package.

**Expected**
- Entrypoint docs describe the current project phase accurately.
- The first build slice points to the canonical auth, Gmail connect, and persisted-jobs vertical-slice direction.
- Source-of-truth links are current and reduce the risk of engineers starting from stale discovery framing.

## T-057 PRD and QA Blocker Checklist Reconciliation Test Cases

### TC-2120 PRD v2 removes stale archive-status ambiguity and points to current source docs
**Steps**
1. Review `prd/jobtrackr-prd-v2.md`.
2. Inspect dashboard, workflow, archive, and fit-signal wording plus any implementation-facing reference notes.

**Expected**
- PRD v2 does not leave `saved` or `archived` framed as unresolved status-model questions.
- Archived remains separate from workflow state.
- PRD v2 points readers to the current PM memo, API contract, and development plan for canonical semantics.

### TC-2121 QA blocker checklist uses current paths and canonical fit-signal wording
**Steps**
1. Review `specs/jobtrackr-qa-blocker-checklist-2026-04-19.md`.
2. Inspect source-document references and the shortest implementation-facing blocker rules.

**Expected**
- The checklist points to valid current project paths.
- Archive, match-rating, low-fit, and pending-fit semantics remain clearly separated from workflow state.
- QA can use the checklist without inheriting stale status-model ambiguity.

## T-058 Roadmap Fit-Language Reconciliation Test Cases

### TC-2125 ROADMAP uses canonical fit-signal language in release scope and exit criteria
**Steps**
1. Review `ROADMAP.md` release goals, milestone language, and exit criteria.
2. Compare fit-language usage against the PM memo, DEVELOPMENT_PLAN, and reconciliation matrix.

**Expected**
- Release sequencing does not present `flagged` or `unflagged` as product-state vocabulary.
- Match rating, low-fit, fit-ready, strong-fit, and pending-fit stay separate from workflow state.
- Roadmap wording does not reopen already-locked workflow semantics.

## T-062 QA Source-Path and Environment Alignment Test Cases

### TC-2140 QA entrypoint docs point to the live project root
**Steps**
1. Review `specs/jobtrackr-qa-blocker-checklist-2026-04-19.md` and any QA-facing source-document lists in active docs.
2. Verify path references against the actual repo layout under `projects/jobtrackr/`.

**Expected**
- QA-facing entrypoint docs point to valid `projects/jobtrackr/...` paths.
- No implementation-facing QA doc sends engineers to the dead root-level `~/Documents/project-requirements/DEVELOPMENT_PLAN.md` path.
- The shortest blocker checklist remains safe to use from the shared project root.

### TC-2141 QA environment notes match the current web-plus-API implementation slice
**Steps**
1. Review active QA notes that mention runtime or app shape, especially around auth, Gmail connect, and persisted-jobs flows.
2. Compare those notes with the current handoff and milestone docs.

**Expected**
- QA docs treat the Next.js web shell plus Go API split as the current implementation context.
- Older local-Go-app wording is explicitly framed as historical or task-scoped, not as the default environment assumption for new execution coverage.
- QA guidance does not force engineers to infer which app shape governs the active auth -> Gmail -> persisted-jobs slice.

## T-063 Roadmap and Legacy Review Source-Reference Reconciliation Test Cases

### TC-2142 Preserved planning docs recover to live project-root source files
**Steps**
1. Review `ROADMAP.md` plus preserved PM and QA review docs referenced during kickoff recovery work.
2. Inspect implementation-facing source-document lists and recovery notes.

**Expected**
- Planning and review docs point to valid `projects/jobtrackr/...` paths.
- Recovery notes explicitly redirect readers away from the dead root-level development-plan path.
- The active auth -> session -> Gmail readonly connect -> persisted jobs slice is called out clearly enough for safe pickup.

## T-064 Automation-Facing Kickoff Reference Reconciliation Test Cases

### TC-2143 Hourly recovery notes call out the live development-plan path explicitly
**Steps**
1. Review automation-facing kickoff guidance and QA-facing recovery notes.
2. Inspect whether the stale `~/Documents/project-requirements/DEVELOPMENT_PLAN.md` path is still presented as an active source.

**Expected**
- Automation-facing guidance names `~/Documents/project-requirements/projects/jobtrackr/DEVELOPMENT_PLAN.md` as the live file.
- The dead root-level path is framed only as historical drift or an external coordination issue.
- QA can recover safely without guessing which development plan is current.

## T-065 Historical Kickoff Slice Recovery Test Cases

### TC-2144 Preserved first-build docs label mock-first guidance as historical
**Steps**
1. Review preserved first-build and kickoff docs.
2. Inspect their recovery notes, path examples, and architecture framing.

**Expected**
- Preserved kickoff docs explicitly label mock-first guidance as historical context.
- They redirect readers to the live `projects/jobtrackr/...` source files.
- The current auth -> session -> Gmail readonly connect -> persisted jobs lane is described as the active pickup path.

## T-066 QA Recovery Reference Reconciliation Test Cases

### TC-2145 PRD v2 and active QA coverage share the same recovery note
**Steps**
1. Review `prd/jobtrackr-prd-v2.md` and `stories/jobtrackr-in-progress-test-cases-2026-04-19.md`.
2. Compare their source-of-truth references, stale-path recovery guidance, and implementation-context notes.

**Expected**
- Both docs point to the same live `projects/jobtrackr/...` entrypoints.
- Both docs treat the dead root-level development-plan path as stale external drift.
- Both docs describe the current implementation as Next.js web plus Go API on the auth -> session -> Gmail readonly connect -> persisted jobs slice.

## T-070 QA Recovery Wording Normalization Test Cases

### TC-2148 PRD, blocker checklist, and active QA coverage use one recovery pattern
**Steps**
1. Review `prd/jobtrackr-prd-v2.md`, `specs/jobtrackr-qa-blocker-checklist-2026-04-19.md`, and `stories/jobtrackr-in-progress-test-cases-2026-04-19.md`.
2. Compare their stale-path recovery notes, ordered source-of-truth references, and architecture wording.

**Expected**
- All three docs redirect readers away from `~/Documents/project-requirements/DEVELOPMENT_PLAN.md` as stale external drift.
- All three docs point QA back to the same explicit `projects/jobtrackr/...` source files.
- All three docs describe the active implementation as Next.js web plus Go API on the auth -> session -> Gmail readonly connect -> persisted jobs slice.

## T-069 Automation Recovery Example Normalization Test Cases

### TC-2149 Automation-facing examples recover to one canonical source-of-truth set
**Steps**
1. Review preserved hourly kickoff guidance, automation-facing recovery notes, and any copied example path lists used during recovery work.
2. Compare the example recovery order and architecture wording against `PROJECT.md`, `DEVELOPMENT_PLAN.md`, and the Phase 3 handoff package.

**Expected**
- Recovery examples point first to the same explicit `projects/jobtrackr/...` files.
- Example wording treats the dead root-level development-plan path as stale prompt drift only.
- The current implementation is described consistently as Next.js web plus Go API on the auth -> session -> Gmail readonly connect -> persisted jobs slice.

## T-067 Roadmap and Legacy Review Kickoff Recovery Test Cases

### TC-2146 Planning artifacts recover to the current web-plus-API delivery slice
**Steps**
1. Review roadmap and preserved review artifacts updated during kickoff recovery.
2. Inspect architecture wording and implementation-slice references.

**Expected**
- Planning artifacts describe the active implementation as Next.js web plus Go API.
- They point to the current auth -> session -> Gmail readonly connect -> persisted jobs lane.
- Older local-Go-app wording is clearly marked historical where it still appears.

## T-068 Kickoff Entrypoint Path Normalization Test Cases

### TC-2147 README, PROJECT, and handoff package use the same explicit project-root paths
**Steps**
1. Review `README.md`, `PROJECT.md`, and the Phase 3 handoff package.
2. Compare ordered source-of-truth lists and any example file paths used during recovery guidance.

**Expected**
- All kickoff entrypoints use the same explicit `projects/jobtrackr/...` paths.
- Bare repo-root filenames are not left ambiguous in implementation-facing recovery notes.
- Engineers can move between README, PROJECT, and handoff docs without path drift.

## T-071 Preserved Engineering Handoff Recovery Test Cases

### TC-2150 Legacy handoff docs redirect to live project-root files and current slice
**Steps**
1. Review preserved engineering handoff docs and older kickoff guidance kept for historical context.
2. Inspect whether they label older build-order guidance as historical and point to the live project-root source set.

**Expected**
- Preserved handoff docs explicitly label legacy guidance as historical context.
- They redirect readers to `projects/jobtrackr/DEVELOPMENT_PLAN.md`, `projects/jobtrackr/PROJECT.md`, and the current Phase 3 handoff package.
- They describe the active implementation as Next.js web plus Go API on the auth -> session -> Gmail readonly connect -> persisted jobs slice.

## T-072 Kickoff Recovery Order and Lane Wording Test Cases

### TC-2151 README, PROJECT, and handoff package use the same ordered recovery path
**Steps**
1. Review kickoff-facing entrypoint docs updated during T-072.
2. Compare their ordered source-of-truth lists and active implementation wording.

**Expected**
- All kickoff-facing docs use the same ordered recovery sequence.
- Active lane wording is consistent across README, PROJECT, and handoff package.
- Bare filenames do not force engineers to guess whether a path is live, shorthand, or historical.

## T-073 Preserved Handoff Wording Normalization Test Cases

### TC-2152 Older handoff docs share the same recovery wording and legacy labeling
**Steps**
1. Review older preserved handoff docs after wording-normalization changes.
2. Compare recovery notes, legacy labels, and architecture wording against current kickoff-facing entrypoints.

**Expected**
- Older handoff docs use the same explicit project-root entrypoints as current kickoff docs.
- Historical build-order or mock-first notes are clearly labeled as preserved context.
- Recovery wording does not reintroduce older local-Go-app framing as the default implementation context.

## T-074 Preserved Implementation-Facing Recovery Note Test Cases

### TC-2153 Older handoff and QA review docs share one absolute-path recovery order
**Steps**
1. Review preserved implementation-facing handoff and QA review docs updated during T-074.
2. Compare their stale-path recovery notes and ordered absolute-path examples.

**Expected**
- Both doc families use the same explicit absolute-path recovery order.
- The dead root-level development-plan path is framed only as stale prompt drift.
- Current lane wording stays aligned with the Next.js web plus Go API auth -> session -> Gmail readonly connect -> persisted jobs slice.

## T-075 Shared QA Recovery Note Normalization Test Cases

### TC-2154 PRD, blocker checklist, and active QA coverage reuse the same recovery note verbatim
**Steps**
1. Review `prd/jobtrackr-prd-v2.md`, `specs/jobtrackr-qa-blocker-checklist-2026-04-19.md`, and `stories/jobtrackr-in-progress-test-cases-2026-04-19.md`.
2. Compare the recovery note text, path order, and active-lane wording byte for byte.

**Expected**
- All three docs reuse the same recovery note without path-order drift.
- The note names the stale root-level development-plan path explicitly.
- The active implementation lane wording matches exactly across all three docs.

## T-085 Kickoff Recovery Byte-Alignment Completion Test Cases

### TC-2157 README, PROJECT, and handoff package now share one recovery order and frontend ownership note
**Steps**
1. Review `README.md`, `PROJECT.md`, and `specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`.
2. Compare the ordered `projects/jobtrackr/...` recovery path examples.
3. Compare the current lane note for Alice and Marcus.

**Expected**
- All three kickoff-facing docs use the same explicit project-root recovery order.
- All three describe Alice and Marcus as frontend owners for the active slice.
- No kickoff-facing doc falls back to the stale root-level `DEVELOPMENT_PLAN.md` path as the live entrypoint.

## T-086 QA Hourly Coverage Refresh Test Cases

### TC-2158 Active QA coverage reflects latest Jimmy fetch and current repo blocker state
**Steps**
1. Review the latest five messages from `#pm-jimmy`.
2. Compare the current QA hourly summary against `projects/jobtrackr/DEVELOPMENT_PLAN.md`.
3. Inspect whether active blocker notes still mention the old Jimmy-fetch 401 story or the resolved JobsTable compile blocker as live blockers.
4. Verify the QA hourly summary distinguishes external kickoff-path drift from repo-side diff-verification follow-through.

**Expected**
- QA hourly coverage records that Jimmy fetch succeeds again.
- QA notes treat the stale external kickoff path as the remaining coordination gap.
- Repo-side follow-through is described as shared QA recovery-doc and kickoff-entrypoint verification work, not a newly reopened engineering blocker.
- Resolved JobsTable compile/build failure is preserved as verification evidence, not an active engineering blocker.

## T-111 Shared Alice Lane Note Closeout Test Cases

### TC-2157 Active QA coverage records the byte-aligned shared Alice lane note check
**Steps**
1. Review `DEVELOPMENT_PLAN.md` plus the preserved implementation-facing docs touched by this closeout.
2. Confirm `T-111` points to the preserved handoff v1 and QA testability review verification work.
3. Compare the `Shared Alice lane note` block in `specs/jobtrackr-engineering-handoff-v1.md` and `specs/jobtrackr-mvp-qa-testability-review.md`.

**Expected**
- `DEVELOPMENT_PLAN.md` records the explicit shared-note closeout as the active implementation-facing verification task.
- Both preserved docs expose the same `Shared Alice lane note` label.
- Both preserved docs reuse the same Alice lane wording: `Alice: frontend delivery on the auth -> session -> Gmail readonly connect -> persisted-jobs experience`.

## T-087 Shared Alice Lane Note Verification Test Cases

### TC-2156 Preserved handoff v1 and QA testability review expose the same explicit Alice lane note
**Steps**
1. Review `specs/jobtrackr-engineering-handoff-v1.md` and `specs/jobtrackr-mvp-qa-testability-review.md`.
2. Locate the `Shared Alice lane note` block in each preserved implementation-facing doc.
3. Compare the Alice lane note text byte for byte.

**Expected**
- Both docs expose the same `Shared Alice lane note` label.
- Both docs reuse the same Alice lane wording: `Alice: frontend delivery on the auth -> session -> Gmail readonly connect -> persisted-jobs experience`.
- The shared Alice note appears alongside the same absolute-path recovery order and active Next.js web plus Go API slice wording.

## T-076 Preserved Planning and Handoff Recovery Note Test Cases

### TC-2155 ROADMAP, PM memo, QA review, and preserved handoff docs share one recovery pattern
**Steps**
1. Review ROADMAP, the PM memo, QA testability review notes, and preserved handoff docs touched during T-076.
2. Compare recovery order, stale-path labeling, and implementation-lane wording.

**Expected**
- Preserved planning and handoff docs use one consistent recovery pattern.
- Recovery order points to the same live `projects/jobtrackr/...` kickoff set.
- The current Next.js web plus Go API lane wording is consistent across all preserved planning and handoff entrypoints.

## T-059 Protected Session Shell and Gmail Disconnect Test Cases

### TC-2130 Local shell requires authenticated session before inbox-derived jobs are visible
**Steps**
1. Launch the local app without an active app session.
2. Attempt to open the jobs workspace or any inbox-derived results surface.
3. Complete auth, then retry access.

**Expected**
- Unauthenticated access is redirected, denied, or gated behind the local shell.
- Inbox-derived jobs are not exposed before session establishment.
- Authenticated users can reach the intended workspace after session setup.

### TC-2131 Gmail disconnect cleanly removes Gmail access without breaking local app session handling
**Steps**
1. Start from an authenticated user with Gmail connected.
2. Trigger Gmail disconnect.
3. Refresh the app and inspect connection state plus session state.

**Expected**
- Gmail connection state changes to a disconnected or reconnect-required canonical state.
- The app does not require manual local-state deletion to recover.
- Session handling and reconnect affordances remain deterministic after disconnect.

## T-116 T-106 Sign-off Checklist Publication Test Cases

### TC-2171 T-106 checklist covers the full frontend QA gate from one note
**Steps**
1. Review `specs/jobtrackr-persisted-jobs-workspace-signoff-checklist-2026-04-23.md`.
2. Compare its scope against `DEVELOPMENT_PLAN.md`, the live handoff package, and current QA coverage.
3. Check whether repo-side verification, API-up validation, API-down fallback validation, and fallback-reason assertions are all included.

**Expected**
- One checklist covers the full T-106 QA path without requiring Sam to reconstruct the proof set from multiple files.
- The checklist explicitly requires `npm test` and `npm run build` before UI QA starts.
- The checklist names the live routes, fallback surfaces, and canonical pending-fit or workflow-normalization assertions that still gate sign-off.

## T-117 Hourly Ticket-Source Recovery Test Cases

### TC-2172 Hourly recovery guidance distinguishes live markdown recovery from long-term GitHub tracking
**Steps**
1. Review the recovery guidance added to `DEVELOPMENT_PLAN.md` and linked kickoff-facing docs.
2. Compare the hourly recovery instructions with the current active and completed task tables.
3. Inspect whether the docs explain what to do when no genuinely unclaimed row remains for the current lane.

**Expected**
- Docs explicitly say hourly recovery still starts from the project-root markdown handoff set.
- Docs distinguish durable GitHub tracking from the live markdown recovery ledger.
- Docs instruct the next agent to add the smallest safe follow-through task rather than hijacking a QA-owned or completed row.

## T-130 PRD QA Gate Pair Alignment Test Cases

### TC-2175 PRD v2 surfaces the same live QA gate pair as blocker and active QA docs
**Steps**
1. Review `projects/jobtrackr/prd/jobtrackr-prd-v2.md`.
2. Compare its implementation-facing QA recovery note against `projects/jobtrackr/specs/jobtrackr-qa-blocker-checklist-2026-04-19.md` and this active QA coverage file.
3. Inspect whether the named live QA gates and sign-off entrypoints match exactly.

**Expected**
- PRD v2 names T-106 and T-095 as the live QA gate pair.
- PRD v2 points directly to `projects/jobtrackr/specs/jobtrackr-persisted-jobs-workspace-signoff-checklist-2026-04-23.md` and `projects/jobtrackr/specs/jobtrackr-source-email-persistence-signoff-checklist-2026-04-22.md`.
- Product-facing recovery notes do not force QA to infer the current sign-off entrypoints from other docs.

### TC-2176 PRD v2 keeps the stale root-path warning and current slice wording aligned with QA docs
**Steps**
1. Review the recovery note and implementation-slice wording in `projects/jobtrackr/prd/jobtrackr-prd-v2.md`.
2. Compare it with `projects/jobtrackr/specs/jobtrackr-qa-blocker-checklist-2026-04-19.md` and `projects/jobtrackr/stories/jobtrackr-in-progress-test-cases-2026-04-19.md`.
3. Check path examples and the current architecture phrasing.

**Expected**
- PRD v2 treats `~/Documents/project-requirements/DEVELOPMENT_PLAN.md` as stale external drift only.
- PRD v2 reuses the same ordered recovery path back to `projects/jobtrackr/DEVELOPMENT_PLAN.md`, `projects/jobtrackr/PROJECT.md`, and the live Phase 3 handoff package.
- PRD v2 describes the active implementation as the Next.js web plus Go API auth -> session -> Gmail readonly connect -> persisted jobs slice, without reintroducing older local-Go-app wording.

## T-118 Backend Provenance Recovery Pointer Test Cases

### TC-2173 Kickoff-facing docs route backend provenance recovery through T-091, T-095, and T-108
**Steps**
1. Review `README.md`, `PROJECT.md`, and `specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`.
2. Inspect Frank-facing backend/shared-contract recovery notes.
3. Compare those notes with the current live provenance tasks in `DEVELOPMENT_PLAN.md`.

**Expected**
- Kickoff-facing docs point Frank's recovery through T-091, T-095, and T-108 explicitly.
- Provenance recovery notes do not force engineers to guess which source-email docs are current.
- Backend/shared-contract pickup stays aligned with the live project-root recovery order and current provenance sign-off set.

## T-119 Preserved Implementation Slice Wording Alignment Test Cases

### TC-2174 Preserved implementation-facing docs reuse the same current slice wording
**Steps**
1. Review `specs/jobtrackr-first-build-slice-v1.md`, `specs/jobtrackr-engineering-handoff-v1.md`, and `specs/jobtrackr-mvp-qa-testability-review.md`.
2. Inspect each file's current implementation slice note.
3. Compare the wording against the live repo phrasing used in QA-facing recovery docs.

**Expected**
- All three preserved implementation-facing docs describe the active lane as `auth -> session -> Gmail readonly connect -> persisted jobs slice`.
- None of the preserved docs reintroduce `persisted jobs UI lane` as the current recovery wording.
- The preserved notes stay aligned with the live Next.js web plus Go API implementation framing used elsewhere in the repo.

## T-106 Persisted Jobs Workspace Handoff Test Cases

### TC-2164 Dashboard loads persisted jobs when the API is available
**Steps**
1. Start the authenticated web app with the persisted-jobs API reachable.
2. Open `/dashboard` with server-backed jobs available.
3. Inspect the rendered rows, source labels, and fallback messaging.

**Expected**
- Dashboard loads persisted jobs through the shared data layer instead of mock-only fixtures.
- Rows render canonical workflow values only.
- Any pending-fit or workflow-normalization notice is visible without breaking table usability.

### TC-2165 Jobs workspace falls back safely to mock data when the API is unavailable
**Steps**
1. Start the web app with the jobs API unavailable or forced into a fetch failure.
2. Open `/jobs` from an authenticated session.
3. Inspect rendered rows, navigation behavior, and fallback messaging.

**Expected**
- The workspace remains usable instead of blanking or crashing.
- Mock fallback is clearly labeled as fallback behavior.
- Selection continuity and dashboard-to-jobs navigation still work in fallback mode.

### TC-2166 Persisted-job fallback reasons are visible at result-set level
**Steps**
1. Seed API results that include one row with missing fit data and one row normalized from a legacy workflow value.
2. Open `/dashboard` and `/jobs`.
3. Compare the fallback notices and row rendering.

**Expected**
- Missing fit data renders a canonical pending-fit state.
- Normalized workflow values do not surface raw legacy aliases in the UI.
- The result set explains why fallback handling was applied.

## T-108 Source-Email Debug Read Endpoint Test Cases

### TC-2167 Source-email debug endpoint returns persisted provenance rows with supported filters
**Steps**
1. Start the API with persisted `source_emails` data available.
2. Call `GET /api/v1/source-emails` with no filters.
3. Repeat with `limit`, `matchedAsJob`, `fromEmail`, and `search` filters.

**Expected**
- Endpoint returns persisted provenance rows without requiring direct database access.
- Supported filters narrow results deterministically.
- Response is stable enough for operator and QA inspection workflows.

### TC-2168 Source-email debug reads preserve provenance inspection without exposing write behavior
**Steps**
1. Call `GET /api/v1/source-emails` against known persisted data.
2. Inspect whether returned rows include message id, sender, subject, linkage-relevant fields, and parse-state context.
3. Confirm no debug-read action mutates persistence state.

**Expected**
- Response exposes enough provenance detail to validate `source_emails` records and job linkage follow-through.
- Debug read stays read-only.
- QA can inspect recent source emails without reconstructing state from sync logs.

## T-127 Repo Root README Kickoff Alignment Test Cases

### TC-2175 Repo root README surfaces the live QA gate pair and project-root recovery order
**Steps**
1. Review `README.md` at the repo root.
2. Compare its kickoff note against `projects/jobtrackr/PROJECT.md`, `projects/jobtrackr/DEVELOPMENT_PLAN.md`, and Jimmy's latest PM note.
3. Inspect whether the README points QA and engineering to the live T-106 and T-095 sign-off entrypoints before local setup details.

**Expected**
- The repo root README uses the same recovery order: `projects/jobtrackr/DEVELOPMENT_PLAN.md`, `projects/jobtrackr/PROJECT.md`, and `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`.
- The README calls out T-106 and T-095 as the two live QA gates and points to T-116 and T-102 as their implementation-facing sign-off checklists.
- The README frames the current implementation as the Next.js web plus Go API auth -> session -> Gmail readonly connect -> persisted jobs slice, not older mock-only or root-level-path guidance.

## T-130 PRD Live QA Gate Alignment Test Cases

### TC-2176 PRD v2 surfaces the live QA gate pair and sign-off entrypoints
**Steps**
1. Review `projects/jobtrackr/prd/jobtrackr-prd-v2.md`.
2. Compare its QA recovery note and live QA entrypoints against `projects/jobtrackr/specs/jobtrackr-qa-blocker-checklist-2026-04-19.md` and `projects/jobtrackr/DEVELOPMENT_PLAN.md`.
3. Inspect whether PRD v2 points QA and engineering to the live T-106 and T-095 sign-off entrypoints without requiring a second recovery hop.

**Expected**
- PRD v2 uses the same recovery order: `projects/jobtrackr/DEVELOPMENT_PLAN.md`, `projects/jobtrackr/PROJECT.md`, and `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`.
- PRD v2 calls out T-106 and T-095 as the two live QA gates and points to T-116, T-102, and the T-095 evidence bundle explicitly.
- PRD v2 frames the current implementation as the Next.js web plus Go API auth -> session -> Gmail readonly connect -> persisted jobs slice, without reintroducing dead root-level path guidance.

## T-109 Frontend Continuity Handoff Refresh Test Cases

### TC-2169 Kickoff-facing entrypoints route frontend recovery through T-106 and T-107 first
**Steps**
1. Review `README.md`, `PROJECT.md`, and `specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`.
2. Inspect the ordered frontend recovery guidance.
3. Compare that guidance with Jimmy's latest plan and the live development plan.

**Expected**
- Entry points name T-106 as the live frontend handoff and T-107 as the current QA summary before older continuity-contract docs.
- Recovery guidance keeps the remaining blocker story narrowed to T-095 plus stale external kickoff-path drift.
- Older detail and workspace continuity docs remain secondary recovery material, not the first pickup.

## T-110 Roadmap-Facing Handoff Refresh Test Cases

### TC-2170 ROADMAP and PM memo point to the same live frontend handoff and blocker boundary
**Steps**
1. Review `ROADMAP.md` and `specs/jobtrackr-pm-decision-memo-2026-04-19.md`.
2. Compare their current handoff note and blocker wording against `DEVELOPMENT_PLAN.md` and Jimmy's latest plan.

**Expected**
- Both docs call out T-106 as the live frontend handoff and T-107 as the latest QA summary.
- Both docs keep T-095 fixture-backed validation and stale external kickoff drift as the remaining blocker boundary.
- Roadmap-facing guidance matches kickoff-facing guidance instead of lagging behind it.

## T-095 Source-Email Persistence Sign-off Test Cases

Evidence note:
- `projects/jobtrackr/specs/jobtrackr-source-email-persistence-evidence-2026-04-22.md`
- `projects/jobtrackr/specs/jobtrackr-source-email-persistence-signoff-checklist-2026-04-22.md`

### TC-2159 Gmail sync persists provenance before job upsert
**Steps**
1. Run the Gmail sync flow against a fixture mailbox with at least one new job-alert email and one duplicate alert.
2. Inspect persisted records in `source_emails`, `job_source_emails`, and the jobs table.
3. Re-run the sync against the same fixture mailbox.

**Expected**
- Each Gmail message is persisted once in `source_emails` before or alongside parsed job creation.
- Saved jobs retain linkage rows in `job_source_emails` back to one or more source emails.
- Re-running sync does not duplicate source-email rows or break provenance on the merged job record.

### TC-2160 Source-email persistence evidence is attached to QA handoff
**Steps**
1. Review QA-facing docs for T-095.
2. Check for linked test evidence covering `go test ./...`, `go build ./...`, fixture setup, and persisted-record examples.

**Expected**
- QA handoff includes concrete build/test evidence, not just narrative status.
- Fixture or sample-record references are available for provenance verification.
- Missing evidence is treated as a QA blocker for final sign-off.

### TC-2162 Reprocessing a persisted source email preserves idempotent job linkage
**Steps**
1. Persist a source email that already produced a job row.
2. Trigger the explicit reprocess path for that saved source email.
3. Inspect the resulting job row, `job_source_emails` linkage, and source-email processing metadata.

**Expected**
- Reprocessing does not create a duplicate source-email row.
- Reprocessing does not create a duplicate job when the normalized job identity is unchanged.
- Provenance linkage remains attached to the existing job, and processing metadata reflects the replay cleanly.

### TC-2163 GOG discovery output is sufficient to persist source-email-first ingestion
**Steps**
1. Run the documented GOG Gmail discovery query flow against a fixture or sample mailbox.
2. Confirm the discovered message payload includes the identifiers and metadata needed for `source_emails` persistence.
3. Compare the observed discovery fields with the source-email persistence requirements in the plan and ingestion specs.

**Expected**
- GOG discovery returns enough metadata to persist Gmail message id, thread id, sender, subject, received timestamp, and body content or normalized equivalent.
- Discovery findings can be handed into source-email persistence without relying on manual inbox-only copy/paste steps.
- Any missing discovery field is treated as a blocker against T-091 and T-095 sign-off.

## T-096 Duplicate Task-ID Normalization Test Cases

### TC-2161 DEVELOPMENT_PLAN uses unique IDs for blocker verification and source-email ingestion
**Steps**
1. Review `projects/jobtrackr/DEVELOPMENT_PLAN.md`.
2. Find the compile-blocker verification task and the source-email ingestion epic.
3. Verify downstream dependency references for the source-email persistence task.

**Expected**
- The compile-blocker verification task remains `T-090`.
- The source-email ingestion epic is renumbered to `T-091`.
- Dependent tasks such as `T-095` point at `T-091`, not the blocker-verification task.

## Current QA coverage gaps
1. T-085 is marked completed in the plan, and this hour's spot checks kept README, PROJECT, the Phase 3 handoff package, and preserved implementation-facing docs aligned on recovery order and current lane wording. Remaining QA risk is regression drift, not an identified mismatch.
2. T-006 is still blocked by status-model drift in `jobtrackr-auto-close-logic-spec-v1.md`, which still references pre-decision statuses like `flagged`, `reviewing`, `skipped`, `interview`, and `not a match` instead of the canonical workflow model.
3. T-062 now covers the remaining QA-owned environment drift between older local-Go-app wording and the current Next.js web plus Go API handoff, but execution coverage still depends on downstream task docs making that split explicit where needed.
4. Jimmy's cron note still points QA at `~/Documents/project-requirements/DEVELOPMENT_PLAN.md`, but the live plan is actually `projects/jobtrackr/DEVELOPMENT_PLAN.md`; T-062 now tracks that coordination gap inside the repo-facing QA docs.
5. Gate A is still not fully closed, because the reconciliation matrix exists but older schema, story, PRD, and auto-close references still expose stale workflow or linkage assumptions.
6. Real runnable API fixtures are still needed before list and workspace checks for T-007, T-011, T-013, T-016, T-020, T-024, and T-025 can graduate from contract validation into executable integration tests.
7. The duplicate list/detail example naming issue is now partially mitigated: `jobtrackr-list-detail-examples-2026-04-20.md` is retained only as a compatibility shim, and `jobtrackr-list-detail-contract-examples-2026-04-20.md` is the canonical source. QA should verify new cross-links keep pointing only to the canonical file.
8. Gmail connection-state edge cases like `expired`, `revoked`, and `denied` now have acceptance coverage, but QA still needs runnable fixtures or test accounts before T-018, T-022, and T-059 can be signed off.
9. T-010 and T-014 ticket work now describe Gate A and Gate B, but QA still needs engineering to treat those gates as hard blockers before live-data integration starts.
10. T-023, T-024, and T-025 now have stronger canonical docs, but those examples and reconciliation rules still need explicit cross-links from the API contract, workspace contract, and handoff package so implementation teams do not fork behavior.
11. T-026, T-029, T-052, and T-055 still lack runnable persisted-jobs fixtures or endpoint examples in QA-owned docs, so execution coverage is blocked at contract level until engineering exposes deterministic sample responses.
12. T-030 and T-031 now have acceptance coverage, but QA still needs a real branch build to verify drawer/page parity, session-state transitions, and no-fallback selection behavior in the UI.
13. T-032 is still an active blocker area because older docs continue to advertise legacy workflow states that conflict with the canonical PM memo and current Gate A reconciliation work.
14. T-033 through T-058 now have contract-level QA coverage in this file, but execution coverage is still blocked until the reconciled downstream docs actually land and can be diff-verified against the PM memo, API contract, reconciliation matrix, and roadmap/entrypoint docs.
15. Alice's persisted-jobs follow-through tasks T-049, T-052, and T-055 now have QA coverage for canonical workflow fallback, pending-fit handling, and fallback-reason visibility, but QA still needs real API payload fixtures that include missing-fit and legacy-data edge cases before sign-off.
16. Frank's doc-cleanup follow-through tasks T-053, T-054, and T-058 still require the older stories, draft PRDs, PROJECT, clarification docs, and roadmap to be visibly annotated or rewritten before QA can confirm stale wording is no longer implementation-facing.
17. T-057, T-062, and T-066 now cover PRD v2 source references plus QA-owned path and environment notes, but QA still needs surrounding non-QA docs and automation prompts to keep pointing at valid current paths so the shortest implementation-facing checklist stays trustworthy.
18. Jimmy's latest plan keeps the highest-value QA work on T-057, T-062, and T-066, with T-063 through T-068 extending the same recovery cleanup into roadmap, kickoff, handoff, and automation-facing entrypoint docs.
19. The active PM implementation slice remains auth -> session -> Gmail readonly connect -> persisted jobs UI, so QA-owned docs should treat the Next.js web shell plus Go API split as current and older local-Go-app wording as historical unless a task explicitly scopes back to that earlier path.
20. The hourly kickoff prompt still referenced `~/Documents/project-requirements/DEVELOPMENT_PLAN.md`, while the live source file remains `~/Documents/project-requirements/projects/jobtrackr/DEVELOPMENT_PLAN.md`; QA-facing repo docs now explicitly treat that mismatch as external coordination drift and tell engineers how to recover safely.
21. The live development plan now has completed entries again, including T-085, T-086, T-090, T-093, T-094, T-096, T-097, and T-107, so this hour's QA output should keep distinguishing plan-complete documentation work from still-open execution sign-off work like T-095 and T-106.
22. T-063 through T-068 still need diff-level verification in roadmap, README, PROJECT, handoff, and preserved kickoff docs before QA can close the remaining recovery-path drift called out by Jimmy's latest plan.
23. T-070 now covers wording normalization across PRD v2, the QA blocker checklist, and in-progress QA coverage so QA can recover from stale automation prompts without comparing three slightly different recovery notes by hand.
24. T-069, T-071, T-072, and T-073 extend the same recovery coverage into automation examples and preserved handoff docs, so hourly kickoff recovery can be tested against one canonical path order instead of doc-specific variants.
25. T-074, T-075, and T-076 are now represented in QA coverage, but they still need diff-level verification across preserved implementation-facing docs, the shared QA recovery note set, and preserved planning/handoff artifacts before QA can confirm the recovery wording is truly normalized end to end.
26. T-086 and T-107 now track the hourly QA coverage refresh lane so the latest Jimmy fetch success, current external kickoff-path drift, and the newly added T-106 frontend QA surface stay visible in the active QA handoff notes.
27. Jimmy's latest hourly plan keeps Priya on QA recovery alignment plus T-095 sign-off support, while the newest repo-side QA target is T-106 on server-backed workspace behavior and mock fallback visibility.
28. README, PROJECT, the Phase 3 handoff package, preserved engineering handoff v1, and the QA testability review were spot-checked again this hour and remain aligned on project-root recovery order plus current frontend lane wording. Keep this as regression monitoring rather than an active mismatch.
29. Jimmy fetch is no longer a blocker story. If future hourly notes regress to the old 401 narrative, QA should treat that as stale context unless a fresh fetch actually fails again.
30. T-095 now has stronger contract-level QA coverage for provenance persistence, explicit reprocess idempotence, and GOG discovery sufficiency, the repo-side evidence bundle exists at `projects/jobtrackr/specs/jobtrackr-source-email-persistence-evidence-2026-04-22.md`, and the Sam-facing sign-off path is now consolidated in `projects/jobtrackr/specs/jobtrackr-source-email-persistence-signoff-checklist-2026-04-22.md`, but final sign-off still depends on fixture-backed verification.
31. T-106 adds a second live QA lane: once the backend-backed workspace branch is available for execution, QA should verify persisted `/dashboard` and `/jobs` behavior, safe mock fallback when the API is unavailable, and visible pending-fit or workflow-normalization notices without treating that new frontend surface like a reopened blocker for T-095.
32. T-106 now also has one implementation-facing sign-off note at `projects/jobtrackr/specs/jobtrackr-persisted-jobs-workspace-signoff-checklist-2026-04-23.md`, so Sam can run one explicit repo-side verification path instead of stitching together the frontend handoff from multiple kickoff docs.
