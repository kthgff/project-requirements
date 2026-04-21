# JobTrackr Spec Clarifications Needed Before Implementation

## Purpose
This document identifies requirement gaps, ambiguities, and untestable areas across the current JobTrackr specs and proposes the minimum clarifications needed before implementation starts.

## Documents Reviewed
- `specs/jobtrackr-gmail-ingestion-spec-v1.md`
- `specs/jobtrackr-fit-analysis-spec-v1.md`
- `projects/jobtrackr/api-contract.md`
- Cross-checks against `projects/jobtrackr/stories.md` and `projects/jobtrackr/requirements.md`

## Executive Summary
Current specs are sufficient for product direction, but not yet sufficient for low-risk implementation. The biggest issues are conflicting data semantics, underdefined edge-case behavior, and several places where APIs are drafted before product decisions are finalized.

Recommendation:
- The P0 product decisions are now answered in `specs/jobtrackr-pm-decision-memo-2026-04-19.md`.
- Engineering should treat the PM memo plus `projects/jobtrackr/api-contract.md` as the canonical current direction.
- Remaining risk is now spec drift: the ingestion spec and QA expectations must be updated so implementation is not driven by stale language.
- Resolve P1 items before UI and API contracts are declared stable.
- P2 items can follow shortly after implementation starts if needed, but earlier is better.

---

# API Contract vs Ingestion Spec Mismatch Review

This section flags the current contract mismatches that would make QA expectations unstable if engineering follows `projects/jobtrackr/api-contract.md` while reviewers or implementers still rely on `specs/jobtrackr-gmail-ingestion-spec-v1.md`.

## Quick mismatch matrix

| ID | Area | Current state | QA risk |
|---|---|---|---|
| M1 | P0 decision drift | Contract reflects PM memo, older docs may still be read as unresolved | Different expected behavior by source doc |
| M2 | Parsed-job threshold | Contract is deterministic, older language is looser | Unstable partial-job expectations |
| M3 | Candidate retention | Contract says persist relevant/uncertain/pipeline-failed only | Debug counts and retained-message expectations drift |
| M4 | Uncertain-email handling | Contract is more deterministic than older wording | Uncertain-message outcomes vary in tests |
| M5 | Incremental sync cadence | Contract fixes 10-minute default | Scheduling and stale-state tests drift |
| M6 | Gmail scope | Contract fixes exact scopes | OAuth consent and reconnect tests drift |
| M7 | One-message-to-many-jobs traceability | Contract exposes linked job collections | Digest and duplicate traceability tests drift |
| M8 | Manual job creation | MVP contract removes `POST /jobs` | Legacy QA may still test manual create flow |
| M9 | Archived handling | Contract uses `archivedAt` only | Archive and status-filter tests drift |
| M10 | Saved handling | Contract uses boolean `saved` flag | Saved-view semantics drift |
| M11 | Sync run statuses | Contract defines canonical enum | Sync run UI/API assertions drift |

## M1. Source-of-truth drift on P0 decisions
### Current state
- `projects/jobtrackr/api-contract.md` says it incorporates `specs/jobtrackr-pm-decision-memo-2026-04-19.md`.
- `specs/jobtrackr-gmail-ingestion-spec-v1.md` still presents several of those same behaviors as open questions or PM recommendations.

### QA risk
A tester can derive different expected behavior depending on which document they read first.

### Action needed
Update the ingestion spec so these are stated as final MVP requirements, not unresolved recommendations:
- Gmail scope
- initial sync window
- incremental sync cadence
- uncertain-email handling
- parsed-job creation threshold
- duplicate-match rules
- candidate/source-email retention policy

## M2. Parsed-job creation threshold is final in the contract, ambiguous in the ingestion spec
### Contract
`projects/jobtrackr/api-contract.md` defines a hard parsed-job creation threshold in Validation Rules.

### Ingestion spec
`specs/jobtrackr-gmail-ingestion-spec-v1.md` still says partial jobs are allowed when there is a valid URL or enough identifying data, but does not define the threshold in the main extraction section.

### QA risk
Unstable expectation for whether low-data emails should create jobs or remain source-email-only.

### Exact tests destabilized
- URL-only parsed job creation
- title+company creation without URL
- below-threshold source-email-only handling
- multi-job digest extraction with mixed-confidence items

