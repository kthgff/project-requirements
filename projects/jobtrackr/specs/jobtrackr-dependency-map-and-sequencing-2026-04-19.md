# JobTrakr Dependency Map and Milestone Sequencing — 2026-04-19

## Purpose

Lock the smallest parallel-safe execution plan across auth, ingestion, persistence, fit analysis, and table UI, using the PM decision memo as the current source of truth.

## Canonical Inputs

- `specs/jobtrackr-pm-decision-memo-2026-04-19.md`
- `specs/jobtrackr-implementation-milestones-v1.md`
- `specs/jobtrackr-engineering-handoff-v1.md`
- `specs/jobtrackr-data-model-v1.md`
- `specs/jobtrackr-gmail-ingestion-spec-v1.md`
- `specs/jobtrackr-frontend-plan-v1.md`

## Source-of-Truth Correction

Before parallel implementation, engineering should treat the PM decision memo as canonical over older docs where they conflict.

Most important drift already visible in older handoff/model docs:
- workflow status list is stale in older docs (`flagged`, `reviewing`, `skipped`, `interview`) and must be replaced by:
  - `new`
  - `interested`
  - `applied`
  - `interviewing`
  - `offer`
  - `rejected`
- archive is not a workflow status, it is `archivedAt`
- shortlist behavior is `saved: boolean`, not a status
- fit outputs are nullable when analysis has not run yet
- one source email may link to multiple jobs
- parsed-job creation threshold is stricter than earlier exploratory docs
- Gmail scope is readonly only for MVP
- manual job entry is out of MVP scope

## Dependency Map

### 1. Auth foundation

**Provides**
- Google sign-in
- session enforcement
- user identity for all user-scoped records

**Blocks until done**
- protected dashboard shell
- user-specific jobs queries
- Gmail connection state and sync actions
- resume upload

**Can run in parallel with**
- persistence schema design
- dashboard component work using mock or fixture data
- ingestion parser design work that does not require live auth wiring

### 2. Persistence foundation

**Provides**
- canonical schema for `user`, `gmail_connection`, `source_email`, `job`, `job_source_email`, `resume`, `job_fit_analysis`, and `job_status_history`
- canonical enums and nullable fields
- query shape for dashboard and detail views

**Blocks until done**
- real jobs table
- source email persistence
- durable dedupe/upsert behavior
- fit-analysis persistence

**Can run in parallel with**
- auth UI and protected routing
- dashboard shell and table layout with mocks
- Gmail OAuth wiring up to the point of persistence integration

### 3. Gmail connection and raw ingestion

**Provides**
- readonly Gmail connect flow
- initial 30-day sync
- incremental sync trigger contract
- source-email persistence for `job_alert`, `uncertain`, and pipeline failures

**Blocks until done**
- real job population from inbox
- meaningful sync status visibility
- extraction/debug traces against live data

**Can run in parallel with**
- dashboard shell work
- schema work
- jobs list query work if fixture-backed first

### 4. Extraction, dedupe, and job upsert

**Provides**
- parsed-job threshold enforcement
- one-email-to-many-jobs extraction path
- duplicate detection and field merge rules
- canonical `job` rows for dashboard consumption

**Blocks until done**
- real jobs table replacing mocks
- accurate source-to-job drilldown
- trustworthy fit-analysis trigger inputs

**Can run in parallel with**
- auth hardening
- UI table rendering against mocked response contracts
- resume upload UI shell

### 5. Dashboard table and query layer

**Provides**
- authenticated dashboard shell
- real list query with canonical search/filter/sort semantics
- empty/loading/error states

**Depends on**
- auth foundation
- persistence foundation
- at least a stable jobs query contract

**Can start before ingestion completes**
Yes. Build against fixture data first, then switch to persisted query results.

### 6. Resume upload and active resume state

**Provides**
- stored active resume
- triggerable dependency for fit analysis

**Blocks until done**
- production fit-analysis execution

**Can run in parallel with**
- ingestion completion
- dashboard query work

### 7. Fit analysis pipeline

**Provides**
- nullable-to-populated fit fields
- flag/score/summary display data

**Depends on**
- persistence foundation
- resume upload
- canonical job content from ingestion or manual test fixtures

**Does not block**
- first end-to-end MVP slice for login, dashboard shell, Gmail connect, raw source-email ingestion, and real jobs list

## Recommended Smallest End-to-End Slice

Build the first shippable slice in this order:

