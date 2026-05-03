# JobTrakr Brand and Product Site Plan

## Purpose
Define how the new brand direction becomes real inside the product and through a public product site.

This plan translates the brand manifesto into implementation-ready web pages, content requirements, and tickets.

## Research Summary
Current SaaS/product-site guidance consistently points to a few core page types:
- homepage with clear outcome, product visuals, proof, and call to action
- product/features page explaining the product in more depth
- pricing page when there is a monetization story or waitlist segmentation
- about/mission page for trust, story, and values
- blog/resources page for SEO and education
- contact/demo/waitlist page for conversion
- privacy/security pages when the product handles sensitive user data

For JobTrakr, the product is early and handles sensitive career data, Gmail access, and resumes. That means the first product site should be smaller than a mature SaaS marketing site, but stronger on trust and clarity.

## Brand Foundation

### Working Brand Position
JobTrakr is evolving from an email tracker into a job-search command center.

The product finds jobs from multiple sources, parses and deduplicates them, compares them against the user's resume, and helps the user decide what is worth acting on.

### Brand Promise
Know what is worth your time.

### Mission
Help job seekers find, review, and act on better opportunities by turning scattered job sources, resumes, and application workflows into one intelligent job-search command center.

### Vision
A world where every job seeker has a personal career engine that finds the right opportunities, explains the fit, and helps them move forward with confidence.

### Voice
- sharp
- calm
- discerning
- modern
- empowering

### Naming Direction
The current repo still uses JobTrakr, but naming exploration should continue before launch.

Strong naming lanes:
- command center names
- signal and clarity names
- momentum and apply-flow names
- fit and match names
- scout and discovery names

The public site implementation should keep copy modular enough to swap the final product name later without rewriting page structure.

## Visual Identity Direction

### Style
Sleek and professional. The product should feel like a career command center, not a playful job board or generic productivity app.

### Color Direction
Primary:
- deep navy for trust, focus, and control
- electric cyan for signal, intelligence, and active states

Secondary:
- green for fit/progress/opportunity
- warm slate for readable UI and calm data surfaces
- amber for incomplete or warning states

### Typography Direction
Use a modern geometric or highly legible sans-serif.

Preferred font style:
- confident sans-serif headings
- readable sans-serif body text
- compact data/table typography

## Product Site Information Architecture

### MVP Public Site Pages
These should be created first.

#### 1. Homepage
Purpose: explain the product in under five seconds and drive sign-in or waitlist/demo action.

Required sections:
- hero with direct value proposition
- short problem statement: job search is scattered and noisy
- product promise: one command center for jobs, resumes, fit, and workflow
- core capability cards:
  - discover jobs from Gmail, Indeed, LinkedIn, and future sources
  - parse and preserve resume context
  - score roles against the active resume
  - review and track jobs in one dashboard
- visual product preview area using dashboard/search-profile/resume-fit concepts
- trust notes for Google OAuth, Gmail readonly access, and encrypted resume storage
- primary CTA: sign in with Google or join waitlist depending on launch stage

#### 2. Product / How It Works
Purpose: show the workflow from sources to dashboard to applying.

Required sections:
- connect Google and create search profiles
- source jobs from email and job boards
- deduplicate and normalize job records
- upload and parse resume
- score jobs and surface fit signals
- review, save, archive, and track application status

#### 3. Resume and Fit Page
Purpose: explain the resume parsing and fit scoring feature clearly.

Required sections:
- PDF resume upload
- encrypted original storage
- parsed candidate profile
- active resume version
- fit scoring and historical traceability
- parsing fallback with pasted text
- privacy statement for resume data

#### 4. Sources Page
Purpose: explain where jobs come from and why source provenance matters.

Required sections:
- Gmail job alerts and recruiter emails
- Indeed and LinkedIn sourcing
- search profiles
- source chips and merged-source provenance
- duplicate merge behavior

#### 5. Pricing / Early Access
Purpose: convert interest without overcommitting to a final pricing model.

Required sections:
- early-access positioning
- expected MVP capabilities
- simple plan placeholder such as `Early access`
- FAQ about launch stage, Gmail permissions, resume privacy, and supported sources

Default MVP stance: if pricing is not decided, use an early-access page instead of a fake pricing table.

#### 6. Privacy and Security
Purpose: build trust for Gmail and resume handling.

Required sections:
- Google OAuth and Gmail readonly access
- resume PDFs encrypted at rest
- user-scoped private resume records
- source-email retention for traceability
- no selling user data
- user disconnect and archive behavior

### Later Site Pages
Create these after the first public site is useful.
- About / Mission
- Blog / Resources
- Changelog
- Contact
- Compare pages
- Integration pages for Gmail, Indeed, LinkedIn
- Customer stories after real usage exists

## Navigation

MVP navigation:
- Product
- Resume + Fit
- Sources
- Early Access
- Privacy
- Sign in

Mobile navigation should collapse cleanly and keep Sign in or Join Waitlist as the persistent primary action.

## Content Guardrails
- Lead with outcomes, not feature inventory.
- Do not promise automatic applying in MVP.
- Do not imply Gmail write access.
- Do not imply parsed resume data is user-editable in MVP.
- Do not claim AI will decide for the user.
- Explain fit scoring as guidance, not judgment.
- Keep the dashboard as the center of gravity.

## Implementation Plan

### Phase 1. Brand System Foundation
- choose temporary launch name or confirm JobTrakr remains working name
- define brand tokens for color, typography, spacing, and UI tone
- define logo direction or wordmark placeholder
- write homepage hero, product promise, mission, and trust copy

### Phase 2. MVP Product Site Pages
- create homepage
- create Product / How It Works page
- create Resume and Fit page
- create Sources page
- create Early Access page
- create Privacy and Security page

### Phase 3. App Integration
- align login page with brand visual system
- align dashboard empty state with brand promise and search-profile CTA
- align resume management UI with brand voice and trust language
- align fit-score UI copy with "guidance, not judgment"

### Phase 4. QA and Launch Review
- review all pages for claims against current requirements
- test mobile and desktop layouts
- verify CTAs route correctly
- verify privacy/security copy matches actual implementation
- review accessibility basics: contrast, keyboard focus, semantic headings

## Tickets To Create
- Brand system foundation for JobTrakr product and site
- Public homepage for job-search command center positioning
- Product / How It Works page
- Resume and Fit product page
- Sources and provenance product page
- Early Access / Pricing placeholder page
- Privacy and Security page for Gmail and resume trust
- Brand integration pass for login, dashboard empty state, and resume UI
- Product-site QA and claim review

## Source Requirements
- `projects/jobtrackr/specs/jobtrackr-requirements.md`
- `projects/jobtrackr/prd/jobtrackr-prd-v2.md`
- `projects/jobtrackr/specs/jobtrackr-frontend-plan-v1.md`
- `projects/jobtrackr/specs/jobtrakr-auth-spec-v1.md`
- `projects/jobtrackr/specs/jobtrackr-fit-analysis-spec-v1.md`
- `projects/jobtrackr/specs/jobtrackr-api-contract.md`
- `projects/jobtrackr/specs/jobtrackr-sourced-jobs-frontend-continuity-contract-2026-05-02.md`
- `projects/jobtrackr/specs/jobtrackr-ui-palette-direction-2026-05-02.md`
