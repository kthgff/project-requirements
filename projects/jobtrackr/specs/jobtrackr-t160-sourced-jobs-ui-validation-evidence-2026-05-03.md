# T-160 Sourced-Jobs UI Validation Evidence — 2026-05-03

## Purpose

Attach concrete repo-side evidence for the T-154/T-159 sourced-jobs UI continuity walkthrough so Alice, Marcus, Priya, and Sam can validate source chips, merged provenance, source filters, drawer/full-detail continuity, and empty states from one current evidence path.

This note supports T-152/T-154 sourced-jobs UI validation only. It does **not** close or replace the live QA gate pair:
- T-106: server-backed dashboard and jobs workspace sign-off
- T-095: source-email persistence fixture-backed sign-off

## App branch and commit under evidence

- App repo worktree: `Documents/jobtrackr-worktrees/feat-jobtrackr-sourced-jobs-ui-continuity`
- App branch: `feat/jobtrackr-sourced-jobs-ui-continuity`
- App commit: `ca0459c`
- App git state at validation start: clean
- Data source used for repo-side validation: mock fallback fixtures in `apps/web/lib/mockJobs.ts`

The primary `Documents/jobtrackr` checkout was dirty on an unrelated branch, so validation intentionally used the clean T-152 worktree rather than attributing evidence to unrelated local changes.

## Seeded source metadata coverage

The T-152 mock fallback dataset currently covers the minimum source-provenance shapes needed for repo-side validation:

| Scenario | Fixture evidence |
|---|---|
| Email-only job | `job_4` and `job_6` use `source: 'gmail'`, `sources: ['gmail']` |
| Indeed-only job | `job_5` uses `source: 'indeed'`, `sources: ['indeed']` |
| LinkedIn-only job | `job_2` and `job_3` use `source: 'linkedin'`, `sources: ['linkedin']` |
| Merged-source job | `job_1` uses `source: 'gmail'`, `sources: ['gmail', 'linkedin']`, `sourceCount: 2` |
| Low-fit non-workflow guard | `job_6` keeps `fitSignal: 'low-fit'` with workflow `status: 'new'` |
| Search/status/location variety | The fixture set spans Remote/Chicago/New York/San Francisco plus `new`, `interested`, `applied`, and `rejected` statuses |

Unknown/missing structured source fallback is covered by source normalization helpers rather than a dedicated mock row: unrecognized labels normalize to `other`, which renders as `Other source` without changing workflow, fit, archive, or queue state.

## Source-chip and filter helper evidence

`apps/web/lib/jobSources.test.ts` passed with coverage for:

- canonical ordered source normalization without duplicates
- Gmail-alert alias normalization to Email/Gmail source semantics
- single-source chip labels such as `LinkedIn`
- merged chip labels such as `LinkedIn +1` and `Indeed +2`
- accessible merged-source copy such as `Sources: LinkedIn and Email`
- OR semantics within selected source filters
- no source-filter selection returning all jobs

`apps/web/lib/jobApiMapping.test.ts` also passed with API mapping coverage that preserves source arrays and source counts while keeping canonical workflow and pending-fit behavior separate.

## Command evidence

Run from `Documents/jobtrackr-worktrees/feat-jobtrackr-sourced-jobs-ui-continuity/apps/web`:

```bash
npm test && npm run build
```

Result:

- `npm test`: PASS — 18 tests passed, 0 failed
- `npm run build`: PASS — Next.js production build completed successfully; routes generated for `/dashboard`, `/jobs`, and `/jobs/[jobId]`

Warnings observed:

- Node emitted existing experimental type-stripping warnings.
- Node emitted existing module type warnings for TypeScript test files.

Neither warning blocked tests or build, and neither changes sourced-jobs UI semantics.

## Walkthrough evidence status

| Surface | Repo-side evidence status | Notes |
|---|---|---|
| Desktop source chips | PASS by component/helper/test coverage | `JobsTable` renders source accessible labels and chip helpers against the seeded fixture shapes. |
| Mobile source chips | PASS by shared component/helper coverage | Mobile cards share the same normalized source helpers as desktop. |
| Merged `+N` source copy | PASS by helper tests and `job_1` fixture | `formatSourceChip` coverage includes `+1` and `+2`; accessible copy expands merged provenance. |
| Source filters | PASS by helper tests and jobs query filtering path | OR-within-source semantics are covered in `jobMatchesSourceFilter`; source filters remain a filter family, not workflow state. |
| Empty state naming source filters | PASS by T-152 implementation path plus build coverage | Browser screenshot still recommended before Sam final sign-off. |
| Drawer source continuity | PASS by shared job record shape and build coverage | Drawer uses the selected `JobRecord` source metadata; browser screenshot still recommended for visual QA. |
| Full-detail source continuity and return context | PASS by detail route build plus navigation helper tests | `/jobs/[jobId]` builds successfully and return-context helper tests pass. |
| Selection preserve/clear/restore | PASS by no sourced-job-specific selection model introduced | Source filters compose with the existing workspace continuity model. |
| Non-state provenance boundary | PASS by fixture/test/doc inspection | Source metadata remains separate from workflow, fit, archive, and queue state. |

## Remaining visual QA note

This T-160 pass records clean repo-side command and fixture evidence. It does not attach actual browser or mobile screenshots. If Sam requires visual proof before moving T-152 out of QA, start from the T-162 evidence index at `projects/jobtrackr/specs/jobtrackr-t162-sourced-jobs-ui-qa-evidence-index-2026-05-03.md`, then use `projects/jobtrackr/specs/jobtrackr-t161-sourced-jobs-ui-visual-qa-capture-handoff-2026-05-03.md` to execute the T-154 walkthrough against the same app commit and capture:

- desktop `/jobs` source chips and merged `+N` treatment
- mobile card source chips
- source-filter empty state with `Clear all filters`
- drawer source continuity
- full-detail return-context continuity

## Non-goals

Do not use this note to:

- close T-106 or T-095 without their dedicated sign-off evidence
- validate live Indeed or LinkedIn scraping
- introduce a separate sourced-jobs queue
- create source-specific workflow states
- treat Email/Indeed/LinkedIn provenance as fit, workflow, archive, or priority state
