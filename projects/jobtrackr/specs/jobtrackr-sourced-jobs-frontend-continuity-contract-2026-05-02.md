# JobTrackr Sourced Jobs Frontend Continuity Contract — 2026-05-02

## Purpose

Lock the `/jobs` continuity expectations for sourced jobs before the Indeed/LinkedIn implementation changes the UI shape.

This is the canonical frontend contract for T-151. It extends the existing jobs workspace, list-to-detail, and detail-view contracts so Gmail, Indeed, and LinkedIn jobs remain one review surface instead of becoming separate source-specific queues.

## Source-of-truth relationship

Use this file with:
- `projects/jobtrackr/specs/jobtrackr-job-source-schema-and-provenance-spec-2026-05-02.md` for schema/provenance fields
- `projects/jobtrackr/specs/jobtrackr-api-contract.md` for API payload shape
- `projects/jobtrackr/specs/jobtrackr-workspace-ux-contract-2026-04-20.md` for filters, empty states, and selection continuity
- `projects/jobtrackr/specs/jobtrackr-list-detail-contract-examples-2026-04-20.md` for drawer/full-page parity
- `projects/jobtrackr/specs/jobtrackr-detail-view-contract-2026-04-20.md` for detail section order and edit rules

If this file conflicts with workflow or fit semantics, the canonical rule still wins: workflow state is only `new`, `interested`, `applied`, `interviewing`, `offer`, `rejected`; low-fit and strong-fit are fit signals; archive state is separate.

## Product goals

1. Keep one canonical jobs workspace for email and scraped jobs.
2. Make source provenance visible enough to trust without bloating the grid.
3. Explain merged-source jobs where users expect context: drawer and full detail.
4. Let users filter by source without breaking existing status/search/location/tag filters or selected-row continuity.
5. Preserve best-fit and newest ordering expectations while still making source freshness readable.

## Canonical source vocabulary

Use these user-facing labels:

| API/source value | User-facing label | Tight chip label |
|---|---|---|
| `gmail` / `email` | Email | Email |
| `indeed` | Indeed | Indeed |
| `linkedin` | LinkedIn | LinkedIn |

Rules:
- Do not show `scraped` as the primary user-facing label. `scraped` is an implementation type; the platform is the product label.
- If a platform is unknown, show `Other source` in detail and `Other` only in tight grid/card space.
- Source labels are provenance, not workflow status and not fit state.

## Expected list payload support

The jobs list should continue to support the existing `source` field as the primary source label.

Sourced-job rows may also include:

```json
{
  "source": "linkedin",
  "sourceCount": 2,
  "sources": ["linkedin", "gmail"],
  "firstSeenAt": "2026-05-02T12:00:00Z",
  "lastSeenAt": "2026-05-02T16:30:00Z",
  "lastSourcedAt": "2026-05-02T16:30:00Z"
}
```

List payload rules:
- `source` is the primary display source, derived from `jobs.primary_source`.
- `sourceCount` is the total active provenance count for the canonical job.
- `sources` is an ordered platform list for filter chips, compact multi-source badges, and accessibility labels.
- `firstSeenAt` is the discovery date when it differs from older `dateReceived` naming.
- `lastSeenAt`/`lastSourcedAt` are freshness metadata and should not replace workflow or archive state.

## Grid and table rendering

### Desktop grid/table

The source column stays compact:
- Single-source job: show one source chip, for example `LinkedIn`.
- Multi-source job: show primary chip plus a compact count, for example `LinkedIn +1`.
- The hover/title or accessible label should expand the count, for example `Sources: LinkedIn, Email`.
- Do not add separate columns for every source platform.
- Do not show raw URLs, source record ids, search profile ids, or run ids in the table.

Recommended row copy:
- Primary chip: `LinkedIn`
- Merged chip: `LinkedIn +1`
- Accessible label: `Sources: LinkedIn and Email. First seen May 2. Last seen May 2.`

### Mobile cards

Mobile cards should keep source context near the job title/company metadata:
- Single-source: `Source: Indeed`
- Multi-source: `Sources: Indeed + Email`
- If space is tight, prefer `Indeed +1` plus an accessible label over wrapping a long provenance block.

### Empty and loading states

Source filters participate in the existing empty-result contract:
- If a source filter causes zero results, empty copy should name the active source filter: `No jobs matched Source: LinkedIn with your current filters.`
- `Clear all filters` clears source filters together with status/search/location/tag filters.
- Loading skeletons should reserve only the compact source-chip area, not a detail provenance block.

## Source filters

Add source filters as another filter family inside the existing workspace filter contract.

Supported source filter values:
- `gmail`
- `indeed`
- `linkedin`

Filter composition:
- Multiple selected sources combine with OR semantics inside the source family.
- Source filters combine with status/search/location/tag/saved/archive filters using AND semantics.
- Source filters must not mutate status, saved, archived, fit, or selected-row state.

Example:

```text
/jobs?source=linkedin&source=gmail&status=new&q=engineer&page=1&pageSize=20
```

Meaning: jobs with LinkedIn OR Gmail provenance AND `new` status AND keyword match.

