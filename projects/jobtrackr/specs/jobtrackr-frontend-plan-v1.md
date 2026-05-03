# JobTrakr Frontend Plan v1

## Purpose

Define the frontend plan for JobTrakr MVP, focused on the login experience and the main jobs dashboard with a table view.

This preserved plan should follow the canonical detail-view contract in `specs/jobtrackr-detail-view-contract-2026-04-20.md` and the canonical list-to-detail examples in `specs/jobtrackr-list-detail-contract-examples-2026-04-20.md` whenever older wording here drifts.

## Frontend Goal

The frontend should let Keith:
- sign in with Google quickly
- access the app securely
- view all tracked jobs in one clean dashboard
- scan job data quickly in a table
- open a job to see more detail
- update job status efficiently

## MVP Frontend Scope

### In scope
- login page
- authenticated app shell
- jobs dashboard page
- jobs table
- job detail panel or detail view
- table sorting and filtering
- status update interactions
- fit flag visibility
- resume upload entry point
- logout control

### Out of scope for MVP
- multi-page marketing site
- advanced theming customization
- complex drag-and-drop workflows
- kanban board
- mobile-native app

---

## Frontend Information Architecture

### Public route
- `/login`

### Authenticated routes
- `/dashboard`
- `/jobs/:jobId` or dashboard-side detail panel
- optional `/settings` for resume and account controls if needed

### Recommended MVP structure
- keep MVP simple with two main states:
  - unauthenticated login page
  - authenticated dashboard

---

## Core Screens

### 1. Login Page

#### Purpose
Provide a single clear entry point into the product using Gmail federated login.

#### Primary content
- JobTrakr product name
- short explanation of value
- email input for the Google account the user intends to use
- one primary CTA: `Sign in with Google`

#### Recommended layout
- immersive split-screen or full-viewport login experience
- product title as a strong first-screen brand signal
- short supporting sentence
- compact product-preview or status-panel treatment that hints at job scanning, matching, and tracking
- Google sign-in button
- optional small trust/help text below

#### UX requirements
- user should understand sign-in is required
- only one primary action should compete for attention
- Google email is required before initiating sign-in
- OAuth profile email must match the supplied email before the app creates a session
- failed sign-in should show a clear retryable error
- mismatched Google account should explain that the user needs to retry with the same email
- successful sign-in should redirect to dashboard or onboarding
- loading and disabled states should make the redirect flow feel deliberate
- mobile layout must keep the email field, Google button, and error messages visible without awkward scrolling

#### Suggested copy direction
- Headline: `Track jobs faster`
- Supporting text: `Sign in with Google to scan job alerts, review matches, and manage your search in one place.`

#### Visual direction
- sleek and professional
- modern job-search command center feel
- clear product trust signal rather than playful or consumer-social styling

---

### 2. Jobs Dashboard

#### Purpose
Provide the main operational screen for viewing and managing all tracked jobs stored in the database.

#### Primary goals
- show all jobs in the database in one place
- make good-fit roles obvious
- support quick scanning and filtering
- support fast status updates
- support searchable retrieval across tracked jobs

#### Recommended layout

##### Top bar
- product name or logo
- sync status or last sync time
- resume status entry point
- user/account menu
- logout

##### Dashboard header
- page title: `Jobs`
- short status line or count summary
- optional CTA for `Upload resume` if none exists

##### Filter row
- search input
- status filter
- fit filter
- source filter
- date filter later if needed
- clear filters action

##### Main content
- searchable jobs table occupying most of the page width

##### Optional side panel or drawer
- opens selected job detail without leaving context

---

## Jobs Table Plan

### Table purpose
Enable rapid review of tracked jobs with enough information to decide what deserves attention.

### Required columns for MVP
- Fit
- Match %
- Company
- Title
- Location
- Source
- Date Found
- Status

### Recommended optional columns
- Salary
- Work Mode
- Last Seen

### Column behavior
- Fit should be visually distinct, badge/icon/color state
- Match % should display the computed alignment percentage
- Status should be editable inline if implementation is straightforward
- Title should open job detail
- Date Found should support sorting

### Sorting
At minimum support:
- date found
- company
- status

PM recommendation:
- default sort: newest first

### Filtering
At minimum support:
- search by title/company/description
- filter by status
- filter by fit flag
- filter by source

### Empty states
If no jobs exist:
- explain that jobs will appear after search profiles and scheduled sourcing start finding matches
- primary action: create a search profile
- secondary context may mention Gmail, Indeed, and LinkedIn sources when connected or planned