## M3. Candidate retention policy conflicts
### Contract / PM memo
- Persist `source_email` for `job_alert`, `uncertain`, and pipeline failures.
- Do not persist clearly irrelevant mail by default.

### Ingestion spec
- says to store `source_email` for every email processed as candidate input
- later says irrelevant email storage is optional
- later gives a PM recommendation to store relevant and uncertain candidates, not the whole inbox

### QA risk
Run counts and debug-message expectations will vary depending on which retention rule engineering implements.

### Exact tests destabilized
- source-email persistence counts after sync
- visibility of irrelevant messages in debug views
- replay and failure-trace expectations for candidate classes

## M4. Uncertain-email behavior is still too loose in the ingestion spec
### Contract / PM memo direction
- uncertain emails are persisted
- PM memo recommends debug visibility for uncertain emails in MVP
- practical extraction behavior needs to be deterministic for QA

### Ingestion spec
- says `uncertain` should either skip or queue for lower-confidence extraction, depending on engineering simplicity
- also includes an open question on whether uncertain emails should be retained for manual review later

### QA risk
QA cannot know whether an uncertain email with a link should create a source-email-only record, a parsed job, or no persisted record.

### Exact tests destabilized
- uncertain email with job link
- uncertain email without job link
- debug review visibility for uncertain messages

## M5. Incremental sync cadence differs between final decision and ingestion recommendation
### Contract / PM memo
- incremental sync cadence defaults to every 10 minutes

### Ingestion spec
- says PM recommendation is every 5 to 15 minutes unless push is easy and reliable

### QA risk
Scheduled-sync expectations and operational acceptance tests will drift.

### Exact tests destabilized
- background ingestion cadence checks
- duplicate protection under overlapping incremental windows
- stale-state timing expectations in Gmail account and sync UI

## M6. Gmail scope is finalized in the contract but still open in the ingestion spec
### Contract / PM memo
- final MVP scope includes `openid`, `email`, `profile`, and `https://www.googleapis.com/auth/gmail.readonly`

### Ingestion spec
- still says to request the minimum practical read scopes and leaves exact scope as an open question

### QA risk
OAuth consent expectations and denied-scope failure behavior are not stable if reviewers treat scope as unsettled.

### Exact tests destabilized
- consent screen validation
- denied-permission handling
- granted-scope inspection via account endpoint

## M7. Source message to job linkage is more precise in the contract than in older mental models
### Contract / PM memo
- a single source email may link to many jobs
- debug endpoints expose `linkedJobIds` / `linkedJobs`
- job detail exposes `sourceMessages`

### Ingestion spec
- says one email may contain one or more jobs, but does not define an explicit observable linkage model for QA

### QA risk
Traceability is conceptually supported but not testable without consistent response-level expectations.

### Exact tests destabilized
- one-message-to-many-jobs digest validation
- duplicate attachment vs new-job creation traceability
- source-message audit trail checks from job detail and debug endpoints

## M8. POST /jobs mismatch has been resolved in practice but should stay explicit in QA docs
### Contract
- manual job creation endpoint is no longer present

### Ingestion-adjacent docs
- prior review documents still call out `POST /jobs` as a mismatch risk and manual entry as unresolved

### QA risk
Legacy QA notes may continue to test or expect a manual create path that is now out of MVP scope.

### Exact tests destabilized
- manual job creation coverage
- validation expectations for manually created rows vs parsed rows

## M9. Archived handling is canonical in the contract, but older docs still describe the conflict
### Contract / PM memo
- archive is represented by `archivedAt`
- `archived` is not a workflow status

### Ingestion / QA-adjacent docs
- older sections still frame this as unresolved or conflicting

### QA risk
Archive tests may still be written against an outdated status-based model.

### Exact tests destabilized
- archived list semantics
- status filter semantics
- archive/unarchive endpoint persistence behavior

## M10. Saved handling is canonical in the contract, but older docs still describe it as unresolved
### Contract / PM memo
- `saved` is a boolean flag and top-level saved view is `saved=true` plus non-archived

### QA-adjacent docs
- older sections still describe saved as potentially a state, flag, or virtual view

