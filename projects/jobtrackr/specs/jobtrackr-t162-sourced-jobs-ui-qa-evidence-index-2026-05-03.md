# T-162 Sourced-Jobs UI QA Evidence Index — 2026-05-03

## Purpose

Give Sam one current QA-facing index for the T-154 sourced-jobs UI continuity validation chain, so source-chip, merged-provenance, source-filter, drawer/full-detail, return-context, and visual-capture evidence can be collected without reconstructing the handoff path from multiple hourly notes.

This index supports T-152/T-154 sourced-jobs UI validation only. It does **not** replace the live QA gate pair:
- T-106: server-backed dashboard and jobs workspace sign-off
- T-095: source-email persistence fixture-backed sign-off

## Start here

1. Recover through the live project-root files:
   - `projects/jobtrackr/DEVELOPMENT_PLAN.md`
   - `projects/jobtrackr/PROJECT.md`
   - `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`
2. Use this index as the current sourced-jobs UI validation map.
3. Keep all checks inside the canonical `/jobs` workspace. Source provenance is metadata only; it must not become workflow, fit, archive, priority, or queue state.

## Evidence chain

| Step | File | Use it for | Current status |
|---|---|---|---|
| Contract | `projects/jobtrackr/specs/jobtrackr-sourced-jobs-frontend-continuity-contract-2026-05-02.md` | Canonical source-chip, merged-provenance, source-filter, drawer/detail, and continuity semantics | Locked by T-151 |
| Validation lens | `projects/jobtrackr/specs/jobtrackr-t152-sourced-jobs-ui-continuity-validation-lens-2026-05-03.md` | QA expectations for T-152 source chips and source-filter behavior | Published by T-154 |
| Walkthrough handoff | `projects/jobtrackr/specs/jobtrackr-t154-sourced-jobs-ui-validation-handoff-2026-05-03.md` | Ordered `/jobs` walkthrough and validation data requirements | Execution-ready |
| Evidence pointers | `projects/jobtrackr/specs/jobtrackr-t159-sourced-jobs-ui-walkthrough-evidence-pointers-2026-05-03.md` | Screenshot/note template, pass/fail structure, and evidence expectations | Published |
| Repo-side evidence | `projects/jobtrackr/specs/jobtrackr-t160-sourced-jobs-ui-validation-evidence-2026-05-03.md` | Clean app branch/commit, fixture coverage, and `npm test && npm run build` proof | PASS on app commit `ca0459c` |
| Visual capture handoff | `projects/jobtrackr/specs/jobtrackr-t161-sourced-jobs-ui-visual-qa-capture-handoff-2026-05-03.md` | Optional desktop/mobile screenshot capture path if Sam wants visual proof | Ready for Sam/QA |

## Current evidence baseline

Use the T-160 evidence note as the repo-side baseline unless a newer app commit is explicitly recorded:

- App branch: `feat/jobtrackr-sourced-jobs-ui-continuity`
- App commit: `ca0459c`
- Data source: mock fallback fixtures in `apps/web/lib/mockJobs.ts`
- Verification command: `npm test && npm run build` from `apps/web`
- Result: PASS — 18 frontend tests passed and the Next.js production build completed successfully

The primary `Documents/jobtrackr` checkout had unrelated dirty work during T-160, so sourced-jobs UI evidence should stay anchored to the clean T-152 worktree/commit unless Sam intentionally validates a newer branch.

## Artifact slots Sam can fill

Use this compact checklist when attaching QA evidence:

```markdown
T-162 sourced-jobs UI QA evidence index
- App branch/commit:
- Data source: seeded API / mock fallback
- Verification command source: T-160 / rerun on newer commit
- Desktop source chips: PASS/FAIL — evidence:
- Mobile source chips: PASS/FAIL — evidence:
- Merged `+N` provenance and accessible copy: PASS/FAIL — evidence:
- Source filters and active summary: PASS/FAIL — evidence:
- Source-aware empty state and clear-all recovery: PASS/FAIL — evidence:
- Drawer source continuity: PASS/FAIL — evidence:
- Full-detail source continuity and return context: PASS/FAIL — evidence:
- Selection behavior with source filters: PASS/FAIL — evidence:
- Provenance stayed metadata-only: PASS/FAIL — evidence:
- Blockers:
```

## Pass boundaries

A sourced-jobs UI validation pass is good enough to hand back for QA decision when it includes:

- the app branch/commit under review
- the data source used, especially whether it was seeded API data or mock fallback fixtures
- command evidence from T-160 or a fresh `npm test && npm run build` rerun on the reviewed commit
- desktop and mobile source-chip coverage, or explicit notes explaining why screenshots were not required
- merged-source `+N` and expanded accessible/title copy coverage
- source-filter behavior covering OR within source filters and AND across search/status/location/source filter families
- source-aware empty-state and `Clear all filters` recovery coverage
- drawer/full-detail source context plus return-context continuity coverage
- an explicit statement that source provenance stayed metadata-only and did not mutate workflow, fit, archive, or queue state

## Still not covered by this index

Do not use this index to:

- close T-106 without the T-116 persisted-jobs workspace sign-off checklist
- close T-095 without the T-102 source-email persistence sign-off checklist
- validate live Indeed or LinkedIn scraping
- introduce a separate sourced-jobs queue outside `/jobs`
- create source-specific workflow statuses
- treat Email, Indeed, LinkedIn, merged-source chips, or unknown-source fallback as fit/workflow/archive state

## Remaining gap

T-160 already supplies clean repo-side fixture and command evidence. T-161 supplies the visual capture path. The remaining gap is a Sam/QA decision: either accept the repo-side evidence as enough for the T-152 sourced-jobs UI handoff or attach desktop/mobile walkthrough screenshots/notes using the artifact slots above.