If filters return no results:
- explain no jobs match the current filters
- provide clear filters action

---

## Job Detail Experience

### Purpose
Show richer information without overwhelming the main table.

### Recommended MVP interaction
Use a right-side detail drawer or panel from the table.

Reason:
- faster than navigating away
- keeps dashboard context visible
- works well for scan-and-review behavior

### Detail content
- title
- company
- location
- source platform
- apply/job link
- job summary or description
- match percentage when available
- fit state and fit summary
- status control
- notes with explicit save/cancel
- editable tags
- source and activity context when present

### Actions in detail view
- change status
- edit notes with explicit save/cancel
- add or remove tags in context
- open original job link
- close drawer

---

## Authenticated App Shell

### Required behaviors
- unauthenticated users are redirected to `/login`
- authenticated users land on `/dashboard`
- app shell should persist top navigation and account controls

### Recommended shell structure
- top nav only for MVP
- avoid heavy sidebar unless more sections exist

---

## Resume Upload Entry Point

Because fit depends on a resume, the frontend should make resume state visible.

### MVP behavior
- if no resume uploaded, show a compact prompt in the dashboard and a full management surface in settings
- accept PDF resume uploads only
- show active resume status in both dashboard and settings
- after successful parsing, make the uploaded resume active automatically
- after upload, queue or refresh fit-analysis state when available
- let users view or download the original uploaded PDF
- support multiple resume versions and active-resume switching in settings
- soft-delete/archive resume versions instead of hard deleting them in MVP
- if parsing fails, show a user-facing error asking for help and offer cleaner PDF upload or pasted-text fallback
- show lightweight parser states such as parsed, incomplete, or failed without exposing technical confidence scores

---

## Frontend States To Support

### Login page
- default
- email validation error
- login in progress
- login error
- Google account mismatch error

### Dashboard
- initial loading
- data loaded
- empty state
- filtered empty state
- sync in progress
- sync failure warning

### Table row
- normal
- selected
- strong-fit highlight
- low-fit cue
- pending or missing fit data

### Job detail panel
- loading
- loaded
- error
- empty notes state
- empty tags state
- inline save failure that preserves unsaved note edits

---

## Functional Requirements

### FR1. Login page
The frontend shall provide a polished login page with a Google email input and Google sign-in action.

The login page shall validate email format before sign-in, submit the supplied email into the auth start flow, and display retryable error states for failed or mismatched Google login attempts.

### FR2. Protected navigation
The frontend shall restrict dashboard access to authenticated users.

### FR3. Dashboard page
The frontend shall provide a main dashboard showing tracked jobs from the database.

### FR4. Table view
The dashboard shall present jobs in a searchable table view.

### FR5. Filtering and search
The dashboard shall support search across tracked jobs and basic filtering.

### FR6. Sorting
The dashboard shall support basic table sorting.

### FR7. Job detail access
The user shall be able to open a job detail view from the table.

### FR8. Status update
The user shall be able to update job status from the detail view, with optional table-side affordances only if they stay aligned with the canonical detail interaction model.

### FR9. Notes and tags editing
The user shall be able to edit notes with explicit save/cancel and add or remove tags from the shared detail experience.

### FR10. Resume visibility
The frontend shall indicate whether a resume has been uploaded.

### FR11. Logout
The frontend shall provide a logout action.

---

## Recommended TypeScript Frontend Direction

PM recommendation for MVP:
- React with TypeScript
- Next.js with TypeScript for app shell and routing
- server-side auth integration where convenient
- a mature table library if needed, but do not over-engineer the first version

Reason:
- fast iteration
- strong TypeScript support
- good auth ecosystem
- easy route protection and app structure

---

## Delivery Sequence Recommendation

1. Build login page
2. Build protected app shell
3. Build dashboard layout
4. Render static jobs table with mock data
5. Connect table to real backend job list
6. Add filters and sorting
7. Add detail drawer using the canonical shared section order
8. Add detail-based status, notes, and tags interactions that match the canonical detail contract
9. Add resume upload entry point and empty states

---

## Open Questions

- Should fit use icon-only, badge-only, or score plus badge in the initial table?
- Should resume upload live in dashboard header, settings, or onboarding?

## Canonical continuity note

For current implementation pickup, treat the shared detail drawer and full-page detail route as one behavioral surface. Notes must use explicit save/cancel, tags must edit in context, and list-to-detail mapping should follow `specs/jobtrackr-list-detail-contract-examples-2026-04-20.md` instead of older simplified wording in this preserved plan.
