# JobTrackr Detail View Contract — 2026-04-20

## Purpose

Define one shared job detail view contract for the frontend and API work tied to T-008 and T-012.

This contract locks:
- the required field groups for the job detail experience
- what is view-only vs editable in MVP
- the interaction model for notes and tags
- the API expectations the frontend can build against now

## Why This Exists

The current API contract already defines a single-job payload, but the UI interaction pattern for the detail experience is still loose. Jimmy's latest guidance was to keep detail editing downstream of one agreed contract instead of letting T-008 and T-012 invent separate behaviors.

## Canonical Detail Experience

### Default presentation
- The dashboard remains the main workspace.
- Selecting a job from the jobs table opens a right-side detail panel on desktop.
- On smaller screens, the same content may render as a full-page detail route using the same section order and payload shape.
- The detail experience should not fork into separate "view" and "edit" screens for MVP.

### Primary user goals
- scan the full job context without losing table context
- update status quickly
- capture notes while reviewing
- add and remove lightweight tags
- open the original job posting

## Detail View Section Order

Render sections in this order so the UI stays consistent across mock and real data:

1. **Header summary**
2. **Fit and workflow**
3. **Job content**
4. **Notes**
5. **Tags**
6. **Source and activity context**

## Section Contract

### 1. Header summary
Required fields:
- `title`
- `company`
- `location`
- `source`
- `dateReceived`
- `applicationLink`

Display rules:
- `title` is the primary heading.
- `company` and `location` sit directly under the title.
- `source` and `dateReceived` are secondary metadata.
- `applicationLink` renders as a clear external action such as `Open job posting`.

### 2. Fit and workflow
Required fields:
- `status`
- `fitState`
- `fitScore`
- `fitFlag`
- `fitSummary`

Interaction rules:
- `status` is editable in place from a select or segmented control.
- `fitScore`, `fitFlag`, and `fitSummary` are view-only in MVP.
- When `fitState = pending`, show a neutral pending state instead of an empty score.
- When `fitState = unavailable`, explain that fit data is not ready rather than implying a low score.

### 3. Job content
Required fields:
- `descriptionSnippet`
- `salaryText`
- `recruiterName`
- `recruiterEmail`

Display rules:
- `descriptionSnippet` may be renamed to `description` later, but frontend mock data should treat it as the main body copy area for now.
- Missing recruiter or salary data should collapse quietly rather than render placeholder noise.

### 4. Notes
Required fields:
- `notes`

Interaction rules:
- Notes are edited inline inside the detail view.
- MVP uses a simple textarea interaction.
- The notes area has explicit `Save` and `Cancel` actions once edited.
- The UI should support an empty starting state with helpful prompt copy such as `Add review notes`.
- Avoid autosave for MVP so the interaction model stays obvious.

API expectation:
- Save through `PATCH /jobs/:id` with the `notes` field.
- Optional `PUT /jobs/:id/notes` remains non-blocking and should not be required for MVP UI completion.

### 5. Tags
Required fields:
- `tags[]` with `id` and `name`

Interaction rules:
- Existing tags render as removable pills.
- The user can add a tag from the detail view without leaving context.
- MVP can support free-text tag entry that maps onto `tagNames` in the API.
- Removing a tag should require only one obvious action, not a modal.
- Duplicate tag creation should be prevented case-insensitively.

API expectation:
- Add tags through `POST /jobs/:id/tags` using `tagNames` or `tagIds`.
- Remove tags through `DELETE /jobs/:id/tags/:tagId`.
- A global tag picker is optional for MVP; free-text add is acceptable if the backend normalizes names.

### 6. Source and activity context
Required fields:
- `sourceMessages[]`
- `activities[]`

Display rules:
- Keep this section lower emphasis than the main review content.
- The first visible source-message fields should be `subject`, `fromEmail`, and `receivedAt`.
- Activity items should be reverse chronological.
- This section is view-only in MVP.

## Editable vs View-Only Summary

### Editable in MVP
- `status`
- `notes`
- `tags`

### View-only in MVP
- title and company metadata
- fit score, fit state, fit summary, fit flag
- source/job posting link
- recruiter metadata
- source messages and activities

## Loading, Empty, and Error States

### Loading
- Show skeletons for header, fit section, notes, and tags.
- Keep panel chrome visible so the open state feels stable.

### Empty notes
- Show an inviting empty state rather than collapsed space.

### Empty tags
- Show no pills plus a visible add-tag affordance.

### Missing optional metadata
- Hide absent salary, recruiter, and activity subsections.

### Save failure
- Preserve unsaved note text locally and show inline error messaging.
- Do not close the panel on failed note or tag writes.

## API Shape Requirements

Frontend work for T-008 and T-012 can proceed assuming `GET /jobs/:id` returns at least:

```json
{
  "data": {
    "id": "uuid",
    "title": "Backend Engineer",
    "company": "Example Inc",
    "location": "Chicago, IL",
    "source": "linkedin",
    "descriptionSnippet": "Go backend role",
    "applicationLink": "https://example.com/jobs/123",
    "recruiterName": "Jane Doe",
    "recruiterEmail": "jane@example.com",
    "salaryText": "$150k-$180k",
    "status": "new",
    "notes": "Interesting infra scope",
    "dateReceived": "2026-04-19T20:00:00Z",
    "fitState": "scored",
    "fitFlag": true,
    "fitScore": 84,
    "fitSummary": "Strong backend and infra match",
    "tags": [
      {
        "id": "uuid",
        "name": "priority"
      }
    ],
    "activities": [],
    "sourceMessages": []
  }
}
```

## Contract Decisions Locked Here

- One shared detail experience serves both read and edit behavior in MVP.
- Notes use explicit save/cancel, not autosave.
- Tags are edited directly in the detail view, not in a separate management screen.
- Status editing stays in the detail view as the primary edit surface.
- Mobile may switch from drawer to route, but content order and behaviors stay the same.

## Dependencies and Impact

### Depends on
- T-001 PM decision alignment
- T-004 dashboard contract locking

### Unblocks or stabilizes
- T-008 job details page structure
- T-012 notes and tag editing interactions
- Priya's frontend polish where selected-row and detail behavior need to feel coherent

## Recommended Follow-through

1. Add a short cross-reference from `specs/jobtrackr-api-contract.md` to this contract.
2. Use this section order in mock fixtures and frontend component naming.
3. Treat any future detail-view interaction change as a contract update, not a one-off UI tweak.
