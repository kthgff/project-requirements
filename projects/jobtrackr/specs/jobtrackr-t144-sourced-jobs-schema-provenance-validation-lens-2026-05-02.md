# T-144 Sourced Jobs Schema and Provenance QA Validation Lens

## Purpose
Give QA one implementation-facing checklist for validating T-144 without reconstructing schema intent from commits, migration diffs, or scattered sourcing specs.

T-144 is supporting sourcing-spine coverage. It does **not** replace the live QA gate pair:
- T-106: server-backed dashboard and jobs workspace sign-off
- T-095: source-email persistence fixture-backed sign-off

## Source-of-truth inputs
- `projects/jobtrackr/DEVELOPMENT_PLAN.md` — T-144 row and current QA state
- `projects/jobtrackr/specs/jobtrackr-job-source-schema-and-provenance-spec-2026-05-02.md` — canonical schema/provenance intent
- `projects/jobtrackr/specs/jobtrackr-job-source-service-ticket-breakdown-2026-05-02.md` — sourcing execution spine and follow-through tickets
- `projects/jobtrackr/stories/jobtrackr-in-progress-test-cases-2026-04-19.md` — active QA coverage, especially TC-2179 through TC-2182

## Validation scope
Use this lens to validate that T-144 created the shared sourced-job foundation only:
- canonical `jobs` records remain the product-facing job model
- `job_sources` records carry source provenance for Email, Indeed, and LinkedIn origins
- `job_source_runs` records provide lightweight operational traceability
- `search_profiles` records support future scheduled discovery metadata
- Gmail ingestion remains compatible with the shared provenance model

Out of scope for this lens:
- completing T-106 or T-095 QA sign-off
- validating T-145 CRUD behavior beyond schema compatibility
- validating T-146 runtime scheduling behavior beyond run-table compatibility
- validating live Indeed or LinkedIn scraping

## Migration and schema inspection checklist
- [ ] Migration creates or extends `jobs` without splitting scraped jobs into a separate canonical table.
- [ ] `jobs` keeps canonical workflow status values only: `new`, `interested`, `applied`, `interviewing`, `offer`, `rejected`.
- [ ] Archive remains represented outside workflow status, such as `archived_at`.
- [ ] Fit remains nullable and signal-oriented; low-fit, strong-fit, fit-ready, and pending-fit do not become workflow states.
- [ ] `job_sources` can attach multiple provenance rows to one canonical job.
- [ ] `job_sources` captures source platform identity deterministically for Gmail, Indeed, and LinkedIn origins.
- [ ] `job_sources` supports source identifiers and URLs needed for dedupe/debug without storing unnecessary raw scraped snapshots.
- [ ] `job_source_runs` stores enough lifecycle metadata for QA to inspect run start/end/status and basic counts.
- [ ] `search_profiles` stores user/profile discovery metadata without owning job workflow state.

## Fixture-backed validation checklist
Use seeded or local fixture data; do not require live scraping.

### Canonical model assertions
- [ ] Insert or inspect a Gmail-origin job and a sourced-platform-origin job that both resolve to the same opportunity.
- [ ] Confirm both origins attach to one canonical `jobs` row instead of creating parallel product records.
- [ ] Confirm `source_count`, `primary_source`, `first_seen_at`, `last_seen_at`, and `last_sourced_at` behavior matches the provenance spec when present.
- [ ] Confirm user-managed fields such as notes, tags, `saved`, `archived_at`, and manual `status` are not overwritten by provenance attachment.

### Provenance assertions
- [ ] A Gmail-origin row can be represented through the shared provenance shape.
- [ ] An Indeed-origin row can be represented through the shared provenance shape.
- [ ] A LinkedIn-origin row can be represented through the shared provenance shape.
- [ ] Duplicate source origins update or attach provenance instead of creating duplicate workflow rows.
- [ ] Missing, archived, or error source statuses remain provenance-layer facts and do not mutate workflow status by themselves.

### Run/profile traceability assertions
- [ ] A search profile can be linked to sourced provenance without changing canonical job workflow state.
- [ ] A source run can be inspected through `job_source_runs` with status, timestamps, and basic count fields.
- [ ] Run metadata is sufficient to diagnose daily sourcing execution at a high level.
- [ ] Run metadata does not imply raw page snapshot storage is required for MVP.

## Gmail compatibility checks
- [ ] Existing T-095 expectations still hold: source emails are persisted before or alongside parsed job creation.
- [ ] Email-to-job provenance remains queryable after the T-144 schema expansion.
- [ ] Gmail-origin jobs can coexist with scraped-origin provenance on the same canonical job.
- [ ] T-108 source-email debug reads remain useful for inspecting Gmail provenance and are not superseded by sourcing tables.

## QA evidence to capture before moving T-144 out of QA
- migration or schema inspection output for `jobs`, `job_sources`, `job_source_runs`, and `search_profiles`
- fixture or seeded data showing one canonical job with multiple provenance rows
- fixture or seeded data showing Gmail provenance compatibility after the sourced-job schema expansion
- test command output from the API project, if rerun during QA
- any mismatch between implementation fields and `jobtrackr-job-source-schema-and-provenance-spec-2026-05-02.md`

## Remaining gap
This document makes the T-144 validation path explicit, but it is not executed QA evidence. T-144 should stay in QA until Sam or a QA pass records concrete migration/fixture inspection results against the implemented API branch.
