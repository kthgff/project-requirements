# JobTrakr Milestones 1 to 4 Engineering Tickets — 2026-04-20

## Purpose

Turn the approved dependency sequencing into implementation-ready engineering tickets for Milestones 1 to 4, with explicit dependencies, owner lanes, and exit checks.

## Assumptions

- The PM decision memo is canonical where older docs conflict.
- Workflow enums must use the corrected model from the dependency sequencing pass.
- Tickets that depend on contract stabilization should not start implementation until those contracts are frozen.

## Contract Gates Before Broad Parallelization

### Gate A. Canonical schema and workflow state correction
Must lock before Milestone 3 and Milestone 4 implementation spreads.

Includes:
- corrected workflow enum and archive semantics
- nullable fit fields for pre-analysis jobs
- one-source-email-to-many-jobs linkage model
- canonical dashboard-facing job fields

### Gate B. Shared dashboard query and Gmail connection contracts
Must lock before fixture-backed frontend work switches to real API data.

Includes:
- jobs list request and response semantics
- job detail response shape
- Gmail connection state shape
- sync run request and accepted/running response semantics

## Milestone 1 Tickets

### JT-M1-01. Google auth initiation and callback
**Lane:** Auth and app shell
**Depends on:** none
**Deliver:**
- auth start route
- Google callback handler
- user record creation or lookup
- session creation and logout path
**Exit checks:**
- user can start Google sign-in
- callback creates a session and returns to app
- logout clears session cleanly

### JT-M1-02. Protected routing and base dashboard shell
**Lane:** Auth and app shell
**Depends on:** JT-M1-01
**Deliver:**
- protected route middleware or server guard
- redirect unauthenticated users to login
- base authenticated shell for dashboard route
**Exit checks:**
- anonymous users cannot reach dashboard
- authenticated users reach shell without errors

## Milestone 2 Tickets

### JT-M2-01. Dashboard layout and account controls
**Lane:** Table UI integration
**Depends on:** JT-M1-02
**Deliver:**
- dashboard page scaffold
- header and account controls
- layout regions for filters and jobs content
**Exit checks:**
- authenticated shell renders consistent dashboard frame
- account/logout controls are visible and usable

### JT-M2-02. Mock jobs table and fixture contract
**Lane:** Table UI integration
**Depends on:** JT-M2-01
**Deliver:**
- mock table component with required columns
- fixture shape aligned to planned real query fields
- loading, empty, and error states
**Exit checks:**
- mock jobs render in table
- row selection behavior is defined
- empty and loading states are present

### JT-M2-03. Filter row shell and interaction summary
**Lane:** Table UI integration
**Depends on:** JT-M2-01
**Deliver:**
- status, location, date, and search controls shell
- active filter summary region
- URL or local state model for filter persistence
**Exit checks:**
- controls render without real backend dependency
- selected filters are reflected in UI state predictably

## Milestone 3 Tickets

### JT-M3-01. Schema correction and persistence foundation
**Lane:** Persistence and contracts
**Depends on:** Gate A
**Deliver:**
- schema for user, gmail_connection, source_email, job, job_source_email, resume, job_fit_analysis, and job_status_history
- corrected workflow enum and archivedAt semantics
- nullable fit fields for jobs without analysis
**Exit checks:**
- migration set is internally consistent
- schema supports one source email to many jobs
- schema matches PM memo decisions

### JT-M3-02. Jobs list and detail contracts
**Lane:** Persistence and contracts
**Depends on:** Gate A
**Deliver:**
- canonical list endpoint request contract
- canonical list response payload
- canonical job detail response payload
- search, filter, sort, and pagination semantics
**Exit checks:**
- frontend and backend can share one documented payload shape
- filter semantics are explicit: AND across filter groups, OR within repeated values
- date bounds and sort defaults are documented

### JT-M3-03. Jobs repository and query implementation
**Lane:** Persistence and contracts
**Depends on:** JT-M3-01, JT-M3-02
**Deliver:**
- repository methods for list and detail reads
- filtering, sorting, and pagination support
- seed or fixture-backed persistence checks
**Exit checks:**
- dashboard can read jobs from persisted data
- detail lookup works for a real job row

### JT-M3-04. Frontend switch from mocks to real jobs query
**Lane:** Table UI integration
**Depends on:** Gate B, JT-M3-03
**Deliver:**
- data fetching wired to real list endpoint
- empty/loading/error states preserved under real data
- row navigation or drill-in wired to real identifiers
**Exit checks:**
- dashboard renders persisted jobs without mock fixtures
- filter state maps cleanly onto query params

## Milestone 4 Tickets

### JT-M4-01. Gmail connection state contract and sync API
**Lane:** Persistence and contracts
**Depends on:** Gate B
**Deliver:**
- connection status shape
- reconnect/disconnect expectations
- sync trigger request and accepted/running response
**Exit checks:**
- frontend can represent connected, reconnect-needed, and failed states
- sync start behavior is documented without ambiguity

### JT-M4-02. Gmail readonly OAuth wiring and token persistence
**Lane:** Gmail connect and ingestion pipeline
**Depends on:** JT-M1-01, JT-M3-01, JT-M4-01
**Deliver:**
- Gmail readonly authorization path
- token storage and refresh handling
- user-visible connection state updates
**Exit checks:**
- user can connect Gmail with readonly scope only
- connection state persists across sessions

### JT-M4-03. Source-email ingestion baseline
**Lane:** Gmail connect and ingestion pipeline
**Depends on:** JT-M4-02, JT-M3-01
**Deliver:**
- initial 30-day fetch
- candidate email classification
- source-email persistence for job_alert, uncertain, and failures
- sync logging hooks
**Exit checks:**
- sync stores candidate source emails durably
- ingestion logs expose what ran and what failed

### JT-M4-04. Extraction threshold, dedupe, and job upsert
**Lane:** Gmail connect and ingestion pipeline
**Depends on:** JT-M4-03, JT-M3-01
**Deliver:**
- extraction threshold enforcement
- one-email-to-many-jobs extraction path
- dedupe by job URL
- create or update canonical job rows
**Exit checks:**
- relevant emails create job rows
- duplicate alerts do not create duplicate active jobs
- source email linkage is preserved for drilldown

### JT-M4-05. Dashboard refresh against real ingestion output
**Lane:** Table UI integration
**Depends on:** JT-M4-04, JT-M3-04
**Deliver:**
- refresh path after sync
- visible ingestion-to-dashboard feedback
- validation that real created jobs appear in list and detail views
**Exit checks:**
- running a sync produces dashboard-visible jobs
- end-to-end loop works from connect to visible job row

## Recommended Owner Mapping

- Frank: JT-M3-01, JT-M3-02, JT-M4-01, coordination on JT-M4-04 sequencing
- Alice: JT-M1-01, JT-M1-02, JT-M4-02, JT-M4-03
- Marcus: JT-M3-03, job payload examples, detail contract validation
- Priya: JT-M2-01, JT-M2-02, JT-M2-03, JT-M3-04, JT-M4-05

## Parallel Execution Rules

- Milestone 2 UI work may proceed on fixtures while Milestone 3 contracts harden.
- No team should implement real status handling from stale docs.
- JT-M3-02 and JT-M4-01 should publish examples before JT-M3-04 or JT-M4-05 integrate live data.
- JT-M4-04 should not start until the join model from JT-M3-01 is accepted.

## Handoff Outcome

When these tickets are accepted, Engineering has a parallel-safe map for Milestones 1 to 4 that can be executed without additional PM clarification on sequencing, contract order, or lane ownership.
