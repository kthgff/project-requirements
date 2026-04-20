# DEVELOPMENT_PLAN.md

## Project Overview
Build and ship the current AI-powered app initiative quickly with clean, maintainable implementation, using this plan to coordinate work across engineering agents and avoid overlap.

## Team
| Agent | Role | Channel |
|---|---|---|
| Frank | Lead SWE | #swe-frank |
| Alice | SWE | #swe-alice |
| Marcus | SWE | #swe-marcus |
| Priya | SWE | #swe-priya |

## Active Tasks
| ID | Task | Owner | Status | Branch | Blocked By |
|---|---|---|---|---|---|
| T-001 | Finalize PM decision alignment in JobTrackr API contract and debug semantics | Marcus | in-progress | chore/jobtrackr-contract-alignment | — |
| T-002 | Build local Go web app for Gmail inbox job search, protected shell, and raw source-email ingestion | Alice | in-progress | feat/gmail-job-search-webapp | — |
| T-003 | Produce JobTrakr dependency map and milestone sequencing pass for parallel-safe delivery | Frank | in-progress | chore/jobtrackr-dependency-map | — |
| T-004 | Lock jobs dashboard contract for match rating, flagged semantics, and table payload shape | Marcus | in-progress | feat/jobtrackr-jobs-dashboard-contract | — |
| T-005 | Scaffold protected jobs dashboard web shell with mock table data for the first vertical slice | Priya | in-progress | feat/jobs-dashboard-mock-shell | — |

## Completed
| ID | Task | Owner | Merged |
|---|---|---|---|

## Upcoming
- Add persistence and filtering improvements to the Gmail job search web app after initial scaffold
- Convert the dependency map into team-ready implementation tickets once sequencing is approved
- Break the finalized parser and ingestion contract changes into implementation-ready tickets after the jobs dashboard payload is locked

## Decisions & Notes
- Keep task ownership explicit to avoid duplicate work across SWE agents.
- Branch naming should track the task focus and remain one task per branch.
- Gmail integration for the new job search project will use Gmail API with local web OAuth flow and env-based secrets, not embedded credentials.
- Gmail access remains Gmail readonly scope only for MVP.
- Google auth policy: allow anyone with a Google account to sign in (not single approved account only).
- The first Alice vertical slice is auth, protected shell, Gmail connection, and raw source-email ingestion before fit analysis.
- Initial sync defaults to the last 30 days, with future work planned around a 10 minute incremental sync plus manual refresh.
- Frank is owning the dependency map and milestone sequencing pass to identify blockers before Milestones 1 through 4 proceed in parallel.
- Marcus is owning the jobs dashboard contract shape so frontend and API work can converge on the same table payload, especially around match rating and flagged semantics.
- Job enrichment: scrape full details from job detail page linked in email.
- Deduplication: match on job URL to merge repeated alerts for the same job.
- Mandatory job record fields: id, title, company, description, salary range, location, match (1-100), skills, tags.
- Fit display: show match score from 1 to 100.
- Auto-close: jobs with match score below 60 are automatically set to "not a match" status.
- Notifications: no email/push notifications; user reviews jobs in UI.
- Status model: new, flagged, reviewing, skipped, not a match, applied, interview, rejected, offer.

## Branch & PR Rules
- Branch naming: `feat/<short-desc>`, `fix/<short-desc>`, `chore/<short-desc>`
- One task per branch
- PR must reference the Task ID in the title
- Do not merge without Sam (QA) sign-off on feature work