### QA risk
Saved-view and saved-filter expectations remain unstable unless the QA docs are updated to the canonical model.

### Exact tests destabilized
- saved filter correctness
- saved view dataset membership
- interactions between saved and archived

## M11. Sync run statuses are finalized in the contract, not yet anchored in the ingestion spec
### Contract / PM memo
- canonical statuses: `queued`, `running`, `completed`, `partial`, `failed`, `canceled`

### Ingestion spec
- expects graceful failure and replay support but does not define the run-status vocabulary

### QA risk
Operational UI and API assertions for sync visibility are not fully grounded in a shared source.

### Exact tests destabilized
- sync run status transitions
- partial-failure reporting
- failure-summary visibility in run detail

---

---

# P0. Must Clarify Before Implementation

## P0-1. Saved vs Archived vs Status Model
### Problem
The requirements explicitly list these unresolved decisions:
- whether saved is a distinct state or a saved flag
- whether archived is a status or a separate attribute

The API contract currently treats:
- `status` as an enum
- `saved` as a boolean
- `archivedAt` as a separate field
- `/jobs/:id/archive` and `/jobs/:id/unarchive` as separate endpoints

Stories also define `Archived` as a supported status.

### Why this blocks implementation
Engineering cannot build a stable schema, API, or UI query model while these semantics conflict.
QA cannot define expected view/filter behavior for inbox, all jobs, saved, and archived.

### Minimum clarification needed
Decide one canonical model:
1. Is `archived` a status value, a timestamped attribute, or both?
2. Is `saved` a boolean flag, a virtual view, or a status?
3. What exact dataset defines each top-level view: inbox, all jobs, saved, archived?

### Recommended clarification
- Keep workflow `status` for the pipeline states only.
- Keep `saved` as a boolean flag.
- Keep archive as a separate attribute with `archivedAt`.
- Remove `Archived` from the workflow status enum if archive remains a separate attribute.

## P0-2. Minimum Threshold for Creating a Parsed Job
### Problem
The ingestion spec says create a partial job if there is a valid job URL or enough identifying data, but does not define enough identifying data.
The API contract says parsed jobs may have nullable title and company, while manual jobs need at least one of them.

### Why this blocks implementation
Without a threshold, engineering may create too many low-value junk rows or reject valuable partial jobs. QA cannot verify correct behavior.

### Minimum clarification needed
Define the minimum required identifying data for parsed-job creation.

### Recommended clarification
For MVP, create a parsed job when any one of these is true:
- external job ID exists, or
- normalized job URL exists, or
- title + company both exist, or
- title + location + source platform exist with medium or higher extraction confidence

Otherwise, store only the source email.

## P0-3. Deduplication Rules and Ambiguous Match Handling
### Problem
The ingestion spec gives a prioritized signal list but not deterministic merge thresholds or field update rules.
It also says ambiguous matches should prefer new record creation with possible duplicate marker, but no data model or API surface is defined for that marker.

### Why this blocks implementation
Deduplication is core MVP trust behavior. Engineering will otherwise make ad hoc merge logic and QA will not have deterministic expected outcomes.

### Minimum clarification needed
Define:
1. URL normalization rules.
2. Exact fallback match conditions for company + title + location.
3. Whether ambiguous matches create a new job with internal duplicate metadata.
4. Which fields may be refreshed on duplicate updates.

### Recommended clarification
- Treat exact external job ID as definitive duplicate.
- Treat normalized URL match as definitive duplicate.
- Treat company + title + location as duplicate only after normalization and exact equality.
- If not exact after normalization, do not auto-merge in MVP.
- On duplicate update, refresh source linkage and `last_seen_at`, and only fill null or lower-confidence fields unless explicitly overridden.

## P0-4. Gmail Scope and Connection-State Behavior
### Problem
The ingestion spec asks what exact Gmail scope should be requested. API contract exposes connection state but does not define failure-state semantics.

### Why this blocks implementation
OAuth integration cannot be finalized without scopes. UI and QA cannot validate reconnect behavior without canonical states.

### Minimum clarification needed
Define:
1. Gmail scopes for MVP.
2. State model for connected, disconnected, expired, revoked, denied, and reconnect-required.
3. User-visible behavior for each state.