Filter chip rules:
- Show source chips in the active-filter summary as `Source: LinkedIn`, `Source: Email`, etc.
- Removing one source chip keeps the other active source filters.
- Clearing all filters removes source filters and follows the existing selection restoration rules.

Selection continuity:
- If a selected job remains visible after a source filter change, preserve `selectedJobId`.
- If a source filter hides the selected job, clear active selection and show the same helper-copy pattern used for other filters.
- If the filter is relaxed and the job re-enters the result set in the same session, restoration follows the existing workspace contract.

## Drawer provenance behavior

The drawer is the first place where merged provenance should become explicit.

Required drawer section:
- Section label: `Sources`
- Placement: after header/fit/workflow summary and before notes/tags, unless the detail-view contract later defines a stricter source section position.
- Single-source content: one row with platform, first seen, last seen, and source link when safe.
- Multi-source content: one row per provenance platform/source record, grouped under one explanatory sentence.

Suggested merged-source copy:

> Found from multiple sources. JobTrakr keeps one canonical job record and attaches each source for provenance.

Drawer source row fields:
- platform label (`LinkedIn`, `Indeed`, `Email`)
- source type only if helpful (`Email alert`, `Job board`)
- first seen date
- last seen date
- source link when present and safe to open
- search profile name only if it is user-facing and short

Matched search profile behavior:
- Show matched profile names in the drawer when available.
- If a job matches multiple profiles, show all profile names in a compact list or chip group.
- Disabled profiles may remain visible as historical match labels, with a subtle paused/disabled treatment if status is shown.
- Matched profile labels must not imply separate job records or workflow states.

Drawer exclusions:
- No raw Gmail message ids by default.
- No source-run ids.
- No dedupe confidence scores unless a future QA/debug mode explicitly asks for them.
- No copy implying each source is a separate job.

## Full detail provenance behavior

The full detail page should show the same provenance facts as the drawer, with more room for audit context.

Required full-detail behavior:
- Use the same `Sources` section heading as the drawer.
- Preserve the same source order and labels as the drawer.
- Include source links and date metadata when available.
- Include source email metadata under Email rows when available: sender, subject, received date.
- Explain merged-source behavior once per job, not once per source row.

Optional full-detail-only fields:
- search profile name
- all matched search profile names
- last checked date
- source status such as `Seen`, `Missing`, or `Error`, only when it helps explain freshness or archive behavior

Archive/freshness copy:
- A missing source does not mean the job workflow status changed.
- If all sources disappear and the job is archived, archive state is shown through archive UI, not through status.

## Source ordering expectations

For display order:
1. primary source first
2. remaining active sources by most recent `lastSeenAt`
3. inactive/missing sources last in full detail only

For primary source selection, follow the schema spec unless implementation has a better confidence signal:
1. LinkedIn
2. Indeed
3. Email/Gmail

## Best-fit and default ordering

Default ordering remains product/review oriented, not source oriented.

Rules:
- Do not sort primarily by source platform by default.
- Preserve current default newest/relevance ordering until the fit service changes it.
- When fit analysis exists, best-fit sorting may use fit score/rationale while source chips remain visible as context.
- `lastSeenAt` can break ties within otherwise equal sort groups but must not bury strong-fit jobs simply because they came from Email rather than LinkedIn.

## Canonical examples

### Single-source row

```json
{
  "id": "job_201",
  "title": "Staff Frontend Engineer",
  "company": "Northstar Health",
  "source": "linkedin",
  "sourceCount": 1,
  "sources": ["linkedin"],
  "status": "new",
  "fitState": "scored",
  "fitScore": 88
}
```

Grid: `LinkedIn`
Drawer/full detail: one `Sources` row for LinkedIn.

### Merged-source row

```json
{
  "id": "job_202",
  "title": "Platform Engineer",
  "company": "Acme Systems",
  "source": "linkedin",
  "sourceCount": 2,
  "sources": ["linkedin", "gmail"],
  "status": "interested",
  "fitState": "pending",
  "fitScore": null
}
```

Grid: `LinkedIn +1` with accessible label `Sources: LinkedIn and Email`.
Drawer/full detail: show the merged-source explanation and two source rows. Status remains `interested`; pending fit remains pending fit.

### Source-filtered selected job

Starting state:
- selected job has `sources: ["linkedin", "gmail"]`
- active filters are `source=linkedin` and `status=new`

If the user removes `source=linkedin` and adds `source=indeed`:
- the selected job is hidden unless it also has Indeed provenance
- clear active selection
- preserve filters
- show helper copy: `Your previously selected job is hidden by the current source filters.`

If the user then clears the source filter and the job returns:
- restore selection according to the workspace continuity contract
- do not reset status/search filters unless the user used `Clear all filters`

## Implementation checklist

Before frontend implementation is considered aligned:
- `/jobs` shows a compact source chip for every row/card.
- Multi-source jobs show a compact merged indicator in the grid/card.
- Drawer and full detail show the same source order and merged-source explanation.
- Source filters appear in the active-filter summary and compose with existing filters.
- Selected-row continuity behaves the same for source filters as for status/search/location filters.
- Workflow, fit, saved, and archive state are never inferred from source platform or source count.
- Tests cover single-source display, multi-source display, source-filter empty state, and selected-row clearing/restoration when source filters hide or reveal a job.
