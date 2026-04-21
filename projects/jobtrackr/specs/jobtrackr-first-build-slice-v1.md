# JobTrakr First Build Slice v1

> Historical kickoff note: this file captures the original mock-first slice. For the current implementation lane, use `projects/jobtrackr/DEVELOPMENT_PLAN.md` and `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`.

## Purpose

Define the original first implementation slice that Engineering used to start coding.

## Canonical source-of-truth reminder

Before implementing or extending this slice, align with:
- `projects/jobtrackr/DEVELOPMENT_PLAN.md`
- `projects/jobtrackr/specs/jobtrackr-pm-decision-memo-2026-04-19.md`
- `projects/jobtrackr/specs/jobtrackr-gate-a-reconciliation-matrix-2026-04-20.md`
- `projects/jobtrackr/specs/jobtrackr-api-contract.md`
- `projects/jobtrackr/specs/jobtrackr-phase-3-engineering-handoff-package-2026-04-20.md`

If an older note conflicts with those files, follow the canonical source set above.

## Recommendation

This document's original recommendation was to start with the smallest visible end-to-end slice:
- Google login page
- authenticated session
- protected dashboard shell
- jobs dashboard with table UI using mock data

That kickoff goal is still useful as historical context, but the current team pickup has moved one step further to the first real vertical slice:
- Google login page
- authenticated session
- Gmail readonly connection
- persisted jobs retrieval
- jobs dashboard rendering server-backed results

This updated direction keeps the original shell work while moving implementation toward the current auth to Gmail to persisted-jobs handoff lane.

---

## Why This Slice First

- it proves the app shell quickly
- it validates auth and route protection early
- it lets frontend and backend work in parallel after the shell exists
- it creates a visible product demo fast
- it avoids blocking on Gmail parsing and fit analysis before the UX exists

---

## In Scope

### Frontend
- `/login` page
- Google sign-in button
- auth loading and error states
- protected `/dashboard` route
- dashboard header
- jobs table with mock data
- empty state support if mock data is removed
- logout control

### Backend
- Google auth integration
- user session creation
- user record creation on first login
- protected route and session middleware

---

## Out of Scope

- Gmail inbox scanning
- job extraction
- enrichment
- resume upload
- fit analysis
- real jobs API
- status persistence

---

## Deliverables

### FE deliverables
1. Login page implemented
2. Protected dashboard page implemented
3. Dashboard renders table with required columns:
   - match rating, with support for pending-fit and low-fit presentation as fit signals
   - company
   - title
   - location
   - source
   - date found
   - status
4. Mock data displayed in table
5. Row interaction pattern chosen, row click or title click

### BE deliverables
1. Google auth flow configured
2. session established after login
3. unauthenticated users redirected to login
4. authenticated users allowed into dashboard
5. user record created or loaded on successful login

---

## Acceptance Criteria

- user can open `/login`
- user can sign in with Google
- unauthenticated user cannot access `/dashboard`
- authenticated user lands on `/dashboard`
- dashboard shows jobs table with required columns and mock rows
- user can log out

---

## Engineering Notes

### Recommended stack for this slice
- TypeScript
- Next.js
- React

### Mock data guidance
Use a temporary static dataset with 5 to 10 representative rows covering:
- stronger-fit and lower-fit examples
- visible match ratings from 0 to 100 plus at least one pending-fit state
- different canonical workflow statuses
- different sources such as LinkedIn and Indeed
- at least one remote role

### Canonical semantics guardrails
- workflow status is only `new`, `interested`, `applied`, `interviewing`, `offer`, or `rejected`
- low-fit and stronger-fit cues are fit signals, not workflow states
- archive behavior remains separate from workflow status
- do not invent placeholder workflow labels for mock data just to make table states feel richer

### UX guidance
- keep styling simple
- optimize for clarity, not polish
- do not block this slice on design perfection

---

## Immediately After This Slice

What followed this original slice is now the active implementation path:
- Gmail readonly connection
- persisted real job list from DB instead of mock data
- dashboard rendering server-backed jobs through the authenticated session

Then:
- broader Gmail ingestion baseline
- resume upload
- fit analysis

---

## Task Breakdown

### Frontend tasks
- create login page
- wire Google sign-in action
- create protected dashboard layout
- create jobs table component
- seed mock job data
- add logout action

### Backend tasks
- configure Google auth
- implement session handling
- create user on first login
- enforce protected route access

---

## PM Decision

Engineering should begin programming now using this slice as the kickoff scope.
