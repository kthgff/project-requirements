# JobTrackr Workspace Continuity QA Matrix — 2026-04-20

## Purpose

Turn the locked workspace continuity rules into one QA-ready matrix so frontend and QA can validate deterministic selection behavior without inferring missing cases.

This matrix is the implementation-facing test companion to:
- `specs/jobtrackr-workspace-ux-contract-2026-04-20.md`
- `specs/jobtrackr-workspace-session-state-examples-2026-04-20.md`
- `specs/jobtrackr-list-detail-contract-examples-2026-04-20.md`

## Canonical Assertions

- Selection is always tracked by job id, never row index.
- `selectedJobId` clears immediately when the selected job leaves `visibleJobIds`.
- `lastSelectedJobId` survives same-session visibility loss and is the only candidate for automatic restoration.
- Sorting changes preserve selection.
- Dashboard return restores the workspace snapshot before restore logic decides whether selection can come back.
- Mobile, tablet, and desktop share the same state semantics even when presentation differs.
- Helper copy appears only when the previous selection is hidden or intentionally not restored.

## QA Matrix

| Scenario ID | Flow | Breakpoint | Starting state | User action | Expected session-state result | Expected UI result |
|---|---|---|---|---|---|---|
| WC-01 | Sort preserves selection | Desktop / Tablet / Mobile | `selectedJobId=job_123`, `lastSelectedJobId=job_123`, row visible | Change sort from `dateReceived:desc` to `company:asc` | `selectedJobId` stays `job_123`, `lastSelectedJobId` stays `job_123`, helper mode stays `none` | Same job remains selected after reorder; detail stays anchored to `job_123` |
| WC-02 | Filter hides selection | Desktop / Tablet / Mobile | `selectedJobId=job_123`, `lastSelectedJobId=job_123`, row visible | Apply filter that removes `job_123` from results | `selectedJobId=null`, `lastSelectedJobId=job_123`, helper mode becomes `hidden_by_filters` | Detail closes, selection styling clears, helper copy explains the previous job is hidden |
| WC-03 | Relaxed filters restore selection | Desktop / Tablet / Mobile | From WC-02 | Remove or relax the restrictive filter so `job_123` is visible again | `selectedJobId=job_123`, `lastSelectedJobId=job_123`, helper mode returns to `none` | Previous selection restores automatically; no replacement row guessing |
| WC-04 | Dashboard return with visible prior selection | Desktop / Tablet | Stored snapshot includes visible `lastSelectedJobId=job_123` | Leave to dashboard, then re-enter jobs | Snapshot restores first, then `selectedJobId=job_123` | Filters, sort, and selected row all return together |
| WC-05 | Dashboard return with hidden prior selection | Desktop / Tablet / Mobile | Stored snapshot includes `selectedJobId=null`, `lastSelectedJobId=job_123`, helper `hidden_by_filters` | Leave to dashboard, then re-enter jobs while `job_123` is still filtered out | Filters and sort restore; `selectedJobId` stays `null`; helper stays `hidden_by_filters` | No stale detail reopen; inline helper copy remains near results summary |
| WC-06 | Clear all filters after hidden selection | Desktop / Tablet / Mobile | Hidden-selection state with `lastSelectedJobId=job_123` | Click `Clear all filters` | If `job_123` exists in full dataset, restore `selectedJobId=job_123`; otherwise keep `selectedJobId=null`; helper clears only when selection restores | Workspace returns to default dataset; selection restores only when valid |
| WC-07 | Non-destructive filter edit keeps selection | Desktop / Tablet / Mobile | `selectedJobId=job_123` is still in result set | Add or remove another filter that does not hide `job_123` | `selectedJobId` and `lastSelectedJobId` both stay `job_123`; helper remains `none` | Selected state remains intact with updated result count |
| WC-08 | Mobile detail return keeps list context | Mobile | `selectedJobId=job_123`, detail shown as page | Tap back from detail to list | If `job_123` still visible, keep `selectedJobId=job_123`; otherwise clear it and retain `lastSelectedJobId=job_123` | Return lands near the same card; hidden selections show helper copy instead of stale detail |
| WC-09 | Breakpoint change does not mutate state | Desktop to Tablet / Tablet to Mobile | Active workspace state with filters and selected job | Resize or reopen at a different breakpoint in same session | No state mutation caused by layout change alone | Same filters, sort, helper mode, and selected job semantics persist |
| WC-10 | No fallback selection when prior job stays absent | Desktop / Tablet / Mobile | `lastSelectedJobId=job_123`, `selectedJobId=null`, `job_123` absent | Change sort, paginate, or re-enter workspace without making `job_123` visible | `selectedJobId` remains `null`; `lastSelectedJobId` remains `job_123` | Workspace never auto-selects a nearby or first row |

## Scenario Notes

### WC-02 and WC-05 helper copy
Preferred copy:
`Your previously selected job is hidden by the current filters.`

Optional secondary action:
- `Clear all filters`

### WC-06 clear-all behavior
`Clear all filters` resets filter state, but it is not allowed to invent a new selection. It may only restore `lastSelectedJobId` when that exact job is visible again in the reset dataset.

### WC-08 mobile parity
Mobile may use route-based detail instead of a drawer, but the session state must still behave like the desktop and tablet model.

## Minimum QA Coverage Expectations

A change touching workspace selection continuity is not ready for sign-off unless QA can demonstrate:
- one preserved-selection case
- one hidden-selection case
- one restored-selection case
- one dashboard return case
- one mobile return case
- one proof that no fallback row is auto-selected

## Cross-Reference Guidance

- Frontend stories should point to this matrix for expected continuity outcomes.
- QA test cases can reference the Scenario IDs directly.
- If a future spec changes selection semantics, this matrix must be updated in the same change.