# JobTrakr MVP Stories v0

## Epic: Job ingestion and tracking

### Story 1
As Keith, I want job-related emails from Gmail to appear as tracked jobs so that I do not have to manually copy them into a tracker.

**Acceptance criteria**
- System can read selected Gmail job emails
- System creates a tracked job record from an email
- Duplicate emails for the same role do not create obvious duplicate jobs
- Tracked jobs are visible in the UI

### Story 2
As Keith, I want to view all tracked jobs in one interface so that I can see what needs attention.

**Acceptance criteria**
- UI shows a list of tracked jobs
- Each job shows core summary information
- I can open a job to view more detail

## Epic: Fit evaluation

### Story 3
As Keith, I want AI to assess whether a job looks like a good fit so that I can focus on the most promising roles.

**Acceptance criteria**
- System generates a fit recommendation for a tracked job
- Recommendation includes a short rationale
- Recommendation is visible in the UI

## Epic: Apply workflow

### Story 4
As Keith, I want help with promising jobs so that I can move faster on applications.

**Acceptance criteria**
- For a promising job, the system provides a clear next step toward applying
- The product indicates whether the job is ready to apply, needs review, or should be skipped
- Exact automation depth for apply flow remains to be defined
