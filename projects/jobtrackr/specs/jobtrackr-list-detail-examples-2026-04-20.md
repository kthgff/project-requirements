# JobTrackr List-to-Detail Contract Examples — 2026-04-20

## Purpose

Provide one canonical examples doc for how the jobs workspace list state, selected-row behavior, and job detail payload work together.

This bridges:
- `specs/jobtrackr-api-contract.md`
- `specs/jobtrackr-detail-view-contract-2026-04-20.md`
- `specs/jobtrackr-workspace-ux-contract-2026-04-20.md`

Jimmy's latest guidance for Marcus was to make the list/detail handoff concrete instead of leaving frontend and QA to infer behavior from separate docs.

## What This Locks

- which list fields must be sufficient to open and maintain detail context
- how selected-row state maps to the detail experience
- how drawer and full-page detail patterns stay equivalent
- how status, notes, and tags behave while a job remains selected in the workspace

## Canonical Rules

1. The jobs list is the source of workspace context.
2. The detail view is the source of full job review and edit interactions.
3. Selection is tracked by `jobId`, never by row index.
4. Drawer and full-page detail must use the same section order:
   1. Header summary
   2. Fit and workflow
   3. Job content
   4. Notes
   5. Tags
   6. Source and activity context
5. Notes use explicit `Save` and `Cancel`.
6. Tags are edited directly in context.
7. Status stays editable in the detail surface.

## Example A: List payload plus selected-row state

### Jobs list response

```json
{
  "data": {
    "items": [
      {
        "id": "job_123",
        "title": "Senior Backend Engineer",
        "company": "Acme",
        "location": "Chicago, IL",
        "source": "linkedin",
        "status": "new",
        "saved": true,
        "dateReceived": "2026-04-20T08:10:00Z",
        "archivedAt": null,
        "fitState": "scored",
        "fitFlag": true,
        "fitScore": 88,
        "fitSummary": "Strong backend and infra match",
        "tags": ["priority", "remote"]
      },
      {
        "id": "job_456",
        "title": "Platform Engineer",
        "company": "Northwind",
        "location": "Remote",
        "source": "indeed",
        "status": "applied",
        "saved": false,
        "dateReceived": "2026-04-19T15:00:00Z",
        "archivedAt": null,
        "fitState": "pending",
        "fitFlag": null,
        "fitScore": null,
        "fitSummary": null,
        "tags": []
      }
    ],
    "page": 1,
    "pageSize": 20,
    "total": 2
  }
}
```

### Matching workspace UI state

```json
{
  "sort": "dateReceived:desc",
  "filters": {
    "q": "engineer",
    "status": ["new", "applied"],
    "location": "Chicago, IL"
  },
  "selectedJobId": "job_123",
  "lastHiddenSelectedJobId": null
}
```

Expected behavior:
- `selectedJobId` highlights the matching row in the list.
- Opening detail for `job_123` uses the detail payload example below.
- Resorting the list preserves selection for `job_123` if it remains visible.
- If filters change and `job_123` no longer matches, clear active selection, keep `lastHiddenSelectedJobId = "job_123"`, and show helper copy.
- If filters are later relaxed and `job_123` becomes visible again in the same session, restore selection automatically.

## Example B: Detail payload for the selected job

```json
{
  "data": {
    "id": "job_123",
    "title": "Senior Backend Engineer",
    "company": "Acme",
    "location": "Chicago, IL",
    "source": "linkedin",
    "dateReceived": "2026-04-20T08:10:00Z",
    "applicationLink": "https://example.com/jobs/job_123",
    "status": "new",
    "saved": true,
    "fitState": "scored",
    "fitFlag": true,
    "fitScore": 88,
    "fitSummary": "Strong backend and infra match",
    "descriptionSnippet": "Lead backend services for workflow automation and internal AI tooling.",
    "salaryText": "$170k-$195k",
    "recruiterName": "Jordan Lee",
    "recruiterEmail": "jordan@acme.example",
    "notes": "Looks strong. Check team size and production ownership.",
    "tags": [
      {
        "id": "tag_priority",
        "name": "priority"
      },
      {
        "id": "tag_remote",
        "name": "remote"
      }
    ],
    "sourceMessages": [
      {
        "id": "src_1",
        "gmailMessageId": "msg_1",
        "subject": "New job recommendation: Senior Backend Engineer",
        "fromEmail": "jobs-noreply@linkedin.com",
        "receivedAt": "2026-04-20T08:05:00Z"
      }
    ],
    "activities": [
      {
        "id": "act_1",
        "type": "source_email_attached",
        "body": "LinkedIn recommendation email linked to this job",
        "createdAt": "2026-04-20T08:11:00Z"
      }
    ]
  }
}
```

## Example C: Status edit while detail stays open

### Request

```json
{
  "status": "interested"
}
```

### Immediate UI expectations

- Update the detail status control optimistically or after success, but keep the same job selected.
- Reflect the new `status` in the list row without collapsing the detail view.
- Do not reorder the row out from under the user unless the active sort explicitly depends on status.

## Example D: Notes edit with explicit save/cancel

### Draft state in UI

```json
{
  "jobId": "job_123",
  "notesDraft": "Looks strong. Check team size, production ownership, and on-call expectations.",
  "notesDirty": true,
  "notesSaving": false
}
```

### Save request

```json
{
  "notes": "Looks strong. Check team size, production ownership, and on-call expectations."
}
```

Expected behavior:
- `Save` persists through `PATCH /jobs/:id`.
- `Cancel` reverts to the last saved value.
- Failed save keeps the edited draft visible and shows inline error text.
- Successful save updates both detail content and any list-level preview that may later consume notes-derived metadata.

## Example E: Direct tag editing in context

### Add tag request

```json
{
  "tagNames": ["staff-plus"]
}
```

### Remove tag request

```text
DELETE /jobs/job_123/tags/tag_remote
```

Expected behavior:
- Add and remove actions do not close the detail view.
- Tag pills update in place after success.
- Duplicate free-text tags are prevented case-insensitively.
- List-level tag chips stay aligned after the detail update succeeds.

## Example F: Selected row becomes hidden by filters

### Starting state
- `selectedJobId = "job_123"`
- user applies `status=applied`
- `job_123` no longer matches the visible set

### Expected outcome

```json
{
  "selectedJobId": null,
  "lastHiddenSelectedJobId": "job_123",
  "helperMessage": "Your previously selected job is hidden by the current filters."
}
```

Behavior rules:
- close the active detail drawer or leave the full-page route only when the selected job is no longer valid for the visible result set
- preserve filters and sort
- offer `Clear all filters` as a recovery action

## Drawer vs Full-Page Detail Equivalence

### Desktop/tablet drawer
- list remains visible
- selected row remains highlighted
- detail panel uses the locked section order

### Mobile full-page detail
- route may change
- same payload and section order apply
- returning to the list restores the prior filter state and scroll position near the selected result

## QA Checks Enabled By This Doc

1. List row selection persists by `jobId`, not row position.
2. Detail section order matches the locked contract.
3. Status edits stay in detail and reflect back into the list.
4. Notes require explicit save/cancel.
5. Tags are editable in context.
6. Hidden selected rows clear cleanly and restore when visible again in-session.

## Recommended Follow-through

1. Cross-reference this doc from the API contract near `GET /jobs` and `GET /jobs/:id`.
2. Use these examples in frontend fixtures and story states.
3. Extend QA cases for T-019 to cover list/detail continuity explicitly.
