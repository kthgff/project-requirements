# JobTrackr List-to-Detail Contract Examples — 2026-04-20

## Purpose

Provide canonical examples for T-023 so the jobs list payload, selected-row behavior, and job detail rendering stay aligned across dashboard, drawer, and full-page detail experiences.

This document is the concrete reference for:
- how one `GET /jobs` row maps into `GET /jobs/:id`
- what selection continuity means when filters, sorting, or layout change
- how drawer and full-page detail views stay behaviorally identical
- how status, notes, and tags editing should behave without inventing new payload assumptions

## Relationship to Existing Contracts

- List payload shape remains defined in `specs/jobtrackr-api-contract.md`
- Detail section order and edit rules remain defined in `specs/jobtrackr-detail-view-contract-2026-04-20.md`
- Workspace selection and filter continuity remain defined in `specs/jobtrackr-workspace-ux-contract-2026-04-20.md`

This file bridges those contracts with end-to-end examples.

## Canonical Example A: Desktop jobs table to right-side detail drawer

### A1. Jobs list response example

```json
{
  "data": {
    "items": [
      {
        "id": "job_123",
        "title": "Senior Backend Engineer",
        "company": "Northstar Health",
        "location": "Remote",
        "source": "linkedin",
        "status": "new",
        "saved": true,
        "dateReceived": "2026-04-20T08:45:00Z",
        "archivedAt": null,
        "fitState": "scored",
        "fitFlag": true,
        "fitScore": 91,
        "fitSummary": "Strong match for Go backend, APIs, and cloud infrastructure",
        "tags": ["priority", "remote"]
      },
      {
        "id": "job_124",
        "title": "Platform Engineer",
        "company": "Acme Systems",
        "location": "Chicago, IL",
        "source": "greenhouse",
        "status": "applied",
        "saved": false,
        "dateReceived": "2026-04-20T07:15:00Z",
        "archivedAt": null,
        "fitState": "pending",
        "fitFlag": null,
        "fitScore": null,
        "fitSummary": null,
        "tags": ["onsite"]
      }
    ],
    "page": 1,
    "pageSize": 20,
    "total": 2
  }
}
```

### A2. Table rendering expectations from that payload

For `job_123`, the table can render everything required for scan and selection without loading full detail first:
- company: `Northstar Health`
- title: `Senior Backend Engineer`
- location: `Remote`
- source: `linkedin`
- date found: `2026-04-20`
- fit column: `91`
- flagged indicator: visible because `fitFlag = true`
- workflow state: `new`
- lightweight tag hints are allowed, but not required

The list row is intentionally summary-only. It must not require recruiter metadata, notes body, source messages, or activities.

### A3. Selected-row state example

When the user clicks `job_123`, workspace state should look conceptually like:

```json
{
  "selectedJobId": "job_123",
  "selectionVisibleInResults": true,
  "detailPresentation": "drawer",
  "activeFilters": {
    "status": ["new", "applied"],
    "q": "engineer"
  },
  "sort": "dateReceived:desc"
}
```

Selection rules locked by this example:
- selection is keyed by `selectedJobId`, not by row index
- sorting the table does not clear selection
- non-destructive filter changes do not clear selection if `job_123` remains in the result set
- selected-row styling remains visible while the drawer is open

### A4. Detail response example for the selected job

```json
{
  "data": {
    "id": "job_123",
    "title": "Senior Backend Engineer",
    "company": "Northstar Health",
    "location": "Remote",
    "source": "linkedin",
    "descriptionSnippet": "Build backend services for scheduling, provider search, and care operations.",
    "applicationLink": "https://example.com/jobs/job_123",
    "recruiterName": "Dana Smith",
    "recruiterEmail": "dana@northstar.example",
    "salaryText": "$170k-$195k",
    "status": "new",
    "saved": true,
    "notes": "Looks strong on Go and platform work. Check healthcare compliance angle.",
    "dateReceived": "2026-04-20T08:45:00Z",
    "archivedAt": null,
    "fitState": "scored",
    "fitFlag": true,
    "fitScore": 91,
    "fitSummary": "Strong match for Go backend, APIs, and cloud infrastructure",
    "tags": [
      { "id": "tag_1", "name": "priority" },
      { "id": "tag_2", "name": "remote" }
    ],
    "activities": [
      {
        "id": "act_1",
        "type": "source_email_attached",
        "body": "Source email linked to job",
        "createdAt": "2026-04-20T08:45:30Z"
      }
    ],
    "sourceMessages": [
      {
        "id": "msg_1",
        "gmailMessageId": "1966abc",
        "subject": "New role you may like: Senior Backend Engineer",
        "fromEmail": "jobs-noreply@linkedin.example",
        "receivedAt": "2026-04-20T08:44:10Z"
      }
    ]
  }
}
```

