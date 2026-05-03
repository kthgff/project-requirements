# T-158 Source-Service Provenance Validation Handoff

## Purpose
Give Frank, Sam, and QA one backend/shared-contract recovery path for the current sourced-job provenance spine without falling back to source-email-only notes or inventing a separate sourced-jobs queue.

This is implementation-facing validation guidance. It does **not** replace the two live QA gates:
- T-106: server-backed dashboard and jobs workspace sign-off
- T-095: source-email persistence fixture-backed sign-off

## Source-of-truth inputs
- `projects/jobtrackr/DEVELOPMENT_PLAN.md` — T-144 through T-157 completion and QA state
- `projects/jobtrackr/specs/jobtrackr-job-source-schema-and-provenance-spec-2026-05-02.md` — canonical shared schema and provenance model
- `projects/jobtrackr/specs/jobtrackr-t144-sourced-jobs-schema-provenance-validation-lens-2026-05-02.md` — schema/provenance QA lens
- `projects/jobtrackr/specs/jobtrackr-t152-sourced-jobs-ui-continuity-validation-lens-2026-05-03.md` — UI source-chip/source-filter validation lens
- `projects/jobtrackr/specs/jobtrackr-t154-sourced-jobs-ui-validation-handoff-2026-05-03.md` — ordered `/jobs` source continuity walkthrough
- `projects/jobtrackr/specs/jobtrackr-source-email-persistence-signoff-checklist-2026-04-22.md` — live T-095 provenance-first sign-off path

## Validation order
Use this order when recovering Frank's backend/shared-contract lane:

1. **T-144 schema/provenance foundation** — confirm canonical `jobs`, `job_sources`, `job_source_runs`, and `search_profiles` remain the shared model.
2. **T-149 cross-source merge behavior** — confirm Gmail, Indeed, and LinkedIn origins attach provenance to one canonical job rather than forking source-specific records.
3. **T-150 LinkedIn plus existing Indeed connector behavior** — confirm provider fixture runs normalize into canonical jobs and source provenance through the source-service runtime.
4. **T-155 source-email debug-read alignment** — confirm Gmail/source-email provenance remains inspectable through `GET /api/v1/source-emails` before falling back to SQL.
5. **T-154/T-157 frontend continuity bridge** — confirm backend provenance fields are sufficient for `/jobs` source chips, merged-source labels, source filters, drawer continuity, and full-detail continuity.

## Backend assertions
- [ ] Sourced jobs persist into canonical `jobs` rows only; there is no parallel scraped-job workflow table.
- [ ] Distinct Gmail, Indeed, and LinkedIn origins create or refresh distinct `job_sources` rows for the same canonical job when merge keys match.
- [ ] Merge precedence stays deterministic: Gmail message identity where relevant, normalized source/application URL, then normalized title/company/location fallback.
- [ ] Source run records in `job_source_runs` capture status, timestamps, provider/platform, profile/run linkage, and useful result counts.
- [ ] Provider fixture connectors can run without live-site scraping and still produce inspectable run/provenance evidence.
- [ ] User-managed job state, tags, notes, archive state, and canonical workflow status are not overwritten by source discovery or provenance refresh.
- [ ] Missing fit analysis remains `pending-fit`/nullable fit behavior; provider provenance never becomes a fit or workflow state.

## API/debug inspection assertions
- [ ] `GET /api/v1/source-emails` remains the source-email debug-read path for T-095/T-108 inspection with `matchedAsJob`, `fromEmail`, `search`, and `limit` filters.
- [ ] Sourced-job provenance remains inspectable through API responses or seeded database checks without requiring manual reconstruction from run logs only.
- [ ] API responses feeding `/jobs` include enough source metadata for Email/Indeed/LinkedIn chips, merged `+N` labels, source filters, and drawer/full-detail source context.
- [ ] The debug/read contract distinguishes source provenance from workflow, archive, and fit-signal state.

## Evidence to capture before closing sourced-job provenance QA follow-through
- API test output for the source-service and persistence packages, if rerun during QA.
- Seeded or fixture-backed provider run output showing `job_source_runs` lifecycle fields.
- Seeded or fixture-backed canonical job with multiple `job_sources` rows across Gmail, Indeed, and LinkedIn.
- Debug-read evidence for source-email provenance through `GET /api/v1/source-emails` when Gmail-origin jobs are involved.
- `/jobs` payload or frontend mapping evidence showing source metadata is sufficient for T-154/T-157 continuity checks.

## Remaining gap
T-158 aligns the backend/shared-contract validation path and links the current provenance handoff set, but it does not execute the app repo test suite or seeded provider runs. Sam or QA still needs concrete fixture/run evidence before moving the sourced-job provenance follow-through out of QA.
