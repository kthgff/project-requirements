# JobTrakr Job Source Schema and Provenance Spec

## Purpose
Define the minimum shared-schema additions and provenance model needed to support the new job source service while keeping scraped jobs and email-ingested jobs inside one canonical JobTrakr jobs model.

## Product constraints already locked
- scraped jobs write directly into the existing JobTrakr database
- scraped jobs share the same jobs model as email-ingested jobs
- duplicate jobs across sources should merge into one canonical record
- users must be able to distinguish Email, Indeed, and LinkedIn in the `/jobs` UI
- only normalized fields/data are stored for MVP
- archive remains separate from workflow state

## Schema strategy
Use one canonical `jobs` record plus source-specific linkage/provenance records.

That gives us:
- one user-facing job row
- multi-source traceability
- clean dedupe/merge behavior
- room to attach both email and scraped origins to the same job

## Recommended model

### 1. `jobs` table
Keep the canonical editable job record in `jobs`.

Add or confirm support for these fields:
- `id`
- `user_id`
- `title`
- `company`
- `location`
- `description_snippet`
- `description_text`
- `application_link`
- `salary_text`
- `employment_type`
- `seniority`
- `work_mode` (`remote`, `hybrid`, `onsite`, `unknown`)
- `status`
- `saved`
- `archived_at`
- `fit_state`
- `fit_flag`
- `fit_score`
- `fit_summary`
- `first_seen_at`
- `last_seen_at`
- `last_sourced_at`
- `primary_source`
- `source_count`
- `created_at`
- `updated_at`

### 2. `job_sources` table
Create a source-provenance table that can attach multiple origins to one job.

Recommended fields:
- `id`
- `job_id`
- `user_id`
- `source_type` (`email`, `scraped`)
- `source_platform` (`gmail`, `indeed`, `linkedin`)
- `source_record_id` nullable
- `external_job_id` nullable
- `source_url` nullable
- `normalized_source_url` nullable
- `search_profile_id` nullable
- `source_title` nullable
- `source_company` nullable
- `source_location` nullable
- `first_seen_at`
- `last_seen_at`
- `last_checked_at`
- `last_status` (`seen`, `missing`, `archived`, `error`)
- `confidence` nullable
- `created_at`
- `updated_at`

## Why this split is right
- `jobs` stays clean and product-facing
- `job_sources` preserves provenance without polluting every UI payload
- merged jobs can truthfully show multiple origins
- disappearing-source logic can act on a source record first, then archive the job if needed

## Canonical semantics

### `primary_source`
`jobs.primary_source` is the UI-default source label when a single source must be shown in tight list space.

Recommended precedence for MVP:
1. `linkedin`
2. `indeed`
3. `gmail`

Reason:
- scraped platforms are the newest intentional sourcing lane
- Gmail often acts as a trailing notification source for a job already discovered elsewhere

### `source_count`
Count of active provenance records attached to a job.

### `first_seen_at`
Earliest known time the opportunity entered JobTrakr from any source.

### `last_seen_at`
Latest time the opportunity was confirmed by any source.

### `last_sourced_at`
Latest time the scraping service refreshed this job from a scraped source.
Useful for freshness, operations, and future source-health debugging.

## Dedupe and merge rules
Apply in this order.

### Definitive merge signals
Merge when either matches exactly after normalization:
1. `external_job_id` + `source_platform`
2. `normalized_source_url`

### Fallback merge signal
Merge when all of the following match exactly after normalization:
- `user_id`
- `title`
- `company`
- `location`

### Merge behavior
When merging a new source into an existing job:
- create or update the `job_sources` row
- update `jobs.last_seen_at`
- update `jobs.last_sourced_at` for scraped-source refreshes
- increment or recompute `source_count`
- fill null canonical fields from stronger source data
- prefer higher-confidence normalized content over weaker content
- never overwrite user-edited notes, tags, status, saved state, or archive state

## Search profile linkage
Scraped records need profile traceability.

Recommended `search_profiles` support fields:
- `id`
- `user_id`
- `name`
- `title_query`
- `location_query`
- `remote_preference`
- `salary_min` nullable
- `salary_max` nullable
- `salary_text` nullable
- `seniority`
- `employment_types` JSON/array
- `include_keywords` JSON/array
- `exclude_keywords` JSON/array
- `enabled`
- `last_run_at`
- `next_run_at` nullable
- `created_at`
- `updated_at`

`job_sources.search_profile_id` should point at the matched profile for scraped-origin rows.

## API contract impact

### `GET /jobs` list payload
Keep the existing `source` field for backward compatibility, but source it from `jobs.primary_source`.

Add optional future-safe fields:
```json
{
  "source": "linkedin",
  "sourceCount": 2,
  "sources": ["linkedin", "gmail"]
}
```

### `GET /jobs/:id` detail payload
Add a provenance block for drawer/detail rendering:
```json
{
  "data": {
    "source": "linkedin",
    "sourceCount": 2,
    "sources": [
      {
        "type": "scraped",
        "platform": "linkedin",
        "url": "https://www.linkedin.com/jobs/view/...",
        "firstSeenAt": "2026-05-02T12:00:00Z",
        "lastSeenAt": "2026-05-02T12:00:00Z"
      },
      {
        "type": "email",
        "platform": "gmail",
        "firstSeenAt": "2026-05-02T13:00:00Z",
        "lastSeenAt": "2026-05-02T13:00:00Z"
      }
    ]
  }
}
```

## `/jobs` UI behavior support
The schema must support:
- source chips/filtering for Email / Indeed / LinkedIn
- full-width grid display with one primary source column/chip
- right-drawer detail view showing richer provenance
- default best-fit ordering without losing source visibility

## Archiving behavior
For MVP, source disappearance should start at the provenance layer.

Recommended behavior:
1. a source refresh marks a `job_sources` row as `missing` when the job disappears from that source
2. if all active provenance rows for a job are missing or archived, archive the job
3. preserve the job record for history
4. edge-case override for jobs with user workflow activity remains TBD

## Minimal operational fields
For light observability, add enough run-level traceability to diagnose daily failures.

Recommended `job_source_runs` table:
- `id`
- `user_id`
- `status` (`queued`, `running`, `completed`, `partial`, `failed`)
- `started_at`
- `finished_at`
- `profiles_scanned`
- `jobs_seen`
- `jobs_created`
- `jobs_merged`
- `jobs_archived`
- `retry_count`
- `error_summary` nullable
- `created_at`

This is intentionally shallow and does not require raw snapshot storage.

## Recommendation
Adopt:
- `jobs` as the canonical editable record
- `job_sources` as the provenance and dedupe layer
- `job_source_runs` as the minimal operational trace layer

That gives engineering a stable MVP model without fragmenting the product into separate email and scraping worlds.
