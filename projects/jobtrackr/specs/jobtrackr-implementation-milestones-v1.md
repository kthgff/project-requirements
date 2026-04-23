# JobTrakr Implementation Milestones v1

## Purpose

Break JobTrakr MVP into concrete implementation milestones that can be executed in order.

## Milestone 1. Auth foundation

### Goal
Ship user login and protected app access.

### Scope
- login page
- Google federated login
- session handling
- protected routing
- user record creation

### Exit criteria
- user can sign in with Google
- unauthenticated users cannot access dashboard
- authenticated users reach the dashboard shell

---

## Milestone 2. Dashboard shell and table foundation

### Goal
Ship the visible frontend shell for the app.

### Scope
- dashboard page
- header and account controls
- filters row shell
- jobs table with mock data
- loading and empty states

### Exit criteria
- authenticated user sees dashboard
- table renders required columns
- row interaction pattern is defined and working

---

## Milestone 3. Data model and real job list

### Goal
Replace mock table data with real persisted jobs.

### Scope
- implement data schema
- implement jobs list query
- implement single-job detail query
- connect dashboard to real data

### Exit criteria
- dashboard reads jobs from database
- job detail view can load a real job

---

## Milestone 4. Gmail ingestion baseline

### Goal
Populate real jobs from Gmail inbox scanning.

### Scope
- Gmail authorization
- initial scan
- candidate email classification
- source email persistence
- basic job extraction
- job creation/upsert

### Exit criteria
- inbox scan creates job records from relevant emails
- duplicate obvious repeats do not create duplicate active rows

---

## Milestone 5. Resume upload and fit foundation

### Goal
Allow the system to evaluate jobs against the user's resume.

### Scope
- resume upload UI
- resume storage
- active resume selection
- fit analysis trigger wiring

### Exit criteria
- user can upload a resume
- uploaded resume is available for job fit analysis

---

## Milestone 6. Fit analysis and fit signals

### Goal
Surface likely good-fit roles in the dashboard without conflating fit indicators with workflow state.

### Scope
- fit scoring
- low-fit and match-rating signal logic
- fit summary storage
- fit visibility in table and detail view

### Exit criteria
- jobs receive fit results
- dashboard clearly shows match rating, low-fit indicators, and pending-fit states without implying a workflow transition

---

## Milestone 7. Workflow completion

### Goal
Complete the core triage and review workflow.

### Scope
- detail drawer or detail page
- status updates
- filter refinements
- source link access
- better error states

### Exit criteria
- user can review, inspect, and update jobs end to end

---

## Milestone 8. QA and release readiness

### Goal
Prepare MVP for real use.

### Scope
- acceptance review against specs
- bug fixing
- edge-case validation
- launch checklist

### Exit criteria
- critical user flow works from login to fit-scored jobs in the dashboard
- major known blockers resolved
