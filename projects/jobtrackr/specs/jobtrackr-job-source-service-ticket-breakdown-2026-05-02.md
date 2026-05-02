# JobTrakr Job Source Service Ticket Breakdown

## Purpose
Break the new job source service workstream into GitHub-ready implementation tickets covering the separate service repo plus the shared app-side integration work.

## Ticket set

These tickets are now live in GitHub under `kthgff/project-requirements`.

### T-144 — Shared schema and provenance foundation for sourced jobs
**GitHub:** #87 — <https://github.com/kthgff/project-requirements/issues/87>
**Focus**
- add shared-schema support for sourced jobs
- define and migrate `job_sources`
- add minimal `job_source_runs`
- extend `search_profiles` support where needed
- preserve one canonical `jobs` record with multi-source provenance

**Dependencies**
- job source service schema/provenance spec

### T-145 — Search profile CRUD and self-serve scheduling contract
**GitHub:** #88 — <https://github.com/kthgff/project-requirements/issues/88>
**Focus**
- support self-serve profile creation/editing
- support multiple profiles per user
- support pause/disable behavior
- persist scheduling metadata needed for daily runs

**Dependencies**
- T-144

### T-146 — Bootstrap the new job source service repository and runtime foundation
**GitHub:** #89 — <https://github.com/kthgff/project-requirements/issues/89>
**Focus**
- create the new repo
- add service config, secrets contract, health endpoint, and minimal run logging
- prove safe database connectivity and dry-run execution path

**Dependencies**
- T-144

### T-147 — Implement Indeed full-detail sourcing connector
**GitHub:** #90 — <https://github.com/kthgff/project-requirements/issues/90>
**Focus**
- execute Indeed searches from saved profiles
- fetch matched full-detail pages
- normalize and write results into the shared jobs model

**Dependencies**
- T-144, T-145, T-146

### T-148 — Implement LinkedIn full-detail sourcing connector
**GitHub:** #91 — <https://github.com/kthgff/project-requirements/issues/91>
**Focus**
- execute LinkedIn searches from saved profiles
- fetch matched full-detail pages
- normalize and write results into the shared jobs model

**Dependencies**
- T-144, T-145, T-146

### T-149 — Implement cross-source dedupe and merge behavior
**GitHub:** #92 — <https://github.com/kthgff/project-requirements/issues/92>
**Focus**
- merge duplicates across Indeed, LinkedIn, and Gmail ingestion
- preserve source provenance on one canonical job record
- protect user-edited workflow fields during merges

**Dependencies**
- T-144, T-147, T-148

### T-150 — Add daily scheduling, retries, and archive-on-disappear behavior
**GitHub:** #93 — <https://github.com/kthgff/project-requirements/issues/93>
**Focus**
- run sourcing once per day per user
- retry failed runs up to 3 times
- mark disappeared source records and archive jobs when appropriate
- keep observability minimal but diagnosable

**Dependencies**
- T-145, T-147, T-148, T-149

### T-151 — Integrate sourced jobs into `/jobs` with source visibility and profile management UX
**GitHub:** #94 — <https://github.com/kthgff/project-requirements/issues/94>
**Focus**
- add app-side search profile management UI
- surface source chips/filters for Email, Indeed, LinkedIn
- render provenance cleanly in the right-side job drawer
- keep the full-width grid plus drawer flow canonical

**Dependencies**
- T-145, T-149

### T-152 — Validate best-fit-first sourced job experience and MVP release readiness
**GitHub:** #95 — <https://github.com/kthgff/project-requirements/issues/95>
**Focus**
- verify sourced jobs feel native in `/jobs`
- validate best-fit-first ordering behavior at the product level
- document runbook, QA checklist, and known limitations

**Dependencies**
- T-150, T-151

## Recommendation
Create these as GitHub issues in `kthgff/project-requirements` now, then use them as the handoff spine for engineering and QA.