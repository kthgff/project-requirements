# JobTrackr MVP QA Testability Review and Acceptance Checklist

## Purpose
This document translates the current MVP stories and requirements into a testability review and release checklist for implementation and QA.

## Scope Reviewed
Source documents:
- `projects/jobtrackr/stories/jobtrackr-stories.md`
- `projects/jobtrackr/specs/jobtrackr-requirements.md`
- `projects/jobtrackr/specs/jobtrackr-gmail-ingestion-spec-v1.md`
- `projects/jobtrackr/specs/jobtrackr-fit-analysis-spec-v1.md`
- `projects/jobtrackr/specs/jobtrackr-api-contract.md`

Implementation kickoff redirect:
- `projects/jobtrackr/DEVELOPMENT_PLAN.md`
- `projects/jobtrackr/PROJECT.md`
- `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`

Active implementation slice:
- Google auth -> session -> Gmail readonly connect -> persisted jobs UI
Primary focus areas for MVP QA:
- Gmail connection
- ingestion
- deduplication
- job creation from partial parses
- status editing
- notes and tags
- search
- filters

## QA Assessment Summary
Current specs are directionally strong for MVP, but not yet fully testable end to end.

Main blockers to reliable implementation and QA sign-off:
1. Several core behaviors are described conceptually but lack deterministic pass/fail rules.
2. Status, archive, and saved semantics conflict across documents.
3. Deduplication behavior is not fully specified for ambiguous matches or update behavior.
4. Search and filter semantics are underdefined, especially normalization, combination logic, and empty states.
5. Partial parsing is required, but the minimum field threshold for record creation is still ambiguous.
6. API contract includes manual creation and saved/archive query shapes that are not aligned with unresolved product decisions.

Recommendation:
- Use `specs/jobtrackr-pm-decision-memo-2026-04-19.md` as the current source of truth for the former P0 blockers.
- Use `specs/jobtrackr-qa-blocker-checklist-2026-04-19.md` as the shortest implementation-facing QA blocker reference.
- Do not begin implementation until those decisions are reflected consistently in the ingestion spec, API contract, and development handoff.
- Treat the checklist below as the minimum QA gate for MVP.

---

# 0. P0 Blocker Checklist Mapped to Blocked Test Cases

This checklist converts the former open P0 items into explicit QA blockers. Even though the PM memo now resolves them at the product level, implementation and QA remain blocked anywhere the decision has not yet propagated into the working specs and build.

## 0.1 Canonical status, saved, and archived semantics

Decision source:
- `specs/jobtrackr-pm-decision-memo-2026-04-19.md`

Required canonical behavior:
- `status` is workflow state only
- `saved` is a boolean flag
- archive state is controlled by `archivedAt`
- `archived` is not a workflow status
- views map as follows:
  - inbox = non-archived + `status=new`
  - all jobs = non-archived
  - saved = non-archived + `saved=true`
  - archived = `archivedAt IS NOT NULL`

Blocked test cases if not implemented consistently:
- Verify status-update coverage for each allowed workflow status only
- Verify archived jobs do not appear in inbox or all-jobs views
- Verify saved view returns only `saved=true` and non-archived jobs
- Verify archived view is driven by `archivedAt`, not `status`
- Verify status filters do not accept or return `archived`
- Verify `PATCH /jobs/:id`, `POST /jobs/:id/archive`, and list filters stay consistent after refresh

Blocking reason:
- QA cannot define stable expected datasets for inbox, all jobs, saved, archived, or status-filter behavior if archive is both a status and an attribute in different docs.

Checklist:
- [ ] Ingestion-adjacent specs no longer describe `archived` as a status
- [ ] Stories and acceptance coverage align to the three-part model
- [ ] API list/filter examples use the canonical view semantics
- [ ] QA regression cases for saved/archive/status no longer conflict across documents

## 0.2 Minimum parsed-job creation threshold

Decision source:
- `specs/jobtrackr-pm-decision-memo-2026-04-19.md`

Required canonical behavior:
Create a parsed job only when at least one is true:
- external job ID exists
- normalized job URL exists
- title and company both exist
- title, location, and source platform exist with medium-or-higher confidence

Otherwise:
- persist source email only
- expose extraction outcome for debug review

Blocked test cases if not implemented consistently:
- Verify link-only alerts create a job when URL is valid
- Verify title+company alerts create a job without URL
- Verify low-data emails below threshold do not create junk job rows
- Verify relevant-but-unparseable emails stay visible as source-email-only records
- Verify null title/null company rows are created only when threshold is still satisfied
- Verify multi-job digests can create zero, one, or many jobs based on per-item threshold evaluation

