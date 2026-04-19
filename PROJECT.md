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
  - `projects/jobtrackr/technical-architecture.md`
  - `projects/jobtrackr/database-schema.md`
  - `projects/jobtrackr/api-contract.md`
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
  - Repository scaffolding plan
  - Initial migration plan
  - Initial endpoint implementation order
  - Local development setup guide
