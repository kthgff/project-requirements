# JobTrakr Gate A Reconciliation Matrix — 2026-04-20

## Purpose

Give engineering one compact reference for the Gate A decisions that must stay aligned across the PM memo, schema, API contract, milestone tickets, and handoff package.

## Canonical precedence

When docs conflict, use this order:
1. `projects/jobtrackr/DEVELOPMENT_PLAN.md`
2. `projects/jobtrackr/specs/jobtrackr-pm-decision-memo-2026-04-19.md`
3. `projects/jobtrackr/specs/jobtrackr-api-contract.md`
4. `projects/jobtrackr/specs/jobtrackr-database-schema.md`
5. `projects/jobtrackr/specs/jobtrackr-milestones-1-to-4-engineering-tickets-2026-04-20.md`
6. `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`

## Gate A decisions

| Gate A area | Canonical rule | Current source-of-truth references | Known stale or risky references | Engineering action |
|---|---|---|---|---|
| Workflow status model | `status` is workflow state only. Allowed values: `new`, `interested`, `applied`, `interviewing`, `offer`, `rejected`. | PM memo sections 1 and 12, API contract canonical job model, database schema `job_status`, milestone tickets Gate A and JT-M3-01, handoff package Gate A section | `jobtrackr-auto-close-logic-spec-v1.md`, `jobtrackr-data-model-v1.md`, `jobtrackr-mvp-stories-v1.md`, and older milestone language still mention `flagged`, `reviewing`, `skipped`, `interview`, or `not a match` as statuses | Do not add or persist pre-memo statuses in schema, API payloads, frontend enums, or QA fixtures |
| Archive behavior | Archive is separate from workflow state and is represented only by `archivedAt` nullable timestamp. `archived` is a view/filter concept, not a workflow enum. | PM memo section 1, API contract canonical job model plus archive endpoints, database schema `jobs.archived_at`, dependency sequencing Gate A notes | Older docs that imply `archived` as a status or mix archive into workflow progression | Keep archive filtering and archive/unarchive actions separate from `status` transitions |
| Saved behavior | `saved` is a separate boolean shortlist flag, not a workflow state. | PM memo section 1, API contract canonical job model, database schema `jobs.saved` | Older product language that treats shortlist behavior as part of status progression | Preserve `saved` independently from workflow and archive state |
| Fit nullability before analysis | Before fit analysis exists, `fitFlag = null`, `fitScore = null`, and `fitSummary = null`. UI may use `fitState` to distinguish pending vs scored vs unavailable. | PM memo section 9, API contract list/detail payload examples, database schema nullable fit columns, dependency sequencing note on fit defaults | Any doc or fixture that assumes fit defaults to false, zero, or a required value before analysis | Keep fit fields nullable in schema and responses until analysis runs, and avoid coercing null into false or 0 |
| Dashboard-facing canonical job fields | Shared job payload must support the dashboard and handoff docs with consistent fields, including `id`, `title`, `company`, `location`, `source`, `status`, `saved`, `dateReceived`, `archivedAt`, fit fields, and tags. | API contract `GET /jobs`, PM memo sections 1 and 9, milestone ticket JT-M3-02, handoff package Gate A and Gate B notes | Older docs that still optimize around pre-canonical table assumptions or omit archive and fit-nullability semantics | Frontend fixtures and backend responses should match the current API contract, not older table drafts |
| Source-email linkage | A single source email may link to multiple jobs. Use join-table semantics for `job_source_emails`. Do not collapse linkage onto a single job field. | PM memo section 12, database schema `job_source_emails`, dependency sequencing source-email linkage notes, milestone ticket JT-M3-01, API contract `sourceMessages` detail response | Older data-model text that leans on one `source_email_id` on `job` or treats linkage as one-to-one | Implement and document one-to-many and many-to-many linkage via `job_source_emails`, including debug and detail-view support |
| Gate A readiness for engineering | Gate A is only ready when the PM memo, schema, API contract, milestone tickets, and handoff package all reflect the same workflow, archive, fit-nullability, and linkage rules. | Development plan T-017 and T-025, handoff package Gate A status, milestone tickets Gate A section | Any lane starting implementation from older specs without checking the current canonical set | Use this matrix as the quick drift check before implementation pickup |

## Immediate drift callouts

### 1. Auto-close spec is not canonical for workflow semantics
`specs/jobtrackr-auto-close-logic-spec-v1.md` still uses pre-decision statuses including `flagged`, `reviewing`, `skipped`, `interview`, and `not a match`.

Engineering rule:
- treat that file as stale on status semantics until it is rewritten against the PM memo
- do not use it to define backend enums or UI state options

### 2. Older story docs still describe the pre-decision workflow model
`stories/jobtrackr-mvp-stories-v1.md` still lists old statuses.

Engineering rule:
- use story docs for user intent only
- use the PM memo and API contract for actual state modeling

### 3. Older data-model notes still hint at single-source linkage
`specs/jobtrackr-data-model-v1.md` still leans toward a single `source_email_id` path.

Engineering rule:
- the schema and PM memo have already moved to a join-table model
- any new implementation should target `job_source_emails`

## Ready-to-use implementation checks

Before a lane starts or reviews work, confirm:
- no new enum or fixture uses `flagged`, `reviewing`, `skipped`, `interview`, `not a match`, or `archived` as workflow statuses
- archive behavior is implemented through `archivedAt`
- `saved` remains independent from workflow state
- fit fields stay nullable before analysis
- source-email linkage supports one email to many jobs
- new examples and QA cases point back to the current API contract and PM memo

## Recommended follow-through

1. Rewrite the auto-close spec against the canonical workflow model
2. Update older story and milestone docs that still expose pre-decision statuses
3. Keep the handoff package linked to this matrix until Gate A is explicitly marked closed
