# T-099 Source-Email Persistence QA Evidence — 2026-04-22

## Purpose
Attach concrete repo-side evidence for T-095 so Sam can validate the provenance-first Gmail sync slice without reconstructing the implementation from commit history alone.

## Scope
- Confirms the current implementation branch and commit in `~/Documents/jobtrackr`
- Records the local verification commands rerun on 2026-04-22
- Points QA to the specific code and docs that prove source-email persistence and email-to-job linkage behavior

## Implementation under review
- Repo: `~/Documents/jobtrackr`
- Branch: `feat/jobtrackr-source-email-persistence`
- Commit: `da7b8af` (`feat(api): persist source emails during gmail sync`)

## Verification rerun
Executed from `~/Documents/jobtrackr/apps/api` on 2026-04-22 12:52 CDT:

```bash
go test ./...
go build ./...
```

Observed result:
- `go test ./...` passed
- `go build ./...` passed
- package-level evidence included `ok github.com/kthgff/jobtrackr/apps/api/internal/http` and `ok github.com/kthgff/jobtrackr/apps/api/internal/ingest`

## Code evidence
### Provenance-first sync flow
File: `~/Documents/jobtrackr/apps/api/internal/ingest/service.go`

What QA should verify:
- `SaveSourceEmail(...)` runs before parsed-job persistence for each scanned Gmail message
- non-job emails still persist as source-email rows for debug visibility
- job-like emails call `SaveJob(...)` and then `LinkJobToSourceEmail(...)`
- returned sync result keeps the saved job list for follow-up inspection

### Focused regression test
File: `~/Documents/jobtrackr/apps/api/internal/ingest/service_test.go`

Named coverage:
- `TestScanAndPersistPersistsSourceEmailsAndJobLinks`

What the test proves:
- two scanned Gmail messages produce two persisted `source_emails`
- only the job-like message becomes a saved job
- one `job_source_emails` link is written for the saved job
- the saved job retains the originating Gmail message id as provenance

## Documentation evidence
File: `~/Documents/jobtrackr/apps/api/README.md`

Current README language now states that Gmail sync:
- stores every scanned Gmail message once in `source_emails`
- upserts parsed job-like messages into `jobs`
- links saved jobs back to source emails in `job_source_emails`
- keeps non-job emails queryable for debugging

## Suggested QA sign-off path
1. Review `apps/api/internal/ingest/service.go`
2. Review `apps/api/internal/ingest/service_test.go`
3. Re-run `go test ./... && go build ./...` from `apps/api`
4. Confirm `apps/api/README.md` still matches implementation behavior
5. If fixture mailbox access is available, execute the T-095 provenance checks from `stories/jobtrackr-in-progress-test-cases-2026-04-19.md`

## Remaining gap
- Final Sam sign-off still needs fixture-backed database verification for `source_emails` and `job_source_emails` rows during a live or seeded sync run.
- Repo-side evidence is now attached and no longer blocked on missing command output or missing code pointers.