Blocking reason:
- QA cannot distinguish valid partial records from parser junk without a deterministic creation threshold.

Checklist:
- [ ] Ingestion spec uses the same parsed-job threshold as the PM memo and API contract
- [ ] Debug behavior for below-threshold emails is explicitly testable
- [ ] Acceptance checklist names source-email-only outcomes as pass conditions

## 0.3 Deduplication and ambiguous-match behavior

Decision source:
- `specs/jobtrackr-pm-decision-memo-2026-04-19.md`

Required canonical behavior:
- exact external job ID match = duplicate
- exact normalized job URL match = duplicate
- exact normalized company+title+location match = duplicate fallback
- near matches that are not exact do not auto-merge in MVP
- duplicate updates may attach source linkage, update `lastSeenAt`, fill nulls, and replace lower-confidence extracted fields only

Blocked test cases if not implemented consistently:
- Verify tracked jobs are deduped when tracking params differ but normalized URL matches
- Verify same title/company with different normalized locations does not merge
- Verify same title/location with different companies does not merge
- Verify repeated alert updates do not overwrite notes, tags, saved state, archive state, or manual status
- Verify ambiguous near-duplicates create separate jobs rather than silent merges
- Verify one-message-to-many-jobs traceability survives duplicate attachment behavior

Blocking reason:
- QA cannot produce deterministic pass/fail expectations for duplicate prevention, data refresh, or user-trust behavior without exact merge rules.

Checklist:
- [ ] URL normalization and exact-match fallback rules are documented in the implementation-facing spec
- [ ] Duplicate update rules explicitly protect user-edited fields
- [ ] Ambiguous-match behavior is testable via stored metadata or observable outcome

## 0.4 Search and filter semantics

Decision source:
- `specs/jobtrackr-pm-decision-memo-2026-04-19.md`

Required canonical behavior:
- keyword search is case-insensitive substring match
- search scope is only `title`, `company`, and `descriptionSnippet`
- filter families combine with AND logic
- repeated values within a filter family combine with OR logic
- date boundaries are inclusive
- date filtering uses the user-facing timezone against UTC-stored timestamps
- location filtering is case-insensitive exact match on normalized location
- default sort is `dateReceived:desc`

Blocked test cases if not implemented consistently:
- Verify query matches title/company/snippet but not notes or tags
- Verify search with null title/company fields does not crash or mis-rank results
- Verify `status=applied&status=interviewing` returns OR-within-status results
- Verify search + status + tag + date filters return intersection results across families
- Verify `dateFrom` and `dateTo` include same-day boundary records in the user-facing timezone
- Verify location normalization collapses casing differences but not semantically different strings
- Verify clearing search restores the correct base dataset for inbox, all jobs, saved, and archived

Blocking reason:
- QA cannot lock result-set expectations without one canonical definition for query scope, combination logic, and date/location semantics.

Checklist:
- [ ] Search scope is consistent across requirements, QA review, and API examples
- [ ] Date and location filter semantics are explicit enough for fixture-based tests
- [ ] Base dataset semantics for inbox, all jobs, saved, and archived remain consistent after filters clear

---

---

# 1. Testability Review by MVP Flow

## 1.1 Gmail Connection

### What is testable now
- OAuth flow exists conceptually
- Gmail connection state is represented in API contract
- reconnect and disconnect behavior are mentioned

### What is not fully testable yet
- exact scopes are unspecified
- success and failure states for expired, revoked, denied, and partially granted auth are not fully defined
- token storage is described as secure but has no observable acceptance criteria
- callback behavior and post-connect UX destination are not specified

### Minimum testability requirement
Before implementation, define:
- exact Gmail scopes
- expected UI states for connected, disconnected, expired, reconnect-required, and auth-denied
- what user-visible message appears for each failure mode

## 1.2 Ingestion

### What is testable now
- initial sync exists
- incremental sync exists
- candidate filtering/classification pipeline exists
- logs and sync runs are exposed conceptually

### What is not fully testable yet
- poll interval is a recommendation, not a requirement
- historical sync window default is not final
- candidate storage policy is inconsistent across docs
- retry and replay behavior is mentioned but not exposed in API contract
- no required SLAs or completion expectations for sync feedback

### Minimum testability requirement
Before implementation, define:
- default initial sync window
- default incremental polling strategy for MVP
- which candidate emails are persisted
- whether on-demand sync is required in MVP UI
- what run statuses are valid and what each means

## 1.3 Deduplication

### What is testable now
- matching hierarchy exists
- preference to avoid false merges is stated

