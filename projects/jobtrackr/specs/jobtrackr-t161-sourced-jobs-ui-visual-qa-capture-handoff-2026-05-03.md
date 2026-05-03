# T-161 Sourced-Jobs UI Visual QA Capture Handoff — 2026-05-03

## Purpose

Give Sam and frontend QA one Marcus-owned visual capture path for the T-154/T-159/T-160 sourced-jobs UI continuity evidence gap.

This handoff is for screenshot/visual evidence only. It does **not** replace the live QA gate pair:
- T-106: server-backed dashboard and jobs workspace sign-off
- T-095: source-email persistence fixture-backed sign-off

## Start here

1. Recover through the live project-root files:
   - `projects/jobtrackr/DEVELOPMENT_PLAN.md`
   - `projects/jobtrackr/PROJECT.md`
   - `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`
2. Open the T-162 QA evidence index, then the execution handoff and evidence notes:
   - `projects/jobtrackr/specs/jobtrackr-t162-sourced-jobs-ui-qa-evidence-index-2026-05-03.md`
   - `projects/jobtrackr/specs/jobtrackr-t154-sourced-jobs-ui-validation-handoff-2026-05-03.md`
   - `projects/jobtrackr/specs/jobtrackr-t159-sourced-jobs-ui-walkthrough-evidence-pointers-2026-05-03.md`
   - `projects/jobtrackr/specs/jobtrackr-t160-sourced-jobs-ui-validation-evidence-2026-05-03.md`
3. Use this note only to capture the remaining browser/mobile visual proof Sam may need before moving T-152 sourced-jobs UI continuity out of QA.

## App and data baseline

Preferred validation target:

- App branch: `feat/jobtrackr-sourced-jobs-ui-continuity`
- Evidence commit: `ca0459c`
- Clean worktree evidence source: `projects/jobtrackr/specs/jobtrackr-t160-sourced-jobs-ui-validation-evidence-2026-05-03.md`
- Data source: seeded mock fallback records in `apps/web/lib/mockJobs.ts`, unless seeded API data is available and explicitly recorded

If validating a different branch or commit, record the branch/commit and whether unrelated local changes are present before treating a visual issue as a T-152 regression.

## Required capture set

Capture screenshots or precise notes for these surfaces, in this order:

1. **Desktop `/jobs` source chips**
   - Width: desktop/table view.
   - Show Email, Indeed, LinkedIn, and merged `+N` chip treatment if possible.
   - Confirm source labels read as provenance metadata only, not workflow, fit, archive, or queue state.

2. **Mobile `/jobs` card source chips**
   - Width: narrow/mobile card view.
   - Show that mobile cards use the same source vocabulary and compact merged-source treatment as desktop.

3. **Merged-source accessible copy**
   - Capture or note the expanded label/title copy for a merged-source job, for example `Sources: LinkedIn and Email`.
   - Confirm the compact `+N` badge does not hide the meaning from assistive or hover/title copy.

4. **Source filters and active filter summary**
   - Apply one source filter and capture the active summary.
   - Apply multiple source filters and note OR semantics within source filters plus AND semantics with search/status/location.
   - Remove one source chip and confirm the remaining source filter stays active.

5. **Source-aware empty state**
   - Combine source filters with another filter to force zero results.
   - Capture empty copy naming the active source filter.
   - Confirm `Clear all filters` resets source filters with the other filter families.

6. **Drawer continuity**
   - Select a job and open the drawer.
   - Capture that the drawer preserves the same source label/provenance context as the grid/card.
   - Confirm drawer source context does not mutate workflow or fit presentation.

7. **Full-detail and return-context continuity**
   - From the selected job, open `/jobs/:id`.
   - Capture or note that the full detail view preserves source context.
   - Return to `/jobs` and confirm the filtered context, selected job behavior, and source chips recover according to the existing workspace continuity contract.

## Evidence template

Use this block in the QA note, PR comment, or Sam handoff:

```markdown
T-161 visual sourced-jobs UI capture
- App branch/commit:
- Data source: seeded API / mock fallback
- Browser/device widths:
- Desktop source chips: PASS/FAIL — screenshot/note:
- Mobile source chips: PASS/FAIL — screenshot/note:
- Merged `+N` accessible copy: PASS/FAIL — screenshot/note:
- Source filters and active summary: PASS/FAIL — screenshot/note:
- Source-aware empty state: PASS/FAIL — screenshot/note:
- Drawer source continuity: PASS/FAIL — screenshot/note:
- Full-detail source continuity and return context: PASS/FAIL — screenshot/note:
- Provenance stayed metadata-only: PASS/FAIL — note:
- Blockers:
```

## Verification command reminder

T-160 already records green repo-side command evidence for commit `ca0459c`:

```bash
npm test && npm run build
```

Rerun from `apps/web` only if the visual capture uses a different branch/commit or if local app files changed before capture.

## Non-goals

Do not use this handoff to:

- close T-106 or T-095 without their dedicated sign-off evidence
- validate live Indeed or LinkedIn scraping
- create a separate sourced-jobs queue outside `/jobs`
- introduce source-specific workflow statuses
- treat Email, Indeed, LinkedIn, merged-source chips, or unknown-source fallback as workflow, fit, archive, priority, or queue state

## Completion note

T-161 is complete when this visual capture handoff is linked from the T-154/T-159/T-160/T-162 evidence path, active QA coverage, and live frontend recovery docs, and `DEVELOPMENT_PLAN.md` records the task as completed or handed to Sam for QA.