### A5. Mapping rules from list row to detail response

The following fields must stay consistent between list and detail for the same job id:
- `id`
- `title`
- `company`
- `location`
- `source`
- `status`
- `saved`
- `dateReceived`
- `archivedAt`
- `fitState`
- `fitFlag`
- `fitScore`
- `fitSummary`

If a user opens detail for a list row and sees a different status or fit value without making an edit, that is contract drift.

## Canonical Example B: Filter change while a job is selected

### B1. Selection survives when the selected job still matches

Starting state:

```json
{
  "selectedJobId": "job_123",
  "activeFilters": {
    "status": ["new", "applied"]
  }
}
```

User adds `source=linkedin`.

If `job_123` is still in the filtered result set:
- keep `selectedJobId = job_123`
- keep drawer open
- keep selected-row styling in the refreshed list
- update the active-filter summary count

### B2. Selection clears when the selected job leaves the result set

User changes filters to:

```json
{
  "status": ["applied"],
  "source": ["greenhouse"]
}
```

If `job_123` no longer matches:
- clear visible selected-row styling
- close the detail drawer
- clear `selectedJobId`
- keep the user in the jobs workspace
- show updated filter summary and results without implying a broken detail panel

Preferred helper copy is allowed, for example: `Your previous selection is no longer in the filtered results.`

## Canonical Example C: Drawer and full-page detail parity

The same selected job may render in two layouts:
- desktop: right-side drawer
- mobile: full-page route such as `/jobs/job_123`

Layout may change, but the following must not change:
- section order
- field names and meanings
- editable versus view-only distinctions
- save and cancel behavior for notes
- tag add and remove behavior
- status editing behavior

### C1. Equivalent presentation contract

Both drawer and full-page detail must render this order:
1. Header summary
2. Fit and workflow
3. Job content
4. Notes
5. Tags
6. Source and activity context

A mobile detail page is not allowed to move tags above notes or hide workflow editing just because it is a route instead of a drawer.

## Canonical Example D: Editable interactions inside detail

### D1. Status edit example

User changes status from `new` to `applied`.

Request:

```json
{
  "status": "applied"
}
```

Behavior expectations:
- detail view updates to `applied` after success
- the corresponding list row updates to `applied`
- selection remains on the same job
- no full-page refresh is required

### D2. Notes edit example

User edits notes from:

```text
Looks strong on Go and platform work. Check healthcare compliance angle.
```

to:

```text
Looks strong on Go and platform work. Check healthcare compliance angle. Good referral target.
```

Request:

```json
{
  "notes": "Looks strong on Go and platform work. Check healthcare compliance angle. Good referral target."
}
```

Behavior expectations:
- notes editing uses explicit `Save` and `Cancel`
- unsaved note text stays local until save succeeds
- list row is not required to render the notes body
- a successful save must not clear selection or close the current detail context

### D3. Tag add example

Request:

```json
{
  "tagNames": ["priority", "referral"]
}
```

Behavior expectations:
- `priority` is not duplicated if it already exists case-insensitively
- resulting detail view shows both tags once
- list row may update tag hints if the UI surfaces them, but full tag editing remains a detail concern

### D4. Tag remove example

Request:

```json
DELETE /jobs/job_123/tags/tag_2
```

Behavior expectations:
- remove happens from one obvious action in the detail view
- detail view remains open after success
- selection remains anchored to `job_123`

## Canonical Example E: Table payload boundaries

The list payload is sufficient for scan, selection, and navigation.
It is not required to include:
- full notes body
- recruiter email
- source message history
- activities timeline

The detail payload is required for review and editing context.
It must include the fields needed by the locked section order and edit model.

This boundary prevents frontend and API drift where the list endpoint slowly turns into a second detail endpoint.

## QA Checklist Derived From These Examples

Use these checks when validating T-023 follow-through:
- opening a row selects by job id, not row position
- changing sort order does not silently select a different row
- selection persists across filter edits only when the selected job is still visible
- drawer and full-page detail render the same section order
- status edits reflect in both detail and list state for the same job
- notes require explicit save and cancel
- tag add and remove happen in detail without modal-heavy flow
- list payload and detail payload agree on shared fields for the same job id

## Contract Decisions Locked Here

- Selection continuity is id-based, not index-based.
- The jobs list stays summary-oriented; detail owns rich review context.
- Drawer and full-page detail are layout variants of the same contract, not separate experiences.
- Status, notes, and tags are the only required editable fields in MVP detail.
- Shared fields must stay semantically identical between list and detail payloads for the same job.
