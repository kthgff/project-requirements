# T-116 T-106 Persisted-Jobs Workspace Sign-off Checklist — 2026-04-23

## Purpose
Give Sam one implementation-facing checklist for final QA validation of T-106 without reconstructing the current proof from plan notes, README handoff copy, and active QA coverage.

## Use this with
- Active QA coverage: `projects/jobtrackr/stories/jobtrackr-in-progress-test-cases-2026-04-19.md`
- QA blocker checklist: `projects/jobtrackr/specs/jobtrackr-qa-blocker-checklist-2026-04-19.md`
- Live handoff package: `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`
- Live plan: `projects/jobtrackr/DEVELOPMENT_PLAN.md`

## Task under sign-off
- Task: `T-106`
- Current implementation branch: `feat/jobtrackr-persisted-jobs-workspace`
- Current evidence commit: `858127d` (`feat(web): wire persisted jobs into dashboard and workspace`)
- Evidence scope at that commit:
  - wires `/dashboard` and `/jobs` to the persisted-jobs data layer
  - adds API-mapping coverage in `apps/web/lib/jobApiMapping.test.ts`
  - updates `apps/web/README.md` for the server-backed workspace handoff

## Preconditions
- Repo available at `~/Documents/jobtrackr`
- Web workspace available at `~/Documents/jobtrackr/apps/web`
- API workspace available at `~/Documents/jobtrackr/apps/api`
- One authenticated test user is available
- One deterministic API-backed job dataset is available with:
  - at least one canonical persisted job
  - at least one row with missing fit data
  - at least one row that exercises workflow normalization or canonicalization notice handling
- A safe API-unavailable mode is available, such as stopping the API locally or pointing the frontend at an unreachable jobs endpoint

## Fast repo-side verification
Run from `~/Documents/jobtrackr/apps/web`:

```bash
npm test
npm run build
```

Expected:
- `npm test` passes
- `npm run build` passes
- No new dashboard or jobs-workspace regressions appear before UI QA starts

## UI sign-off steps
### A. API-up persisted-jobs verification
1. Start the API and web app with persisted jobs available.
2. Sign in through the authenticated shell.
3. Open `/dashboard`.
4. Open `/jobs` from the authenticated app.
5. Inspect rendered rows, source labels, fallback notices, and navigation behavior.

### B. API-down mock-fallback verification
1. Make the jobs API unavailable while keeping the web app running.
2. Start from an authenticated session.
3. Open `/dashboard` and `/jobs` again.
4. Verify the UI still renders safely and calls out fallback behavior.

### C. Result-set fallback-reason verification
1. Use an API-backed dataset that includes:
   - one row with missing fit data
   - one row whose workflow value required canonical normalization before render
2. Open `/dashboard` and `/jobs`.
3. Compare the visible fallback messaging and row output.

## Required assertions
### A. Persisted-jobs path is live when the API is available
- `/dashboard` and `/jobs` both load server-backed jobs instead of behaving like mock-only screens
- Result rows show canonical workflow values only
- Source labels and result-set notices remain visible without breaking table readability

### B. Mock fallback is safe when the API is unavailable
- The authenticated shell does not blank, crash, or trap the user on an error-only screen
- Mock fallback is visibly labeled as fallback behavior
- Dashboard-to-jobs navigation and row-selection continuity still work well enough for QA walkthroughs

### C. Fallback reasons stay canonical
- Missing fit data renders as pending-fit or equivalent canonical pending messaging
- Legacy workflow aliases do not surface raw in the UI
- Visible notices explain normalized workflow or missing-fit fallback without implying stale workflow states

## Suggested inspection targets
At minimum, confirm:
- `/dashboard` can show persisted jobs when the API is reachable
- `/jobs` can show the same server-backed slice without losing navigation continuity
- API-down behavior stays usable and clearly labeled
- Pending-fit and workflow-normalization notices are visible at the result-set level

## Code and doc pointers
- Frontend README note: `~/Documents/jobtrackr/apps/web/README.md`
- T-106 evidence commit to inspect in the app repo: `858127d` on `feat/jobtrackr-persisted-jobs-workspace`
- Active handoff note: `~/Documents/project-requirements/projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`
- Active QA coverage: `~/Documents/project-requirements/projects/jobtrackr/stories/jobtrackr-in-progress-test-cases-2026-04-19.md`
- Live task plan: `~/Documents/project-requirements/projects/jobtrackr/DEVELOPMENT_PLAN.md`

## Sign-off rule
Move T-106 from QA to Completed only when all of the following are true:
- fast repo-side web verification passes
- API-up persisted-jobs checks pass on both `/dashboard` and `/jobs`
- API-down mock fallback stays usable and visibly labeled
- fallback notices for pending-fit and workflow normalization are visible and canonical

## Remaining blocker if sign-off fails
If any T-106 check fails, keep T-106 in QA and report the exact failing surface, route, dataset state, and expected fallback or continuity behavior rather than collapsing the result into a generic frontend QA blocker.