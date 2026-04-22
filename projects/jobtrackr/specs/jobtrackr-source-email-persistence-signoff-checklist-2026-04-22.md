# T-102 T-095 Source-Email Persistence Sign-off Checklist — 2026-04-22

## Purpose
Give Sam one implementation-facing checklist for final QA validation of T-095 without reconstructing the current proof from plan notes, blocker summaries, and the evidence bundle.

## Use this with
- Evidence bundle: `projects/jobtrackr/specs/jobtrackr-source-email-persistence-evidence-2026-04-22.md`
- Active QA coverage: `projects/jobtrackr/stories/jobtrackr-in-progress-test-cases-2026-04-19.md`
- Live plan: `projects/jobtrackr/DEVELOPMENT_PLAN.md`

## Task under sign-off
- Task: `T-095`
- Current implementation branch: `feat/jobtrackr-source-email-persistence`
- Current evidence commit: `da7b8af` (`feat(api): persist source emails during gmail sync`)

## Preconditions
- Repo available at `~/Documents/jobtrackr`
- API workspace available at `~/Documents/jobtrackr/apps/api`
- A fixture mailbox, seeded sync fixture, or equivalent deterministic test source is available for one new job-like Gmail message and one duplicate alert

## Fast repo-side verification
Run from `~/Documents/jobtrackr/apps/api`:

```bash
go test ./...
go build ./...
```

Expected:
- `go test ./...` passes
- `go build ./...` passes
- No new compile or ingest regressions appear before fixture-backed QA starts

## Fixture-backed sign-off steps
1. Run the Gmail sync flow against a deterministic fixture or seeded mailbox containing:
   - one new job-alert email
   - one duplicate alert for an already-seen or mergeable job
2. Inspect persisted records in:
   - `source_emails`
   - `job_source_emails`
   - `jobs`
3. Re-run the same sync flow against the same fixture source.
4. If the implementation exposes an explicit reprocess path, replay one already-persisted source email and inspect persistence results again.

## Required assertions
### A. Provenance-first persistence
- Each scanned Gmail message is persisted once in `source_emails`
- Source-email persistence happens before or alongside parsed job upsert, never after a successful job save with missing provenance
- Non-job or below-threshold job-like mail remains queryable as `source_emails` for debug review when applicable

### B. Job linkage
- Parsed jobs created from persisted emails get linkage rows in `job_source_emails`
- A merged or deduplicated job still preserves source-email provenance instead of replacing it with only the newest message
- Re-running sync does not create duplicate linkage rows for the same message-to-job relationship when nothing materially changed

### C. Idempotence
- Re-running sync does not create duplicate `source_emails` rows for the same Gmail message id
- Re-running sync does not create duplicate jobs when normalized job identity is unchanged
- If reprocess is available, replaying a persisted source email keeps the existing job linkage intact and only updates processing metadata as expected

## Suggested SQL / inspection targets
Use whatever local inspection path is already standard for the environment. At minimum, confirm:
- one row per Gmail message id in `source_emails`
- at least one corresponding row in `job_source_emails` for each saved job created from a persisted source email
- deduped jobs still retain one-or-more provenance links after repeat sync

## Code pointers
- Sync implementation: `~/Documents/jobtrackr/apps/api/internal/ingest/service.go`
- Regression test: `~/Documents/jobtrackr/apps/api/internal/ingest/service_test.go`
- Current API behavior note: `~/Documents/jobtrackr/apps/api/README.md`

## Sign-off rule
Move T-095 from QA to Completed only when all of the following are true:
- repo-side evidence bundle is still current
- fast repo-side verification passes
- fixture-backed persistence checks pass for `source_emails` and `job_source_emails`
- repeat sync stays idempotent for source-email persistence and job linkage

## Remaining blocker if sign-off fails
If any fixture-backed check fails, keep T-095 in QA and report the exact failing assertion, fixture used, and affected persistence table rather than reverting to a generic "needs more QA" note.
