# JobTrackr Fit Analysis Spec v1

## Purpose

Define how JobTrackr evaluates job fit using Keith's uploaded resume, determines whether a role should be flagged, and stores output suitable for backend processing and table-based UI review.

## Goal

Help Keith focus on the most relevant jobs by automatically comparing tracked roles against his resume and surfacing likely matches.

## Scope

In scope for MVP:
- use uploaded resume as the fit-analysis input
- compare each tracked job against the active resume
- produce a fit result for each job
- flag likely good-fit jobs
- store a concise rationale
- support table display and job-detail display

Out of scope for MVP:
- tailoring the resume automatically
- drafting cover letters
- learning from user actions automatically
- personalized ranking across multiple users

---

## Inputs

### Required inputs
- active uploaded resume
- normalized tracked job record
- job description text or best available job summary

### Preferred job inputs
- title
- company
- location
- work mode
- employment type
- salary if available
- requirements text
- seniority

### Resume inputs
The system should use the uploaded resume to derive fit context, such as:
- current and prior roles
- skills and technologies
- years or depth of experience where inferable
- industry/domain experience where inferable
- leadership or management background where inferable

---

## Output Model

Each analyzed job should produce:
- `fit_flag` boolean
- `fit_score` integer from 0 to 100
- `summary` short human-readable explanation
- `strengths` list of positive fit factors
- `gaps` list of missing or weaker-fit factors
- `analyzed_at` timestamp

### MVP UI requirements
In table view, show at minimum:
- fit flag
- fit score as the user-facing `match rating`

In job detail view, show at minimum:
- fit summary
- top strengths
- top gaps

---

## Fit Dimensions

The analysis should compare the job and resume across these dimensions.

### 1. Role alignment
Does the target job title or function align with Keith's background?

Examples:
- PM to PM is strong alignment
- PM to engineering manager may be weak or mixed alignment depending on resume

### 2. Skills alignment
Do the required or preferred skills overlap with resume evidence?

Examples:
- product management
- AI/ML product exposure
- SaaS
- analytics
- stakeholder management
- domain tools or technologies

### 3. Seniority alignment
Is the requested level plausible for Keith based on resume evidence?

Examples:
- senior vs junior mismatch
- manager/director level alignment

### 4. Work constraints alignment
Does the role align with practical constraints visible from the job?

Examples:
- remote vs onsite
- location mismatch
- contract vs full-time mismatch when inferable

### 5. Experience gaps
Are there obvious must-have requirements not supported by the resume?

Examples:
- required domain expertise
- required years in a narrow specialty
- required certifications

---

## Scoring Model, MVP Recommendation

Use a 0 to 100 score internally and expose it in the first dashboard UI as the user-facing `match rating`.

### Suggested interpretation
- `80-100`: strong fit
- `60-79`: plausible fit
- `40-59`: weak or mixed fit
- `0-39`: poor fit

### Suggested flag rule
Set `fit_flag = true` when:
- `fit_score >= 70`

PM recommendation:
- keep threshold configurable
- start with `70`

---

## Reasoning Requirements

The system should not return only a number.

Each analysis must include:
- one short overall summary sentence or paragraph
- 2 to 5 strengths when available
- 1 to 5 gaps when available

### Good summary example
"Strong fit based on product management experience and relevant AI-focused background, with a possible gap around direct fintech domain experience."

### Bad summary example
"Score 82."

---

## Fit Flag Semantics

`fit_flag = true` means:
- the role appears worth Keith's attention
- the system believes the job is a reasonably strong match based on current resume evidence

`fit_flag = false` does not mean:
- the job is impossible
- the user should never apply

It only means:
- the system does not currently see enough evidence to prioritize it as a strong match

---

## Analysis Trigger Rules

Run fit analysis when:
- a new job is created
- an existing job is materially enriched or updated
- a new resume is uploaded and becomes active

### Reanalysis on resume upload
When Keith uploads a new resume:
- mark it as active
- rerun fit analysis for existing active jobs, either immediately or via background queue

---

## Missing Data Handling

### Missing resume
If no active resume exists:
- do not run fit analysis
- keep `fit_flag = false` or null depending on implementation preference
- mark fit state as unavailable or pending in detail view

PM recommendation:
- prefer nullable score and nullable summary, with an explicit "resume required" UI state

### Missing job description
If the job lacks a full description:
- run fit analysis using best available fields
- lower confidence if needed
- do not block analysis entirely

---

## Storage Rules

Persist each analysis in `job_fit_analysis` with:
- `job_id`
- `resume_id`
- `fit_flag`
- `fit_score`
- `summary`
- `strengths`
- `gaps`
- `model_name`
- `analyzed_at`

Mirror the latest analysis onto `job` for fast table queries:
- `job.fit_flag`
- `job.fit_score`
- `job.fit_summary`

---

## Functional Requirements

### FR1. Resume-based analysis
The system shall use Keith's active uploaded resume as the primary fit-analysis context.

### FR2. Job comparison
The system shall compare each tracked job against resume evidence.

### FR3. Score output
The system shall produce a numeric fit score from 0 to 100.

### FR4. Flag output
The system shall produce a boolean fit flag derived from the fit result.

### FR5. Human-readable rationale
The system shall produce a concise human-readable fit summary.

### FR6. Strength and gap capture
The system shall store structured or semi-structured strengths and gaps.

### FR7. Reanalysis
The system shall support rerunning fit analysis when a new resume is uploaded or job details materially change.

### FR8. Partial-data analysis
The system shall support fit evaluation using partial job data when complete detail is unavailable.

---

## Non-Functional Requirements

- results should be understandable, not just numeric
- threshold for flagging should be configurable
- analysis should be traceable to a specific resume version
- system should tolerate incomplete job data

---

## UI Guidance

### Table view
Show:
- fit flag as a clear yes/no visual state
- fit score as a visible `match rating` from 0 to 100
- pending or unavailable state when analysis has not run yet

### Job detail view
Show:
- short fit summary
- top strengths
- top gaps
- optionally analyzed timestamp

### Filtering
At minimum, support:
- flagged only
- unflagged only
- status filter

---

## Guardrails

The analysis should avoid overclaiming.

### Required behavior
- distinguish evidence from inference
- note meaningful missing requirements as gaps
- avoid treating keyword overlap as proof of qualification
- avoid hard-rejecting jobs solely due to missing data

### Not acceptable
- flagging nearly every job as a fit
- producing unsupported claims about Keith's background
- suppressing uncertainty when the source data is weak

---

## Open Questions

- Should the user be able to override fit flags manually?
- Should location and remote preference be hard filters or only scoring inputs?
- Should the system support separate thresholds for flagging and sorting?
