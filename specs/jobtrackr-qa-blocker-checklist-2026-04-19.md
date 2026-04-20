# JobTrackr QA Blocker Checklist for P0 Behaviors

## Purpose
This checklist converts the former open P0 product decisions into concrete QA blockers and blocked test coverage. It is the shortest implementation-facing QA artifact for the current MVP handoff.

Primary source documents:
- `specs/jobtrackr-pm-decision-memo-2026-04-19.md`
- `projects/jobtrackr/api-contract.md`
- `specs/jobtrackr-gmail-ingestion-spec-v1.md`
- `specs/jobtrackr-mvp-qa-testability-review.md`
- `specs/jobtrackr-spec-clarifications-needed.md`

Status:
- Product decisions are resolved in the PM memo.
- QA remains blocked anywhere those decisions are not implemented consistently in code and reflected in the ingestion spec, API contract, and QA fixtures.

---

## 1. Status, Saved, and Archived Semantics

### Canonical MVP rule
- `status` is workflow state only
- `saved` is a boolean flag
- archive state is controlled by `archivedAt`
- `archived` is not a workflow status

### Blocked test cases
- [ ] Status edit coverage for only `new`, `interested`, `applied`, `interviewing`, `offer`, `rejected`
- [ ] Inbox query returns only non-archived jobs with `status=new`
- [ ] Saved view returns only non-archived jobs with `saved=true`
- [ ] Archived view returns only jobs with `archivedAt != null`
- [ ] Status filters reject `archived` as a status value
- [ ] Archive/unarchive endpoints persist correctly across refresh and subsequent list queries
- [ ] Saved + archived interactions exclude archived jobs from saved view by default

### Blocking condition
QA cannot lock expected datasets for inbox, all jobs, saved, archived, or status filters if archive is modeled both as a state and as an attribute.

---

## 2. Parsed Job Creation Threshold

### Canonical MVP rule
Create a parsed job only when at least one is true:
- external job ID exists
- normalized job URL exists
- title and company both exist
- title, location, and source platform exist with medium-or-higher confidence

Otherwise, persist source email only.

### Blocked test cases
- [ ] URL-only relevant message creates a parsed job
- [ ] Title+company relevant message creates a parsed job without URL
- [ ] Below-threshold relevant message creates no job and remains source-email-only
- [ ] Multi-job digest can yield mixed outcomes in one message: zero, one, or many jobs
- [ ] Null title and/or null company rows are allowed only when another threshold path is satisfied
- [ ] Relevant but unparseable email remains visible in debug review

### Blocking condition
Without a hard threshold, QA cannot separate valid partial rows from extraction junk.

---

## 3. Deduplication and Ambiguous Matches

### Canonical MVP rule
A candidate is a duplicate only when one of these is exact after normalization:
1. external job ID
2. normalized job URL
3. normalized company + title + location

Near matches do not auto-merge in MVP.

Allowed duplicate updates:
- attach source linkage
- update `lastSeenAt`
- fill null fields
- replace lower-confidence extracted fields

Protected fields:
- notes
- tags
- `saved`
- `archivedAt`
- manual status edits

### Blocked test cases
- [ ] Tracking-parameter URL variants dedupe to one job
- [ ] Same title/company with different normalized locations do not merge
- [ ] Same title/location with different companies do not merge
- [ ] Repeat alerts do not overwrite user-managed fields
- [ ] Ambiguous near-duplicates create separate jobs
- [ ] One message linking to multiple jobs remains traceable after duplicate attachment behavior

### Blocking condition
Without exact merge rules, QA cannot write deterministic duplicate, update, or traceability assertions.

---

## 4. Search and Filter Semantics

### Canonical MVP rule
- keyword search is case-insensitive substring match
- search scope is `title`, `company`, and `descriptionSnippet` only
- filter families combine with AND logic
- repeated values within a family combine with OR logic
- date boundaries are inclusive
- date filtering uses the user-facing timezone against UTC-stored timestamps
- location filtering is case-insensitive exact match on normalized location
- default sort is `dateReceived:desc`

### Blocked test cases
- [ ] Query matches title/company/snippet but not notes or tags
- [ ] Search handles partial jobs with null searchable fields cleanly
- [ ] Repeated status filters behave as OR within the status family
- [ ] Search + status + tag + date filters behave as intersection across families
- [ ] `dateFrom` and `dateTo` include same-day boundary records in user timezone
- [ ] Location filter collapses casing differences but not semantically different locations
- [ ] Clearing search/filters restores the correct base dataset for inbox, all jobs, saved, and archived

### Blocking condition
QA cannot freeze fixture expectations without one canonical query and filter model.

---

## 5. Contract Mismatches That Still Destabilize QA

These are the highest-risk contract mismatches to re-check during implementation review.

### Mismatch checklist
- [ ] `POST /jobs` remains out of the MVP contract and is not reintroduced in implementation
- [ ] Archived handling is still represented by `archivedAt`, not a status enum value
- [ ] Saved handling remains a boolean flag and saved view logic stays non-archived only
- [ ] Sync run statuses stay limited to `queued`, `running`, `completed`, `partial`, `failed`, `canceled`
- [ ] `GET /messages` and `GET /messages/:id` support one-message-to-many-jobs traceability
- [ ] Source-email persistence excludes clearly irrelevant mail by default
- [ ] Uncertain-message behavior stays deterministic: persist for debug visibility, extract only with strong identifier

---

## 6. QA Gate Decision

### Build readiness
Status: **Conditionally testable, not yet stable for sign-off**

### Ready when
- the ingestion spec, API contract, and implementation all express the same canonical P0 behavior
- fixtures can be built without stale alternative interpretations
- the mismatch checklist above passes in code and docs
