# JobTrakr PRD v0

## Product Summary

JobTrakr is a job-search workflow tool for Keith Goff. It ingests job opportunities from Gmail, tracks them in one place, helps evaluate fit with AI, and assists with applications when a role is worth pursuing.

## Problem

Job searching takes too long because opportunities arrive across email and require repeated manual work to review, compare, track, and apply.

## Goal

Reduce the time and effort required to move from inbound job email to a clear decision: ignore, track, review, or apply.

## Primary User

- Keith Goff

## Core Jobs To Be Done

- When job emails arrive, I want them automatically tracked so I do not miss opportunities.
- When I review tracked jobs, I want to quickly understand whether a role is a good fit.
- When a role looks promising, I want help preparing or completing the application.

## MVP Scope

### In scope
- Connect to Gmail or ingest job-related emails from Gmail
- Identify and track job opportunities from email
- Show tracked jobs in a UI
- Provide AI-assisted fit evaluation
- Support an application workflow for promising jobs

### Not yet defined
- Whether v1 submits applications automatically
- Whether v1 drafts resumes or cover letters
- Whether v1 supports multiple users
- Whether v1 tracks interview pipeline after application

## User Flow

1. Job email arrives in Gmail
2. JobTrakr detects and parses the opportunity
3. JobTrakr creates or updates a tracked job record
4. Keith views tracked jobs in the UI
5. AI evaluates likely fit
6. Keith reviews recommendation
7. Keith chooses to ignore, save, or apply
8. Product supports the apply step at the chosen MVP depth

## Key Open Questions

- What counts as a job email?
- What data should be extracted from each email?
- What fit criteria matter most?
- What level of automation is acceptable for applying?
- What statuses should jobs move through?

## Success Criteria

- Keith can see inbound job opportunities in one place
- Keith can quickly distinguish stronger fits from weaker ones
- Keith spends materially less time handling job applications