### Recommended clarification
- Request the minimum Gmail read-only scope required for inbox scanning.
- Expose `connected`, `needsReconnect`, and last successful sync time.
- Standardize user-facing error states for auth denied, token expired, and revoked access.

## P0-5. Initial Sync and Incremental Sync Defaults
### Problem
The ingestion spec contains PM recommendations, not final requirements, for:
- initial sync window, and
- incremental interval

### Why this blocks implementation
Engineering must choose concrete values for scheduling and sync tests. QA needs exact expected behavior.

### Minimum clarification needed
Define:
1. default historical sync window
2. whether the window is user-configurable in MVP
3. sync cadence for incremental ingestion
4. whether on-demand sync is supported in MVP UI

### Recommended clarification
- Default initial sync to last 30 days.
- Not user-configurable in MVP.
- Poll every 10 minutes for MVP.
- Support on-demand sync from UI only if it is already cheap to build; otherwise keep endpoint internal/admin.

## P0-6. Search and Filter Semantics
### Problem
Requirements say search title/company/description and combine filters, but semantics are not defined for casing, tokenization, dates, locations, or compound logic.

### Why this blocks implementation
Frontend and backend can easily diverge. QA cannot produce deterministic expected result sets.

### Minimum clarification needed
Define:
1. case sensitivity
2. substring vs token matching
3. whether notes/tags are included in keyword search
4. inclusive/exclusive date bounds
5. timezone basis for date filtering
6. exact location matching behavior
7. whether compound filters are ANDed or ORed

### Recommended clarification
- Make keyword search case-insensitive.
- Match by simple substring in MVP.
- Restrict MVP keyword search to title, company, and descriptionSnippet only.
- Use inclusive date boundaries.
- Apply date filtering in the user-facing timezone, stored in UTC.
- Use case-insensitive exact-match location filtering in MVP.
- Combine filters with AND logic.

---

# P1. Should Clarify Before Stable API/UI Build

## P1-1. Candidate Email Retention Policy
### Problem
One section says store source email for every candidate processed. Another recommends storing relevant and uncertain candidates, not the whole inbox. Irrelevant storage is marked optional.

### Risk
Inconsistent retention will affect debugging, storage, privacy posture, and sync counts.

### Minimum clarification needed
Define exactly which message classes are persisted for MVP.

### Recommended clarification
Persist `source_email` only for:
- `job_alert`
- `uncertain`
- any candidate that failed after entering the processing pipeline
Do not persist clearly irrelevant mail in MVP.

## P1-2. Sync Run Status Vocabulary
### Problem
API contract shows statuses like `running`, `completed`, and `partial`, but no canonical enum or meaning is defined.

### Risk
UI and operational reporting will drift.

### Minimum clarification needed
Define the allowed run statuses and transition conditions.

### Recommended clarification
Use:
- `queued`
- `running`
- `completed`
- `partial`
- `failed`
- `canceled`

## P1-3. Relevant Recruiter Outreach Scope
### Problem
Requirements say the system should support recruiter outreach patterns, but the ingestion spec still asks whether recruiter outreach is first-class in MVP.

### Risk
Classification accuracy and test corpus scope will drift.

### Minimum clarification needed
State clearly whether recruiter outreach emails are in MVP scope.

### Recommended clarification
Yes, recruiter outreach that clearly references an open role is in MVP scope.

## P1-4. Enrichment Source of Truth
### Problem
The ingestion spec asks what source is authoritative when email data conflicts with linked page data.

### Risk
Engineering may overwrite trustworthy fields incorrectly.

### Minimum clarification needed
Define source precedence per field.

### Recommended clarification
For MVP:
- external job page is authoritative for job description and structured job metadata when fetch succeeds
- source email remains authoritative for received date and source-message linkage
- if conflict exists for title/company/location, keep enriched value plus preserve original extracted values in source metadata for traceability

## P1-5. Material Update Trigger for Reanalysis
### Problem
Fit analysis should rerun on material updates, but material is not defined.

### Risk
System may rerun too often or miss important refreshes.

### Minimum clarification needed
Define which job field changes trigger fit reanalysis.

### Recommended clarification
Trigger reanalysis when any of these change:
- title
- company
- location
- work mode
- employment type
- salary
- description or requirements text
- seniority
- active resume
Do not rerun for notes, tags, saved, archive, or status changes.

