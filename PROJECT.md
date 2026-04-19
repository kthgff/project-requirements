# Project Requirements Workspace

## Active Projects

### JobTrackr
- Location: `projects/jobtrackr/`
- Status: Planning in progress
- Current docs:
  - `projects/jobtrackr/prd.md`
  - `projects/jobtrackr/requirements.md`
  - `projects/jobtrackr/stories.md`
  - `projects/jobtrackr/development-plan.md`
- Stack direction:
  - Frontend: Next.js, React, TypeScript
  - Backend: Go with Gin
  - Database: PostgreSQL
  - Data access: pgx + sqlc
  - UI: Tailwind CSS + shadcn/ui
- Locked planning decisions:
  - Model saved as a boolean flag
  - Model archived as an archive attribute, not a primary status
  - Include manual job entry in MVP
  - Use soft duplicate detection, not aggressive auto-merge
  - Attach update emails to a timeline only when match confidence is high
- Next deliverables:
  - Technical architecture spec
  - Database schema spec
  - API contract draft
  - Repository scaffolding plan
