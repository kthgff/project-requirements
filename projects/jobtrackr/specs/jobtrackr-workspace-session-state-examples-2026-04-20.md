# JobTrackr Workspace Session-State Examples — 2026-04-20

## Purpose

Lock the deterministic session-state model for T-024 so Priya can implement row-selection continuity without ambiguity across filters, sorting, dashboard return flows, and responsive layouts.

This file extends:
- `specs/jobtrackr-workspace-ux-contract-2026-04-20.md`
- `specs/jobtrackr-list-detail-contract-examples-2026-04-20.md`

QA-ready scenario coverage for these transitions lives in:
- `specs/jobtrackr-workspace-continuity-qa-matrix-2026-04-20.md`

## Canonical Session-State Shape

```json
{
  "workspaceSession": {
    "activeFilters": {
      "q": "engineer",
      "status": ["new", "applied"],
      "source": ["linkedin"],
      "location": "remote"
    },
    "sort": "dateReceived:desc",
    "visibleJobIds": ["job_123", "job_124", "job_130"],
    "selectedJobId": "job_123",
    "lastSelectedJobId": "job_123",
    "selectionVisibleInResults": true,
    "selectionHelperMode": "none",
    "entryPoint": "dashboard",
    "detailPresentation": "drawer"
  }
}
```

## State Rules

- `selectedJobId` is the active visible selection.
- `lastSelectedJobId` is the most recent intentional user selection and survives temporary visibility loss inside the same session.
- `selectionVisibleInResults` is derived from whether `selectedJobId` is present in `visibleJobIds`.
- `selectionHelperMode` is one of `none`, `hidden_by_filters`, or `selection_not_restored`.
- Sorting never mutates `selectedJobId` or `lastSelectedJobId` by itself.
- Dashboard return preserves the whole workspace session snapshot.

## Example A: Sorting preserves selection by id

Before sort change:

```json
{
  "sort": "dateReceived:desc",
  "visibleJobIds": ["job_123", "job_124", "job_130"],
  "selectedJobId": "job_123",
  "lastSelectedJobId": "job_123",
  "selectionHelperMode": "none"
}
```

After sort change to `company:asc`:

```json
{
  "sort": "company:asc",
  "visibleJobIds": ["job_130", "job_123", "job_124"],
  "selectedJobId": "job_123",
  "lastSelectedJobId": "job_123",
  "selectionHelperMode": "none"
}
```

Expected behavior:
- the selected job moves position but stays selected
- drawer or detail state remains anchored to `job_123`
- list scroll restoration targets `job_123`, not its former row index

## Example B: Filter change hides selected row

Before restrictive filter:

```json
{
  "activeFilters": {
    "status": ["new", "applied"]
  },
  "visibleJobIds": ["job_123", "job_124"],
  "selectedJobId": "job_123",
  "lastSelectedJobId": "job_123",
  "selectionHelperMode": "none"
}
```

After adding `status=["applied"]` when `job_123` is `new`:

```json
{
  "activeFilters": {
    "status": ["applied"]
  },
  "visibleJobIds": ["job_124"],
  "selectedJobId": null,
  "lastSelectedJobId": "job_123",
  "selectionHelperMode": "hidden_by_filters"
}
```

Expected behavior:
- visible selection clears immediately
- detail surface closes
- filters stay intact
- helper copy explains that the previous selection is hidden by the current filters

Preferred helper copy:
`Your previously selected job is hidden by the current filters.`

## Example C: Relaxing filters restores the last selection

Starting from Example B, user clears the restrictive status filter:

```json
{
  "activeFilters": {},
  "visibleJobIds": ["job_123", "job_124", "job_130"],
  "selectedJobId": "job_123",
  "lastSelectedJobId": "job_123",
  "selectionHelperMode": "none"
}
```

Expected behavior:
- the most recent selected id restores automatically when visible again
- restoration targets only `lastSelectedJobId`
- the workspace must not guess a nearby replacement row

## Example D: Dashboard return restores snapshot without stale selection

User leaves the workspace with this stored snapshot:

```json
{
  "activeFilters": {
    "q": "engineer",
    "source": ["linkedin"]
  },
  "sort": "dateReceived:desc",
  "selectedJobId": null,
  "lastSelectedJobId": "job_123",
  "selectionHelperMode": "hidden_by_filters",
  "entryPoint": "dashboard"
}
```

If `job_123` still does not match on re-entry:
- restore filters and sort
- keep `selectedJobId = null`
- keep helper copy visible near the results summary
- do not reopen stale detail state

If the user re-enters after filters or data changes make `job_123` visible again:
- restore `selectedJobId = job_123`
- clear helper copy

## Example E: Mobile detail return behavior

On mobile, full-page detail may replace the list view.

State while opening detail:

```json
{
  "selectedJobId": "job_123",
  "lastSelectedJobId": "job_123",
  "detailPresentation": "page"
}
```

When returning from detail in the same session:
- restore the list with the same filters and sort
- scroll near `job_123`
- show `job_123` as selected if still visible
- if no longer visible, clear `selectedJobId`, keep `lastSelectedJobId`, and show helper copy

## Breakpoint Parity Rules

- Desktop and tablet follow the same preserve, clear, and restore rules.
- Mobile follows the same id-based logic even when detail is a route instead of a drawer.
- Layout changes do not change state semantics.

## QA Checklist

Use `specs/jobtrackr-workspace-continuity-qa-matrix-2026-04-20.md` when a reviewer needs breakpoint-by-breakpoint expected outcomes instead of narrative examples.


- Sorting keeps selection tied to job id.
- Restrictive filters clear visible selection but retain `lastSelectedJobId`.
- Relaxing filters restores only the last intentional selection.
- Dashboard return restores workspace snapshot before applying selection restoration rules.
- Mobile detail return scrolls near the selected job when it is still visible.
- Helper copy appears only when selection is hidden, not after ordinary sorts.

## Decisions Locked Here

- The workspace keeps both `selectedJobId` and `lastSelectedJobId`.
- Hidden selections clear visibly but remain restorable within the same session.
- Dashboard return restores snapshot state, not a fresh workspace.
- No fallback selection is allowed when the prior job is absent.
