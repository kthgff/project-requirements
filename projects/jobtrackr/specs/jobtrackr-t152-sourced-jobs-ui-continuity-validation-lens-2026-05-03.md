# T-152 Sourced Jobs UI Continuity QA Validation Lens

## Purpose
Give QA one implementation-facing checklist for validating the T-152 sourced-jobs UI continuity slice without reconstructing expectations from the frontend commit, README notes, or the T-151 contract.

T-152 is supporting sourced-jobs UI coverage. It does **not** replace the live QA gate pair:
- T-106: server-backed dashboard and jobs workspace sign-off
- T-095: source-email persistence fixture-backed sign-off

## Source-of-truth inputs
- `projects/jobtrackr/DEVELOPMENT_PLAN.md` — T-152/T-154 rows and current QA state
- `projects/jobtrackr/specs/jobtrackr-sourced-jobs-frontend-continuity-contract-2026-05-02.md` — canonical source chips, merged-source explanation, and source-filter contract
- `projects/jobtrackr/specs/jobtrackr-workspace-ux-contract-2026-04-20.md` — filter, empty-state, and row-selection continuity rules
- `projects/jobtrackr/specs/jobtrackr-list-detail-contract-examples-2026-04-20.md` — drawer/full-detail continuity examples
- `projects/jobtrackr/stories/jobtrackr-in-progress-test-cases-2026-04-19.md` — active QA coverage, especially TC-2195 through TC-2197

## Validation scope
Use this lens to validate that T-152 exposed sourced-job provenance inside the existing `/jobs` workspace:
- compact Email/Indeed/LinkedIn chips render for single-source jobs
- merged-source jobs show deterministic `+N` treatment without adding a separate source-specific queue
- source filters compose with existing search/status/location filters
- source-aware empty states and active-filter summaries stay readable
- mock fallback and API-backed data use the same canonical source vocabulary

Out of scope for this lens:
- completing T-106 or T-095 QA sign-off
- validating live Indeed or LinkedIn scraping
- changing canonical workflow, fit, archive, or row-selection semantics
- validating backend dedupe beyond what is visible in frontend source metadata

## Fixture/data setup checklist
Use seeded API data or stable mock fallback data; do not require live scraping.

- [ ] Include at least one Email-only job.
- [ ] Include at least one Indeed-only job.
- [ ] Include at least one LinkedIn-only job.
- [ ] Include at least one merged-source job with two or more sources, such as LinkedIn + Email.
- [ ] Include at least one row with missing or unknown source metadata to verify safe fallback copy.
- [ ] Include enough status/search/location variety to verify source filters compose with existing filters.

## `/jobs` chip and card validation checklist
- [ ] Desktop rows show one compact chip for single-source jobs, using `Email`, `Indeed`, or `LinkedIn` labels.
- [ ] Desktop rows show primary source plus a compact `+N` label for merged-source jobs.
- [ ] Mobile cards keep source context near title/company metadata without wrapping into a bulky provenance block.
- [ ] Accessible labels or title text expand merged-source context, for example `Sources: LinkedIn and Email`.
- [ ] Unknown source metadata degrades to `Other`/`Other source` rather than blank UI.
- [ ] Source labels do not appear as workflow status, fit state, or archive state.

## Source-filter validation checklist
- [ ] Source filter options are limited to the canonical user-facing platforms: Email, Indeed, and LinkedIn.
- [ ] Selecting multiple sources behaves as OR inside the source family.
- [ ] Source filters combine with search/status/location filters using AND semantics across filter families.
- [ ] Active-filter summaries show source filters as source filters, for example `Source: LinkedIn`.
- [ ] Removing one source chip preserves other active source filters.
- [ ] `Clear all filters` clears source filters together with existing filter families.
- [ ] Empty-result copy names source filters when they contribute to zero results.

## Selection and navigation continuity checklist
- [ ] If the selected job remains visible after source filters change, `selectedJobId` is preserved.
- [ ] If source filters hide the selected job, active selection clears with the existing helper-copy pattern.
- [ ] If filters are relaxed and the job re-enters the visible result set, restore behavior follows the existing workspace contract.
- [ ] Opening drawer or full detail from a sourced-job row preserves the same primary/merged-source labels.
- [ ] Returning from full detail does not lose the active source-filter context.

## API-backed versus mock-fallback checks
- [ ] With the API available, source chips and filters reflect API source metadata from persisted jobs.
- [ ] With the API unavailable, mock fallback keeps the same source-chip labels and filter affordances.
- [ ] Pending-fit or workflow-normalization notices remain visible and do not conflict with source-provenance copy.
- [ ] The UI does not infer source provenance from freeform text when structured source metadata is missing.

## QA evidence to capture before moving T-152 out of QA
- screenshot or notes for single-source and merged-source chip rendering on desktop and mobile
- source-filter walkthrough showing source + search/status/location composition
- empty-result screenshot or notes where active source filters are named
- selection continuity notes for preserve, clear, and restore behavior after source-filter changes
- test/build command output from `apps/web`, if rerun during QA
- any mismatch between implementation behavior and `jobtrackr-sourced-jobs-frontend-continuity-contract-2026-05-02.md`

## Remaining gap
This document makes the T-152 validation path explicit, but it is not executed QA evidence. T-152 should stay in QA until Sam or a QA pass records concrete UI walkthrough results against the implemented frontend branch.
