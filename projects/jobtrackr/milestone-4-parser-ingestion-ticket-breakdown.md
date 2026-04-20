# Milestone 4 Parser and Ingestion Ticket Breakdown

## Purpose
Translate the current Gmail ingestion baseline and parser follow-through into implementation-ready engineering tickets that can be picked up once the jobs dashboard payload contract is stable.

## Depends On
- T-001, PM decision alignment in JobTrackr API contract and debug semantics
- T-004, jobs dashboard contract for match rating, flagged semantics, and table payload shape

## Delivery Notes
- These tickets assume Gmail scope remains readonly.
- Deduplication remains keyed on job URL.
- Canonical low-fit handling below fit score 60 stays downstream of ingestion and fit analysis as a fit-signal concern, not inside the parser and not as a workflow-state mutation.
- Parser output should match the required job fields already captured in `DEVELOPMENT_PLAN.md` decisions.

## Proposed Tickets

### JT-M4-01, Gmail client wrapper and token lifecycle hardening
**Goal:** Make Gmail reads reliable before parsing logic expands.

**Scope**
- Encapsulate Gmail API calls behind a reusable client wrapper
- Add token refresh handling and failure logging
- Normalize pagination and checkpoint inputs for incremental sync

**Dependencies**
- OAuth flow and token storage baseline exists

**Acceptance notes**
- Worker can request recent messages without duplicating auth logic
- Expired access tokens refresh automatically
- Failures are logged with enough context for debugging

---

### JT-M4-02, Incremental inbox polling and checkpoint persistence
**Goal:** Ensure each sync pass is resumable and bounded.

**Scope**
- Persist last successful sync checkpoint
- Fetch messages incrementally from the checkpoint window
- Add a manual backfill override for the local MVP

**Dependencies**
- JT-M4-01

**Acceptance notes**
- Re-running sync does not reprocess the full mailbox by default
- Checkpoint updates only after a successful batch
- Manual backfill remains possible for local testing

---

### JT-M4-03, Job-email detection rules package
**Goal:** Decide which Gmail messages should enter parsing.

**Scope**
- Define sender, subject, and label heuristics for relevant job emails
- Capture detection reasons in logs for debugging
- Keep the rules isolated from downstream parsing

**Dependencies**
- JT-M4-02

**Acceptance notes**
- Non-job emails are skipped consistently
- Debug output explains why an email matched or was rejected
- Detection rules can be extended without changing parser flow

---

### JT-M4-04, Parsed job payload contract for ingestion pipeline
**Goal:** Lock the internal parser output shape before multiple parsing rules accumulate.

**Scope**
- Define parser output fields for title, company, location, salary, source URL, and raw extraction metadata
- Mark required vs optional fields
- Document confidence and fallback behavior for partial parses

**Dependencies**
- T-001, T-004
- JT-M4-03

**Acceptance notes**
- Parser output maps cleanly onto the jobs persistence model
- Partial parses still produce useful metadata without corrupting job records
- Contract is stable enough for implementation without PM follow-up

---

### JT-M4-05, Rule-based parser for high-signal alert formats
**Goal:** Turn the most common job alert emails into structured job candidates.

**Scope**
- Implement parser rules for the highest-signal templates first, starting with LinkedIn and Indeed style alerts
- Extract title, company, location, source URL, and salary when present
- Preserve raw snippets when extraction is ambiguous

**Dependencies**
- JT-M4-04

**Acceptance notes**
- Common alert formats produce structured candidate jobs
- Missing fields degrade gracefully instead of failing the batch
- Raw snippets remain available for future parser refinement

---

### JT-M4-06, Job URL deduplication and merge policy
**Goal:** Prevent repeated alerts from creating duplicate active jobs.

**Scope**
- Match parsed candidates by canonicalized job URL
- Define merge rules for refreshed metadata vs existing user-edited fields
- Record whether a sync created or updated a job

**Dependencies**
- JT-M4-05

**Acceptance notes**
- Repeated alerts update the same job record when URLs match
- User-authored notes, tags, and workflow state are preserved
- Sync logs show create vs update outcomes

---

### JT-M4-07, Ingestion observability and replay-safe error handling
**Goal:** Make parser and ingestion failures diagnosable without manual guesswork.

**Scope**
- Add structured logs for detection, parse, dedupe, and persistence stages
- Track per-message failure reasons
- Ensure one bad message does not fail the entire batch

**Dependencies**
- JT-M4-03 through JT-M4-06

**Acceptance notes**
- Engineers can identify the failing stage for a message quickly
- Batch continues after single-message failures
- Retry and replay do not duplicate persisted jobs

## Recommended Pickup Order
1. JT-M4-01
2. JT-M4-02
3. JT-M4-03
4. JT-M4-04
5. JT-M4-05
6. JT-M4-06
7. JT-M4-07

## Coordination Watchouts
- Do not let parser output invent fields that drift from the locked dashboard payload.
- Keep fit scoring and canonical low-fit handling out of the ingestion parser layer, and do not treat fit signals as workflow-state changes.
- Preserve user-authored workflow fields during dedupe updates.
- Treat any flagged or low-fit indicators as fit-signal metadata only, never as new workflow statuses.