### What is not fully testable yet
- exact normalization rules for URLs are missing
- exact threshold for high-confidence company + title + location match is missing
- field refresh rules on duplicate update are optional and vague
- UI and API behavior for possible duplicates is unspecified

### Minimum testability requirement
Before implementation, define:
- normalization rules for URL comparison
- exact merge rules for company/title/location fallback matching
- whether duplicate hits update existing records silently or surface activity/events
- expected behavior when ambiguity exists

## 1.4 Job Creation from Partial Parses

### What is testable now
- partial records are allowed
- source traceability is required
- fallback behavior when enrichment fails is described

### What is not fully testable yet
- minimum identifying data required to create a job is not consistent across docs
- single email can yield zero, one, or many jobs, but expected user experience is undefined
- handling of jobs with null title and null company is not defined for UI list rendering and search

### Minimum testability requirement
Before implementation, define:
- the minimum field threshold for job creation
- whether valid job URL alone is sufficient
- whether company or title is required for displayable job rows
- how partial jobs appear in list and detail views

## 1.5 Status Editing

### What is testable now
- status enum exists
- PATCH endpoint exists
- list filtering by status exists

### What is not fully testable yet
- archived appears both as a status and a separate archival attribute
- saved appears as a separate concept but is not defined in requirements decisions
- immediate persistence has no UI/API timing or optimistic update expectation

### Minimum testability requirement
Before implementation, define:
- canonical status model
- whether archived is only a status, only an attribute, or both
- whether saved is a flag, a view, or both
- allowed transitions if any

## 1.6 Notes and Tags

### What is testable now
- add/edit notes is defined
- add/remove tags is defined
- tags are filterable
- API shapes exist

### What is not fully testable yet
- note length, empty note handling, and sanitization are unspecified
- tag normalization rules are unspecified
- duplicate tag handling is only partly defined
- ordering and casing behavior for tags are unspecified

### Minimum testability requirement
Before implementation, define:
- max note length and empty-string behavior
- tag casing and normalization rules
- whether tag names are unique case-insensitively per user
- how special characters and whitespace are handled

## 1.7 Search

### What is testable now
- search fields are named
- query parameter exists

### What is not fully testable yet
- tokenization, case sensitivity, substring behavior, and punctuation handling are unspecified
- search result ordering when `sort` is absent or query is present is unspecified
- behavior for null fields in partial jobs is unspecified

### Minimum testability requirement
Before implementation, define:
- case sensitivity behavior
- substring vs token match behavior
- whether search covers notes, tags, recruiter fields, or source email metadata
- sort behavior for search results

## 1.8 Filters

### What is testable now
- status, date received, location, and tags are included
- combined filters are required

### What is not fully testable yet
- inclusive vs exclusive date boundaries are unspecified
- timezone source for date filtering is unspecified
- location matching rules are unspecified
- interaction of saved/archive/status filters is unresolved
- empty result and no-filter defaults are unspecified

### Minimum testability requirement
Before implementation, define:
- date filter boundary rules
- user timezone vs UTC behavior
- exact location matching behavior
- whether filters are ANDed together by default
- default dataset shown for inbox, all jobs, saved, and archived views

---

# 2. MVP Acceptance Checklist

Use this as a release gate. Each item should be marked Pass, Fail, or Blocked during QA.

## 2.1 Gmail Connection Checklist
- [ ] User can start Google OAuth from the app
- [ ] User can grant required Gmail permissions successfully
- [ ] Successful connection returns user to the expected app state
- [ ] Connected state is visible in session/account UI
- [ ] Gmail account endpoint reflects connected state accurately
- [ ] Disconnect removes Gmail access without deleting imported jobs
- [ ] Expired or revoked auth is detected and exposed as reconnect required
- [ ] Auth denial or callback failure surfaces a clear user-facing error
- [ ] Tokens are not exposed to the client in API responses

## 2.2 Ingestion Checklist
- [ ] Initial sync runs against the defined historical window
- [ ] Incremental sync processes only new candidate messages after initial sync
- [ ] Candidate filtering excludes clearly irrelevant emails according to spec
- [ ] Relevant candidate emails create source_email records
- [ ] Relevant emails with extractable jobs create or update job records
- [ ] Relevant emails with no extractable jobs are retained for debugging when required
- [ ] A failed email does not stop the rest of the sync run
- [ ] Sync runs expose accurate counts and statuses
- [ ] Failures are visible in logs or sync run detail
- [ ] Reprocessing the same message set is idempotent at the job level

