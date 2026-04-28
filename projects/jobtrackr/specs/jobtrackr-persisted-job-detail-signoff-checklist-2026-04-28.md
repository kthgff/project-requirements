# T-138 Persisted Job Detail Route Sign-off Checklist — 2026-04-28

## Purpose
Give Sam one implementation-facing checklist for validating the completed persisted full-detail route follow-through from T-133 and T-137 without reconstructing the behavior from plan history, frontend README notes, and scattered QA cases.

## Use this with
- Live plan: `projects/jobtrackr/DEVELOPMENT_PLAN.md`
- Live project overview: `projects/jobtrackr/PROJECT.md`
- Live handoff package: `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`
- QA blocker checklist: `projects/jobtrackr/specs/jobtrackr-qa-blocker-checklist-2026-04-19.md`
- Active QA coverage: `projects/jobtrackr/stories/jobtrackr-in-progress-test-cases-2026-04-19.md`

## Tasks covered
- `T-133` persisted job detail API fallback for `/jobs/:id`
- `T-137` full-detail recovery links back to the filtered jobs workspace

## Current implementation evidence
- `T-133` branch: `feat/jobtrackr-job-detail-api-fallback`
- `T-137` branch: `feat/jobtrackr-detail-page-recovery-links`
- Repo-side expectation: the full detail route prefers persisted API data when available, falls back safely when it is not, and preserves jobs-workspace return context without regressing pending-fit copy fidelity

## Preconditions
- App repo available locally with the web workspace at `apps/web` and the API workspace at `apps/api`
- One authenticated test user is available
- One deterministic persisted-jobs dataset is available with:
  - at least one persisted job that can be opened at `/jobs/:id`
  - at least one row with missing fit data so pending-fit copy can be inspected
  - at least one launched-from-workspace detail flow that includes filters or page context in the return path
- A safe API-unavailable mode is available, such as stopping the API locally or pointing the detail fetch to an unreachable backend

## Fast repo-side verification
Run from the app repo workspaces:

```bash
cd apps/api && go test ./... && go build ./...
cd ../web && npm test && npm run build
```

Expected:
- API tests and build pass
- web tests and build pass
- no new `/jobs/:id` or detail-navigation regressions appear before UI QA starts

## UI sign-off steps
### A. Persisted detail route verification
1. Start the API and web app with persisted jobs available.
2. Sign in through the authenticated shell.
3. Open `/jobs` and launch a full detail view for a persisted job.
4. Verify the routed detail page content, match presentation, and any fallback notices.

### B. Safe detail fallback verification
1. Keep the web app running but make the detail API unavailable, or use a missing persisted job id.
2. Open `/jobs/:id` directly or from the jobs workspace.
3. Inspect the rendered detail state, fallback copy, and navigation safety.

### C. Return-context recovery verification
1. Open `/jobs` with a non-default search, filter, page, or selected row state.
2. Launch the full detail route.
3. Use the return path back to the jobs workspace.
4. Confirm the recovered workspace state matches the launching context.

## Required assertions
### A. Persisted detail data is preferred when available
- `/jobs/:id` renders persisted job detail data instead of relying on mock-only records when the API is reachable
- detail presentation uses canonical workflow values only
- pending-fit rows stay canonical and do not regress into `null/100` or similar broken match copy

### B. Safe fallback remains visible and usable
- the detail page stays usable when persisted detail data is unavailable
- fallback behavior is clearly labeled instead of failing silently
- unsupported or unsafe return targets fall back to `/jobs` safely

### C. Workspace continuity survives the detail route
- returning from the full detail route preserves the original jobs workspace context when that context is valid
- filters, page, and selected-row review flow remain stable enough for QA walkthroughs
- recovery does not depend on raw legacy workflow aliases or non-canonical fit wording

## Suggested inspection targets
At minimum, confirm:
- `/jobs/:id` uses persisted API detail data when available
- API-down or missing-row behavior stays safe and explicit
- the return link lands back in the same filtered jobs review context when possible
- pending-fit messaging stays canonical across drawer-to-route and route-to-workspace flows

## Code and doc pointers
- Live plan entries: `projects/jobtrackr/DEVELOPMENT_PLAN.md` (`T-133`, `T-137`)
- Frontend continuity contract: `projects/jobtrackr/specs/jobtrackr-detail-view-contract-2026-04-20.md`
- List/detail continuity examples: `projects/jobtrackr/specs/jobtrackr-list-detail-contract-examples-2026-04-20.md`
- Workspace continuity contract: `projects/jobtrackr/specs/jobtrackr-workspace-ux-contract-2026-04-20.md`
- Active QA coverage: `projects/jobtrackr/stories/jobtrackr-in-progress-test-cases-2026-04-19.md`

## Sign-off rule
Treat this checklist as the implementation-facing QA note for the completed persisted detail-route follow-through. If any check fails, report the exact failing route, dataset state, launch path, return target, and expected canonical fallback behavior instead of collapsing the result into a generic detail-page bug.
