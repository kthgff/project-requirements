# JobTrackr Auto-Close Logic Spec v1

## Purpose

Define the MVP backend behavior for automatically moving low-fit jobs to `not a match` once fit analysis completes.

## Scope

In scope:
- status transition rules when fit analysis produces a score
- guardrails that prevent unwanted overwrites of user-managed workflow states
- event logging and status history expectations
- list/detail API behavior after auto-close runs

Out of scope:
- the fit-scoring algorithm itself
- manual bulk review tooling
- notification behavior

## Canonical Rule

When a job receives a fit score below `60`, the system must automatically set job status to `not a match`.

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
- latest `job.fit_score`
- analysis timestamp

Optional but recommended:
- prior fit score
- trigger source (`new_job`, `job_enriched`, `resume_changed`, `manual_reanalysis`)

## Decision Table

### Case 1. Fit score is null
- do not auto-close
- leave current status unchanged
- log that auto-close was skipped because score is unavailable

### Case 2. Fit score is 60 or above
- do not auto-close
- leave current status unchanged

### Case 3. Fit score is below 60 and current status is `new`
- set status to `not a match`
- append `job_status_history` entry
- log auto-close reason

### Case 4. Fit score is below 60 and current status is `flagged` or `reviewing`
- set status to `not a match`
- append `job_status_history` entry
- log auto-close reason

### Case 5. Fit score is below 60 and current status is user-advanced
Statuses that must not be auto-overwritten:
- `applied`
- `interview`
- `offer`
- `rejected`

Behavior:
- leave status unchanged
- log that the job was below threshold but status was protected

### Case 6. Fit score is below 60 and current status is already `not a match`
- no-op
- avoid duplicate history rows

### Case 7. Fit score is below 60 and current status is `skipped`
- leave status unchanged
- log protected manual state

## Recommended Protected-State Rule

For MVP, auto-close should only mutate statuses that still represent pre-decision triage:
- `new`
- `flagged`
- `reviewing`

It should not overwrite statuses that imply a human already made or advanced a decision:
- `skipped`
- `not a match`
- `applied`
- `interview`
- `rejected`
- `offer`

This avoids destructive workflow churn after user action.

## Persistence Requirements

When auto-close changes a job status:
- update `job.status = 'not a match'`
- set `job.updated_at`
- append `job_status_history` row with:
  - `job_id`
  - `from_status`
  - `to_status = 'not a match'`
  - `reason = 'auto_close_fit_score_below_threshold'`
  - `metadata.threshold = 60`
  - `metadata.fit_score = <latest score>`
  - `created_at`

## API Expectations

### Jobs list API
Low-fit jobs that were auto-closed must appear with:
- `status = 'not a match'`
- latest `fitScore`
- latest `fitFlag`
- latest `fitSummary`

### Job detail API
Expose enough detail for debugging and user trust:
- current status
- current fit score
- latest status history row
- optional audit metadata if debug mode is enabled

## Logging Requirements

Emit a structured log for each evaluation:
- `job_id`
- `fit_score`
- `threshold = 60`
- `current_status`
- `action = auto_closed | skipped_null_score | skipped_protected_status | no_op_already_closed | skipped_above_threshold`
- trigger source

## Pseudocode

```text
if fit_score is null:
  log skipped_null_score
  return

if fit_score >= 60:
  log skipped_above_threshold
  return

if status in ['new', 'flagged', 'reviewing']:
  set status = 'not a match'
  insert status history row
  log auto_closed
  return

if status == 'not a match':
  log no_op_already_closed
  return

log skipped_protected_status
```

## Acceptance Criteria Additions

To consider T-006 done, verify:
- jobs with `fit_score < 60` move from `new` to `not a match`
- jobs with `fit_score < 60` move from `flagged` or `reviewing` to `not a match`
- jobs with `fit_score = 60` do not auto-close
- jobs with `fit_score = null` do not auto-close
- jobs already in `applied`, `interview`, `offer`, `rejected`, or `skipped` are not overwritten
- a status history row is written only when a transition occurs
- structured logs capture both transition and skip paths

## Open Implementation Question

The current plan says score below 60 becomes `not a match`, but it does not explicitly say whether `skipped` is protected from later auto-close passes.

Recommendation:
- treat `skipped` as protected because it reflects explicit user intent
- only revisit that if PM wants low-fit reanalysis to override manual triage states
