# BLOCKERS.md

This file tracks active and resolved blockers for the JobTrackr project. Jimmy (PM) maintains this file by scanning engineer and QA channels for blocker announcements.

## Active Blockers
| ID | Who | Blocker | What I've Tried | Need From | Status |
|---|---|---|---|---|---|

Current note:
- There are no active repo-side blockers right now.
- The remaining coordination gap is external kickoff drift when hourly prompts still point to `~/Documents/project-requirements/DEVELOPMENT_PLAN.md` instead of `projects/jobtrackr/DEVELOPMENT_PLAN.md`.
- Repo-side follow-through is QA doc verification across the shared recovery-note set and kickoff-facing entrypoints, not a reopened engineering blocker.

## Resolved Blockers
| ID | Who | Blocker | Resolution |
|---|---|---|---|
| B-001 | Alice | Next.js compile failure in `apps/web/components/JobsTable.tsx` with `Unexpected token `div`. Expected jsx identifier` at line 44 blocks the jobs dashboard frontend from running | Priya re-ran `npm --prefix apps/web run build` in `/Users/keith.goff/Documents/jobtrackr` on 2026-04-22 and the Next.js production build completed successfully, so the compile failure no longer reproduces. Treat this as resolved engineering work plus QA verification evidence, not an active blocker. |
