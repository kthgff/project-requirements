# T-163 Source-Service Provenance QA Evidence Index — 2026-05-03

## Purpose

Give Sam one current QA-facing index for backend sourced-job provenance validation, so schema, merge, connector, source-run, and source-email debug-read evidence can be collected without reconstructing the T-144/T-149/T-150/T-155/T-158 handoff chain from hourly notes.

This index supports sourced-job service provenance validation only. It does **not** replace the live QA gate pair:
- T-106: server-backed dashboard and jobs workspace sign-off
- T-095: source-email persistence fixture-backed sign-off

## Start here

1. Recover through the live project-root files:
   - `projects/jobtrackr/DEVELOPMENT_PLAN.md`
   - `projects/jobtrackr/PROJECT.md`
   - `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`
2. Use this index as the current backend/source-service provenance validation map.
3. Keep sourced jobs inside the canonical `jobs` model. Source provenance is metadata only; it must not become workflow, fit, archive, priority, or queue state.

## Evidence chain

| Step | File or app surface | Use it for | Current status |
|---|---|---|---|
| Schema contract | `projects/jobtrackr/specs/jobtrackr-job-source-schema-and-provenance-spec-2026-05-02.md` | Canonical `jobs`, `job_sources`, `job_source_runs`, and `search_profiles` provenance model | Locked by T-144 |
| Schema QA lens | `projects/jobtrackr/specs/jobtrackr-t144-sourced-jobs-schema-provenance-validation-lens-2026-05-02.md` | Migration/schema inspection plus Gmail compatibility checks | Published by T-148 |
| Source-service validation map | `projects/jobtrackr/specs/jobtrackr-t158-source-service-provenance-validation-handoff-2026-05-03.md` | Ordered backend validation path across T-144, T-149, T-150, and T-155 | Published by T-158 |
| Source-email sign-off | `projects/jobtrackr/specs/jobtrackr-source-email-persistence-signoff-checklist-2026-04-22.md` | Fixture-backed T-095 source-email persistence and `job_source_emails` assertions | Live QA gate |
| Source-email evidence bundle | `projects/jobtrackr/specs/jobtrackr-source-email-persistence-evidence-2026-04-22.md` | Existing repo-side proof behind the T-095 branch and commit | Repo-side evidence available |
| Debug-read contract | `projects/jobtrackr/stories/jobtrackr-in-progress-test-cases-2026-04-19.md` | Active TC coverage for T-108 `/api/v1/source-emails` filters and source-service provenance checks | Contract coverage published |
| App API tests | `apps/api` in the app repo | `go test ./...` and `go build ./...` proof for source-service/persistence packages | Needs QA rerun on reviewed app commit |

## Current evidence baseline

Use these completed implementation anchors unless a newer app commit is explicitly under review:

- T-144 sourced schema/provenance: app commit `c33b2f0`
- T-149 cross-source dedupe/merge: app commit `e6e277f`
- T-150 LinkedIn sourcing connector: app commit `78d2a72`
- T-155 source-email debug-read alignment: docs-only contract alignment to implemented T-108 endpoint
- T-158 backend validation handoff: `projects/jobtrackr/specs/jobtrackr-t158-source-service-provenance-validation-handoff-2026-05-03.md`

The current baseline is implementation-facing and repo-side only. Sam still needs executed seeded/API evidence before closing sourced-job provenance QA follow-through.

## Artifact slots Sam can fill

Use this compact checklist when attaching backend/source-service QA evidence:

```markdown
T-163 source-service provenance QA evidence index
- App branch/commit:
- Database/source data: seeded provider fixtures / Gmail fixture / local DB
- Verification commands:
- `go test ./...`: PASS/FAIL — evidence:
- `go build ./...`: PASS/FAIL — evidence:
- Schema/migration inspection (`jobs`, `job_sources`, `job_source_runs`, `search_profiles`): PASS/FAIL — evidence:
- Cross-source merge to one canonical job: PASS/FAIL — evidence:
- Distinct Gmail/Indeed/LinkedIn `job_sources` provenance rows: PASS/FAIL — evidence:
- Source-run lifecycle fields and result counts: PASS/FAIL — evidence:
- Indeed fixture connector normalization: PASS/FAIL — evidence:
- LinkedIn fixture connector normalization: PASS/FAIL — evidence:
- T-108 `GET /api/v1/source-emails` debug-read filters: PASS/FAIL — evidence:
- `/jobs` payload source metadata is sufficient for T-154/T-162 UI validation: PASS/FAIL — evidence:
- Provenance stayed metadata-only: PASS/FAIL — evidence:
- Blockers:
```

## Pass boundaries

A sourced-job provenance validation pass is good enough to hand back for QA decision when it includes:

- the app branch/commit under review
- command evidence from `apps/api` for `go test ./...` and `go build ./...`, or an explicit reason those commands were not rerun
- migration or database inspection covering `jobs`, `job_sources`, `job_source_runs`, and `search_profiles`
- seeded or fixture-backed evidence showing Gmail, Indeed, and LinkedIn provenance attaching to one canonical job when merge keys match
- source-run lifecycle evidence with status, timestamps, provider/profile linkage, and useful counts
- T-108 debug-read evidence for `GET /api/v1/source-emails` with `matchedAsJob`, `fromEmail`, `search`, and `limit` filters when Gmail-origin jobs are involved
- a `/jobs` API payload or mapping proof that source metadata is sufficient for UI source chips, merged-source labels, source filters, drawer/full-detail source context, and return continuity
- an explicit statement that source provenance stayed metadata-only and did not mutate workflow, fit, archive, priority, or queue state

## Still not covered by this index

Do not use this index to:

- close T-106 without the T-116 persisted-jobs workspace sign-off checklist
- close T-095 without the T-102 source-email persistence sign-off checklist and fixture-backed persistence proof
- validate live Indeed or LinkedIn scraping against production sites
- introduce a separate sourced-jobs queue outside `/jobs`
- create source-specific workflow statuses
- treat Email, Indeed, LinkedIn, source-run status, merged-source counts, or source profile state as fit/workflow/archive state

## Remaining gap

T-158 supplies the validation map; this index supplies the evidence collection path. The remaining gap is executed Sam/QA evidence: rerun API checks on the app branch under review, capture seeded provider/source-email debug-read proof, and attach the artifact checklist above before moving sourced-job provenance follow-through out of QA.