## 2.3 Deduplication Checklist
- [ ] Duplicate detection uses external job ID when available
- [ ] Duplicate detection uses normalized job URL when external ID is absent
- [ ] Fallback duplicate logic for company + title + location behaves per spec
- [ ] Repeated alerts for the same job do not create duplicate active rows in high-confidence cases
- [ ] Duplicate updates refresh only approved fields
- [ ] `last_seen_at` updates on repeated sightings when required
- [ ] Ambiguous matches do not silently merge distinct jobs
- [ ] Duplicate handling is observable in stored metadata or activity logs

## 2.4 Partial Parse Job Creation Checklist
- [ ] Job record is created when required minimum identifying data is present
- [ ] Missing optional fields are stored as null, not invalid placeholders
- [ ] Source email reference is retained for every created parsed job
- [ ] Partial jobs remain editable in the UI
- [ ] Partial jobs remain searchable and filterable within supported fields
- [ ] Multi-job digest emails can create more than one job when applicable
- [ ] Low-confidence extraction without sufficient identifying data stores source email only

## 2.5 Status Editing Checklist
- [ ] User can update a job to each supported status value
- [ ] Status changes persist on refresh
- [ ] Invalid status values are rejected with a validation error
- [ ] Status changes update list and detail views consistently
- [ ] Status filter results update correctly after editing
- [ ] Archived behavior matches the canonical archived model
- [ ] Saved behavior matches the canonical saved model

## 2.6 Notes and Tags Checklist
- [ ] User can add notes to a job
- [ ] User can edit existing notes
- [ ] Notes persist on refresh
- [ ] User can add one or more tags to a job
- [ ] User can remove tags from a job
- [ ] Duplicate tags are prevented or normalized according to spec
- [ ] Tags appear consistently in list, detail, and filter metadata
- [ ] Tag-based filtering returns the expected jobs

## 2.7 Search Checklist
- [ ] Search matches title
- [ ] Search matches company
- [ ] Search matches description content or snippet
- [ ] Search handles partial jobs with null fields without error
- [ ] Search returns stable, deterministic result ordering
- [ ] Search clears correctly and restores the expected base dataset
- [ ] No-results state is handled cleanly

## 2.8 Filter Checklist
- [ ] Status filter returns only matching statuses
- [ ] Date received filter applies correct boundary logic
- [ ] Location filter applies the specified matching logic
- [ ] Tag filter returns correct jobs
- [ ] Combined filters return intersection results
- [ ] Filters interact correctly with search
- [ ] Filters can be cleared cleanly
- [ ] Inbox, All Jobs, Saved, and Archived views use the correct underlying query semantics

---

# 3. High-Priority QA Scenarios

## Scenario Set A: Gmail Connection
1. Connect Gmail successfully on first attempt.
2. Deny Gmail permissions during OAuth.
3. Connect successfully, then revoke Gmail access externally.
4. Disconnect Gmail from within the app.
5. Load app with expired token and confirm reconnect handling.

## Scenario Set B: Ingestion and Classification
1. Ingest a clearly relevant LinkedIn alert.
2. Ingest a clearly relevant recruiter outreach email.
3. Ingest a clearly irrelevant newsletter.
4. Ingest an uncertain email with job link present.
5. Ingest an uncertain email with no job link.
6. Run initial sync, then incremental sync with overlapping message set.

## Scenario Set C: Partial Parse and Multi-Job Extraction
1. Single-job email with all expected fields present.
2. Single-job email with only title plus link.
3. Single-job email with company missing.
4. Digest email containing multiple distinct jobs.
5. Relevant email with no extractable job entity.

## Scenario Set D: Deduplication
1. Same external job ID from repeated alerts.
2. Same normalized job URL with different tracking parameters.
3. Same title/company but different locations.
4. Same title/location but different companies.
5. Ambiguous near-duplicate that should not auto-merge.

## Scenario Set E: Editing and Organization
1. Edit parsed title and company on a partial job.
2. Change status repeatedly across allowed statuses.
3. Add notes, edit notes, and refresh.
4. Add tags with mixed casing and spacing.
5. Remove one tag from a multi-tag job.

## Scenario Set F: Search and Filters
1. Search by title keyword.
2. Search by company keyword.
3. Filter by one status.
4. Filter by date range.
5. Filter by location.
6. Combine search + status + tag + date.
7. Confirm archived and saved views match final product semantics.

---

# 4. QA Sign-Off Recommendation

## Implementation readiness
Status: **Not ready for full implementation sign-off yet**

## Reason
The MVP direction is clear, but several core workflows still lack deterministic behavioral definitions required for engineering consistency and QA pass/fail validation.

## Minimum release-prep requirement
Resolve all P0 clarification items in `jobtrackr-spec-clarifications-needed.md` before implementation begins.
