# T-159 Sourced-Jobs UI Walkthrough Evidence Pointers — 2026-05-03

## Purpose

Give Marcus, Sam, and frontend QA one evidence path for executing the T-154 sourced-jobs UI walkthrough without reconstructing the expected proof from T-151, T-152, T-154, T-156, and T-157.

This note is a continuity/evidence pointer only. It does **not** replace the live QA gate pair:
- T-106: server-backed dashboard and jobs workspace sign-off
- T-095: source-email persistence fixture-backed sign-off

## Start here

1. Recover through the live project-root files:
   - `projects/jobtrackr/DEVELOPMENT_PLAN.md`
   - `projects/jobtrackr/PROJECT.md`
   - `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`
2. Open the execution walkthrough:
   - `projects/jobtrackr/specs/jobtrackr-t154-sourced-jobs-ui-validation-handoff-2026-05-03.md`
3. Pair it with the validation lens:
   - `projects/jobtrackr/specs/jobtrackr-t152-sourced-jobs-ui-continuity-validation-lens-2026-05-03.md`
4. Review the T-160 repo-side evidence pass before moving T-152 out of QA or requesting extra browser/mobile screenshots:
   - `projects/jobtrackr/specs/jobtrackr-t160-sourced-jobs-ui-validation-evidence-2026-05-03.md`
5. If screenshot proof is still required, use the T-161 visual capture handoff:
   - `projects/jobtrackr/specs/jobtrackr-t161-sourced-jobs-ui-visual-qa-capture-handoff-2026-05-03.md`

## Evidence anchor map

| Surface | Evidence to collect | Canonical reference |
|---|---|---|
| Grid/table source chips | Desktop note or screenshot showing Email, Indeed, LinkedIn, unknown/missing source, and merged `+N` treatment | T-151 contract, T-152 app handoff, T-154 walkthrough steps 1 and 3 |
| Mobile card source chips | Mobile-width note or screenshot showing the same normalized source vocabulary as desktop | T-151 contract, T-154 walkthrough steps 1 and 3 |
| Drawer continuity | Note or screenshot confirming the selected job keeps the same source label when the drawer opens/closes | T-154 walkthrough step 2 and Marcus trace assertions 1-2 |
| Full-detail continuity | Note or screenshot confirming `/jobs/:id` preserves source context and safely returns to the filtered `/jobs` workspace | T-154 walkthrough step 2 and Marcus trace assertions 2-4 |
| Source filters | Notes covering single-source filter, multi-source OR semantics, AND composition with search/status/location, and remove-one-chip behavior | T-154 walkthrough steps 4-5 |
| Empty states | Note or screenshot showing source-aware empty copy and `Clear all filters` clearing source filters with other filter families | T-154 walkthrough step 6 |
| Selection changes under filters | Notes confirming preserve/clear/restore behavior follows the workspace continuity contract, not a sourced-job-specific model | T-154 walkthrough step 7 and workspace UX contract |
| Non-state provenance | Explicit note that Email/Indeed/LinkedIn/merged-source metadata never changes workflow, fit, archive, or queue state | T-151 contract and T-154 non-goals |

## Minimum walkthrough dataset

Use seeded API records when available. If the API is unavailable, use the stable mock fallback path and state that clearly in QA evidence.

Minimum records:
- one Email-only job
- one Indeed-only job
- one LinkedIn-only job
- one merged-source job with at least two sources
- one job with missing or unknown structured source metadata
- enough search, status, and location variety to combine source filters with existing filter families

## Command evidence

When the app branch is safe to validate, run from `apps/web`:

```bash
npm test
npm run build
```

Record the branch and commit under test. If the app repo is dirty or contains unrelated work, do not treat that as T-152/T-154 failure by default; record the dirty state separately and only validate isolated source-chip/source-filter behavior that can be safely attributed to the sourced-jobs UI slice.

## Pass/fail notes template

Use this compact block in QA notes or Sam handoff comments:

```markdown
T-159 sourced-jobs UI continuity evidence
- App branch/commit:
- Data source: seeded API / mock fallback
- Desktop source chips: PASS/FAIL — notes:
- Mobile source chips: PASS/FAIL — notes:
- Merged `+N` source copy: PASS/FAIL — notes:
- Source filters OR-within-source and AND-across-families: PASS/FAIL — notes:
- Empty state names source filters and clear-all resets them: PASS/FAIL — notes:
- Drawer source continuity: PASS/FAIL — notes:
- Full-detail source continuity and return context: PASS/FAIL — notes:
- Selection preserve/clear/restore follows workspace contract: PASS/FAIL — notes:
- `npm test`: PASS/FAIL/not rerun — notes:
- `npm run build`: PASS/FAIL/not rerun — notes:
- Blockers:
```

## Explicit non-goals

Do not use this evidence path to:
- close T-106 or T-095 without their dedicated sign-off evidence
- validate live Indeed or LinkedIn scraping
- introduce a separate sourced-jobs queue
- create source-specific workflow statuses
- treat source provenance as fit, workflow, archive, or priority state

## T-159 completion note

T-159 is complete when this pointer is linked from the T-154 execution handoff, active QA coverage, and live frontend recovery docs. T-160 now records repo-side seeded-data walkthrough evidence against the implemented app branch. T-161 defines the optional Sam/QA visual browser and mobile screenshot capture path if final sign-off requires screenshots.
