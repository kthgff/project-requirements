# DEVELOPMENT_PLAN.md

## Project Overview
Build and ship the current AI-powered app initiative quickly with clean, maintainable implementation, using this plan to coordinate work across engineering agents and avoid overlap.

## Team
| Agent | Role | Channel |
|---|---|---|
| Frank | Lead Engineer | #engineer-frank |
| Alice | Engineer | #engineer-alice |
| Marcus | Engineer | #engineer-marcus |
| Priya | Engineer | #engineer-priya |

## Active Tasks
| ID | Task | Owner | Status | Branch | Blocked By |
|---|---|---|---|---|---|
| T-001 | Finalize PM decision alignment in JobTrackr API contract and debug semantics | Marcus | in-progress | chore/jobtrackr-contract-alignment | — |
| T-002 | Build local Go web app for Gmail inbox job search, protected shell, and raw source-email ingestion | Alice | in-progress | feat/gmail-job-search-webapp | — |
| T-003 | Produce JobTrakr dependency map and milestone sequencing pass for parallel-safe delivery | Frank | in-progress | chore/jobtrackr-dependency-map | — |

## Completed
| ID | Task | Owner | Merged |
|---|---|---|---|

## Upcoming
- Break the finalized contract changes into implementation-ready parser and ingestion tickets
- Add persistence and filtering improvements to the Gmail job search web app after initial scaffold
- Convert the dependency map into team-ready implementation tickets once sequencing is approved

## Decisions & Notes
- Keep task ownership explicit to avoid duplicate work across engineer agents.
- Branch naming should track the task focus and remain one task per branch.
- Gmail integration for the new job search project will use Gmail API with local web OAuth flow and env-based secrets, not embedded credentials.
- Gmail access remains Gmail readonly scope only for MVP.
- The first Alice vertical slice is auth, protected shell, Gmail connection, and raw source-email ingestion before fit analysis.
- Initial sync defaults to the last 30 days, with future work planned around a 10 minute incremental sync plus manual refresh.
- Frank is owning the dependency map and milestone sequencing pass to identify blockers before Milestones 1 through 4 proceed in parallel.

## Branch & PR Rules
- Branch naming: `feat/<short-desc>`, `fix/<short-desc>`, `chore/<short-desc>`
- One task per branch
- PR must reference the Task ID in the title
- Do not merge without Sam (QA) sign-off on feature work