1. Auth foundation
2. Protected dashboard shell
3. Persistence foundation for user, Gmail connection, source email, and job records
4. Gmail connect flow with readonly scope
5. Manual `POST /sync/run` endpoint returning accepted/running
6. Initial 30-day source-email ingestion with debug visibility
7. Extraction + dedupe into real job rows
8. Dashboard query wired to persisted jobs

This is the smallest slice that proves the product loop without waiting for resume upload or fit analysis.

## Parallel-Safe Work Breakdown for Milestones 1 to 4

### Lane A — Auth and app shell
Own:
- login page
- Google auth callback/session
- protected routing
- base dashboard shell

### Lane B — Persistence and contracts
Own:
- schema migration/model pass
- canonical enums and nullable fields
- jobs list/detail query contracts
- sync run and Gmail connection state contracts

### Lane C — Gmail connect and ingestion pipeline
Own:
- Gmail OAuth scope wiring
- candidate email fetch
- source-email persistence
- extraction threshold checks
- dedupe/upsert

### Lane D — Table UI integration
Own:
- table component
- filter controls
- loading/empty/error states
- switch from fixtures to real jobs query once Lane B contract stabilizes

## Blockers to Parallel Build Safety

### Blocker 1. Schema drift across current docs
Older specs still disagree with the PM memo on statuses, archive semantics, and fit defaults.

**Impact**
Parallel teams will implement incompatible enums and query assumptions.

**Resolution**
Update the API contract and data model immediately before more implementation lands.

### Blocker 2. Missing explicit join model for one source email to many jobs
The PM memo locks one-message-to-many-jobs linkage, but older data-model text still leans on single `source_email_id` on `job`.

**Impact**
Ingestion and detail view work can diverge or require migration churn.

**Resolution**
Add a join table or equivalent linkage model now, with `job_source_email(job_id, source_email_id, linkage_type, created_at)` preferred for MVP durability.

### Blocker 3. Gmail connection state contract is not yet reflected everywhere
The PM memo defines `connected`, `needsReconnect`, `state`, `lastSyncedAt`, `lastErrorCode`, and `lastErrorMessage`, but older docs do not consistently model it.

**Impact**
Auth/connect UI and ingestion worker can drift on reconnect behavior.

**Resolution**
Publish one shared response shape before implementation splits.

### Blocker 4. Search/filter semantics need one canonical query contract
The PM memo now locks AND-across-filters, OR-within-repeated-values, inclusive date bounds, normalized exact-match location, and explicit sort semantics.

**Impact**
Frontend and backend will otherwise each invent behavior.

**Resolution**
Freeze the list endpoint contract before table integration moves off mocks.

### Blocker 5. Fit-analysis work should not leak into Milestones 1 to 4 contracts unnecessarily
Fit is downstream and nullable by default.

**Impact**
Early slices get delayed if fit is treated as required rather than optional.

**Resolution**
Keep `fitFlag`, `fitScore`, and `fitSummary` nullable/optional in early query responses and avoid gating the first vertical slice on fit execution.

## Milestone Sequencing Recommendation

### Milestone 1
Auth foundation only.

### Milestone 2
Dashboard shell plus table UI using fixtures, in parallel with schema and query contract finalization.

### Milestone 3
Persistence foundation and real jobs list query. This milestone should absorb the canonical state-model corrections before more UI coupling happens.

### Milestone 4
Gmail ingestion baseline, but sequence internally as:
1. Gmail connect state contract
2. readonly OAuth wiring
3. source-email persistence
4. extraction threshold logic
5. dedupe/upsert
6. dashboard refresh against real data

## Immediate Recommended Follow-up Tickets

1. **Contract correction pass**
   - update API contract and data model to PM memo semantics
2. **Schema linkage pass**
   - add explicit source-email-to-job join model
3. **Dashboard query contract pass**
   - freeze search/filter/sort request and response shapes
4. **Gmail connection state contract pass**
   - publish connect/reconnect/disconnect/sync response types
5. **Initial ingestion implementation pass**
   - build source-email persistence before full enrichment depth

## Engineering Recommendation

Do not treat Milestones 1 to 4 as fully serial. Treat them as a staged merge of four parallel lanes, but require two contracts to harden first:
- canonical schema/state model
- dashboard query and Gmail connection contracts

Once those are frozen, auth, shell, ingestion, and table integration can move in parallel with materially lower rework risk.