## P1-6. Fit Signal Nullability Semantics
### Problem
The fit-analysis spec recommends nullable score and summary when no resume exists, but also says fit-signal metadata may be false or null depending on implementation preference.

### Risk
This will create avoidable UI ambiguity and inconsistent filtering.

### Minimum clarification needed
Choose one canonical nullability model.

### Recommended clarification
Use:
- `fit_flag = null` when no analysis exists
- `fit_score = null` when no analysis exists
- `fit_summary = null` when no analysis exists
This cleanly distinguishes not analyzed from analyzed-and-fit-ready without leaving legacy `flagged` or `unflagged` shorthand in the canonical user-facing vocabulary.

## P1-7. Manual Job Creation Scope for MVP
### Problem
The API contract includes `POST /jobs`, but requirements list manual entry as an unresolved decision.

### Risk
API and UI may expose a feature PM has not approved.

### Minimum clarification needed
Decide whether manual entry is in MVP scope.

### Recommended clarification
If manual creation is not approved for MVP, remove `POST /jobs` from the MVP contract and keep only parsed/editable records.

---

# P2. Nice to Clarify Early

## P2-1. Notes Constraints
### Problem
No max length, empty-state handling, or sanitization/display behavior is defined.

### Minimum clarification needed
Set max note length and empty-note behavior.

### Recommended clarification
- allow empty string to clear notes
- cap notes at a reasonable MVP size
- store plain text only in MVP

## P2-2. Tag Normalization Rules
### Problem
Tags can be added by ID or name, but normalization rules are not fully defined.

### Minimum clarification needed
Define casing, whitespace trimming, uniqueness, and special-character handling.

### Recommended clarification
- trim leading/trailing whitespace
- collapse internal duplicate whitespace
- enforce case-insensitive uniqueness per user
- preserve original display casing from first creation or normalize to lower case consistently

## P2-3. Search Result Ordering
### Problem
Default sort is `dateReceived:desc`, but query-specific ordering is not defined.

### Minimum clarification needed
State whether search results still follow explicit sort or relevance-like ordering.

### Recommended clarification
Use explicit sort only, defaulting to `dateReceived:desc` in MVP.
Do not introduce fuzzy relevance ranking yet.

## P2-4. Multi-Job Digest Traceability
### Problem
The ingestion spec supports multi-job extraction but the API contract exposes `jobId` on messages as if a message links to one job.

### Minimum clarification needed
Define one-to-many source message linkage in the data model and API.

### Recommended clarification
A single source message may link to many jobs. Update debug endpoints accordingly.

## P2-5. Uncertain Email Review
### Problem
The ingestion spec asks whether uncertain emails should be retained for manual review later, but no MVP UI is defined.

### Minimum clarification needed
Clarify whether uncertain emails are only debug-visible or user-visible in MVP.

### Recommended clarification
Keep uncertain emails debug-visible only in MVP.

---

# API Contract Issues to Update After Clarification

Once the decisions above are made, update the API contract in these areas:

1. **Job schema**
   - resolve `status` vs `archivedAt`
   - confirm whether `saved` remains in MVP

2. **Jobs list query params**
   - align `saved` and `archived` params with the canonical data model
   - define whether multi-value params repeat or comma-separate

3. **Job detail response**
   - ensure source message model supports one message to many jobs where needed
   - add fit fields if fit is part of MVP job list/detail behavior

4. **Sync endpoints**
   - define final run-status enum
   - define whether `/sync/run` is public UI behavior or debug/admin only

5. **Messages debug endpoints**
   - align `detectionResult` and `parseStatus` with ingestion classification vocabulary
   - support multiple linked jobs when applicable

---

# Recommended Minimum Decision Package

To unblock engineering quickly, PM should issue a short decision memo covering only these items:

1. canonical data model for status, saved, and archived
2. minimum parsed-job creation threshold
3. exact duplicate merge rules
4. Gmail scopes and reconnect-state semantics
5. initial and incremental sync defaults
6. keyword search and filter semantics
7. whether recruiter outreach and manual job entry are in MVP
8. fit-analysis nullability and reanalysis triggers

If those are resolved, implementation can start with materially lower QA and product risk.
