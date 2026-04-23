# JobTrackr Low-Fit Handling Spec v1

## Status
This document has been rewritten on 2026-04-20 to remove stale pre-Gate-A workflow semantics.
Use it only with the PM decision memo, API contract, and Gate A reconciliation matrix.

Canonical references:
- `specs/jobtrackr-pm-decision-memo-2026-04-19.md`
- `specs/jobtrackr-api-contract.md`
- `specs/jobtrackr-gate-a-reconciliation-matrix-2026-04-20.md`

## Purpose
Define the MVP backend behavior for handling low-fit jobs after fit analysis runs, without reintroducing deprecated workflow statuses.

## Scope
In scope:
- low-fit evaluation after fit analysis writes or refreshes a score
- guardrails that prevent unwanted overwrites of canonical workflow states
- event logging and status-history expectations when a canonical workflow change is allowed
- list and detail API expectations after low-fit evaluation

Out of scope:
- the fit-scoring algorithm itself
- manual bulk review tooling
- notification behavior
- any new workflow enum values outside the PM memo

## Canonical workflow guardrail
Canonical workflow statuses remain:
- `new`
- `interested`
- `applied`
- `interviewing`
- `offer`
- `rejected`

The system must not introduce or persist `flagged`, `reviewing`, `skipped`, `interview`, `not a match`, or `archived` as workflow statuses.
Archive behavior remains separate through `archivedAt`, and shortlist behavior remains separate through `saved`.

## Current MVP rule
When a job receives a fit score below `60`, the system may mark the job as low-fit for triage and logging purposes, but it must not create a non-canonical workflow state.

Until a follow-up PM decision explicitly defines an automated low-fit transition inside the canonical model, low-fit handling must follow this safe default:
- keep the current canonical workflow `status` unchanged
- preserve `saved` and `archivedAt`
- persist the latest `fitScore`, `fitFlag`, and `fitSummary`
- emit a structured low-fit evaluation log
- optionally append a status-history or audit entry only if a future canonical transition is explicitly approved

## Trigger
Run the rule whenever fit analysis writes or refreshes the latest fit result for a job.

Primary trigger points:
- new job created and analyzed
- existing job reanalyzed after enrichment
- existing job reanalyzed after active resume changes

## Inputs
Required:
- `job.id`
- current `job.status`
- latest `job.fitScore`
- analysis timestamp

Optional but recommended:
- prior fit score
- trigger source (`new_job`, `job_enriched`, `resume_changed`, `manual_reanalysis`)

## Decision table

### Case 1. Fit score is null
- do not apply low-fit handling
- leave current status unchanged
- log that low-fit evaluation was skipped because score is unavailable

### Case 2. Fit score is 60 or above
- do not apply low-fit handling
- leave current status unchanged
- keep canonical fit fields updated

### Case 3. Fit score is below 60 and current status is a canonical workflow state
- leave `status` unchanged
- preserve `saved` and `archivedAt`
- persist the low-fit score and summary
- log that the job fell below the threshold without mutating workflow state

## Persistence requirements
When low-fit evaluation runs:
- keep `job.status` within the canonical enum
- update fit fields from the latest analysis result
- update `job.updated_at`
- do not write `to_status = 'not a match'`
- do not create history rows for deprecated status names

If a later PM-approved canonical transition is added, this document must be revised before implementation.

## API expectations

### Jobs list API
Low-fit jobs must appear with:
- canonical `status`
- latest `fitScore`
- latest `fitFlag`
- latest `fitSummary`
- unchanged `saved` and `archivedAt` behavior

### Job detail API
Expose enough detail for debugging and user trust:
- current canonical status
- current fit score
- fit summary if present
- latest related activity or audit metadata if debug mode is enabled

## Logging requirements
Emit a structured log for each evaluation:
- `job_id`
- `fit_score`
- `threshold = 60`
- `current_status`
- `action = skipped_null_score | above_threshold | low_fit_logged_no_status_change`
- trigger source

## Pseudocode

```text
if fit_score is null:
  log skipped_null_score
  return

if fit_score >= 60:
  log above_threshold
  return

log low_fit_logged_no_status_change
return
```

## Acceptance criteria additions
To consider low-fit handling ready for implementation, verify:
- jobs with `fitScore < 60` do not create deprecated workflow statuses
- jobs with `fitScore = 60` do not trigger low-fit handling
- jobs with `fitScore = null` do not trigger low-fit handling
- canonical workflow statuses remain unchanged unless a future PM-approved transition exists
- structured logs capture skip and low-fit paths without stale enum values

## Follow-up note
T-006 and any related implementation should be treated as a canonical low-fit handling task, not as permission to persist `not a match`.
If PM wants automated archive or another canonical transition later, update the PM memo and API contract first, then revise this spec.