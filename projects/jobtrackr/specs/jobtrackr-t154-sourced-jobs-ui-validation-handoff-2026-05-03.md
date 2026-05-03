# T-154 Sourced-Jobs UI Validation Handoff

## Purpose
Give Alice, Sam, and QA one execution-ready handoff for validating the T-152 sourced-jobs UI continuity slice from the T-154 lens without reopening product semantics.

This note is supporting sourced-jobs UI validation only. It does **not** replace the live QA gate pair:
- T-106: server-backed dashboard and jobs workspace sign-off
- T-095: source-email persistence fixture-backed sign-off

## Start here
1. Recover to the live project docs in this order:
   - `projects/jobtrackr/DEVELOPMENT_PLAN.md`
   - `projects/jobtrackr/PROJECT.md`
   - `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`
2. Open the T-154 validation lens:
   - `projects/jobtrackr/specs/jobtrackr-t152-sourced-jobs-ui-continuity-validation-lens-2026-05-03.md`
3. Keep implementation and QA inside the existing `/jobs` workspace. Do not create a separate sourced-jobs queue, new workflow status, or source-specific review surface.

## Validation data needed
Use seeded API data when available, or the stable mock fallback path if the API is unavailable.

Minimum dataset:
- one Email-only job
- one Indeed-only job
- one LinkedIn-only job
- one merged-source job with at least two sources
- one job with missing or unknown structured source metadata
- enough search, status, and location variety to combine source filters with existing filters

## Walkthrough sequence
Run the checks in this order so selection and filter continuity are easy to inspect.

1. Load `/jobs` with no filters.
   - Verify source chips are visible in rows/cards.
   - Verify source labels read as source provenance, not workflow, fit, or archive state.
2. Select a single-source job.
   - Verify the drawer preserves the same source label.
   - Open full detail, then return to `/jobs`; confirm the selected job and source context recover safely.
3. Select a merged-source job.
   - Verify compact primary-source plus `+N` treatment.
   - Verify accessible/title copy expands to the merged-source set, for example `Sources: LinkedIn and Email`.
4. Apply one source filter.
   - Verify active-filter summary names it as `Source: ...`.
   - Verify existing search/status/location filters still compose with AND semantics across filter families.
5. Apply multiple source filters.
   - Verify OR semantics within source filters and AND semantics with other filter families.
   - Remove one source chip and confirm the remaining source filter persists.
6. Force an empty result with a source filter plus another filter.
   - Verify empty copy names the active source filter.
   - Verify `Clear all filters` clears source filters together with existing filter families.
7. Change filters while a job is selected.
   - If the selected job remains visible, selection should preserve.
   - If hidden, selection should clear with the existing helper-copy pattern.
   - If filters are relaxed and the job returns, restore behavior should follow the workspace continuity contract.

## Verification commands
If touching or revalidating frontend code, run from `apps/web`:

```bash
npm test
npm run build
```

If validation depends on API-backed sourced records, also record the seeded data setup or API response source used for the walkthrough. If the API is unavailable and the mock fallback path is used, say that explicitly in the QA evidence.

## Evidence to capture
Before moving T-152 out of QA, capture:
- desktop source-chip screenshot or notes
- mobile card source-chip screenshot or notes
- merged-source `+N` screenshot or notes
- source-filter walkthrough notes covering OR-within-source and AND-across-filter-families behavior
- empty-result screenshot or notes naming source filters
- drawer/full-detail continuity notes for source labels and return context
- `npm test` and `npm run build` output if rerun

## Non-goals
Do not use this handoff to:
- close T-106 or T-095 without their dedicated sign-off evidence
- validate live Indeed or LinkedIn scraping
- change canonical workflow statuses
- treat low-fit, strong-fit, fit-ready, pending-fit, or source provenance as workflow state
- create a separate sourced-jobs queue outside `/jobs`

## Remaining gap
This handoff makes the T-154 validation lens execution-ready, but it is not browser or seeded API evidence by itself. T-152 should stay in QA until Sam or a QA pass records concrete walkthrough results against the implemented frontend branch.
