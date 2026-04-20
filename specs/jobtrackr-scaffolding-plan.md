# JobTrackr MVP Scaffolding Plan

## Purpose
Turn the JobTrackr PM package into an implementation-ready repository and service layout for the MVP.

This plan covers:
- initial monorepo structure
- backend and frontend service boundaries
- local development setup
- the smallest build order that supports Gmail auth and ingestion first

## Stack
- Frontend: Next.js + React + TypeScript
- Backend: Go + Gin
- Database: PostgreSQL
- DB access: pgx + sqlc
- Local infrastructure: Docker Compose
- API contract: OpenAPI-generated or hand-maintained during MVP

## Recommended Monorepo Structure

```text
jobtrackr/
├── apps/
│   ├── web/
│   │   ├── app/
│   │   │   ├── (auth)/
│   │   │   ├── dashboard/
│   │   │   ├── settings/
│   │   │   └── api/optional/
│   │   ├── components/
│   │   ├── lib/
│   │   ├── hooks/
│   │   ├── styles/
│   │   ├── public/
│   │   ├── package.json
│   │   └── tsconfig.json
│   └── api/
│       ├── cmd/
│       │   └── api/
│       ├── internal/
│       │   ├── auth/
│       │   ├── config/
│       │   ├── domain/
│       │   ├── gmail/
│       │   ├── ingest/
│       │   ├── jobs/
│       │   ├── platform/
│       │   ├── server/
│       │   ├── store/
│       │   └── sync/
│       ├── db/
│       │   ├── migrations/
│       │   ├── queries/
│       │   └── sqlc.yaml
│       ├── scripts/
│       ├── go.mod
│       └── Makefile
├── packages/
│   └── api-client/
│       ├── src/
│       └── package.json
├── infra/
│   ├── docker-compose.yml
│   ├── env/
│   └── postgres/
├── docs/
│   ├── architecture/
│   ├── api/
│   ├── parsing/
│   └── runbooks/
├── .github/
│   └── workflows/
├── README.md
├── package.json
└── Makefile
```

## Service Boundaries

### apps/web
Owns:
- authentication entry UI
- dashboard pages
- settings pages
- Gmail connection state UI
- sync status visibility
- jobs list and detail experiences

Should not own:
- Gmail API access
- token handling
- background work
- business rules for ingestion or dedupe

### apps/api
Owns:
- Google OAuth handshake and callback
- session issuance and validation
- Gmail token encryption and persistence
- Gmail sync orchestration
- source email ingestion
- job extraction pipeline
- dedupe and upsert logic
- ingestion run tracking
- jobs CRUD APIs

### packages/api-client
Owns:
- generated or shared TS client models for `/api/v1`
- keeps frontend request types aligned with backend DTOs

### infra
Owns:
- local Postgres
- optional pgAdmin/Adminer in dev only
- environment templates
- local service dependencies

## Backend Module Layout

### internal/auth
Responsibilities:
- OAuth state generation and validation
- Google OAuth config and URL generation
- callback token exchange
- session creation and cookie management

Initial submodules:
- `oauth.go`
- `session.go`
- `middleware.go`
- `handlers.go`

### internal/gmail
Responsibilities:
- Gmail API client wrapper
- read scopes and token usage
- message list and message fetch operations
- Gmail message normalization into internal DTOs

Initial submodules:
- `client.go`
- `mapper.go`
- `scopes.go`

### internal/ingest
Responsibilities:
- ingestion orchestration
- candidate selection
- classification
- raw source email persistence
- extraction and downstream pipeline invocation

Initial submodules:
- `service.go`
- `scheduler.go`
- `pipeline.go`
- `handlers.go`

### internal/jobs
Responsibilities:
- job aggregate logic
- dedupe rules
- job upsert behavior
- job listing and retrieval

Initial submodules:
- `service.go`
- `dedupe.go`
- `handlers.go`

### internal/store
Responsibilities:
- sqlc-generated query usage
- transaction boundaries
- repository adapters

Initial submodules:
- `postgres.go`
- `repositories/`

### internal/server
Responsibilities:
- Gin router setup
- middleware registration
- health routes
- route grouping

## Frontend Module Layout

### app/(auth)
- sign-in page
- OAuth start flow trigger
- callback landing/redirect handling

### app/dashboard
- initial post-login screen
- sync status card
- recent ingested messages preview
- first jobs list placeholder

### app/settings
- Gmail account connection state
- reconnect and disconnect controls

### lib/api
- typed wrappers around backend endpoints
- session fetch
- gmail account fetch
- sync run trigger

### components
Initial useful components:
- `AuthButton`
- `ConnectionStatusCard`
- `SyncRunCard`
- `EmptyState`
- `JobsTable`

## Required Database Tables for Initial MVP Foundation

These should exist before broader product features.

### Phase A foundation tables
- `users`
- `sessions`
- `gmail_accounts`
- `ingestion_runs`
- `ingestion_errors`
- `gmail_messages`
- `jobs`

### Deferred tables for later UI completeness
- `job_tags`
- `job_tag_links`
- `job_activities`

## Local Development Setup

### Docker Compose services
Minimum:
- `postgres`

Optional but helpful:
- `adminer` or `pgadmin`

### Root scripts / tasks
Recommended top-level commands:
- `make dev` → start web + api locally
- `make infra-up` → boot Postgres
- `make infra-down`
- `make migrate`
- `make test`
- `make lint`

### API Makefile targets
- `run`
- `build`
- `test`
- `migrate-up`
- `migrate-down`
- `sqlc-generate`

### Web scripts
- `pnpm dev`
- `pnpm build`
- `pnpm lint`
- `pnpm typecheck`

## Initial Route Structure

### Frontend routes
- `/`
- `/login`
- `/dashboard`
- `/settings/account`

### Backend routes
- `GET /health/live`
- `GET /health/ready`
- `POST /api/v1/auth/google/start`
- `GET /api/v1/auth/google/callback`
- `POST /api/v1/auth/logout`
- `GET /api/v1/auth/session`
- `GET /api/v1/gmail/account`
- `POST /api/v1/gmail/connect/start`
- `POST /api/v1/gmail/disconnect`
- `POST /api/v1/sync/run`
- `GET /api/v1/sync/runs`
- `GET /api/v1/messages`
- `GET /api/v1/messages/:id`

## Smallest Useful Vertical Slice
The first slice should prove:
- Google OAuth works
- Gmail account is persisted
- raw Gmail messages can be fetched from Gmail API
- candidate messages can be stored in Postgres
- ingestion runs are observable
- frontend can show connection state and recent sync outcome

That is the minimum end-to-end slice before job extraction UI matters.

## Execution Order for Scaffolding

### Step 1. Repo bootstrap
- finalize monorepo layout
- initialize root README and task runner
- add docker compose
- scaffold Next.js app
- scaffold Go API

### Step 2. Shared foundation
- config loading
- env templates
- DB connection
- migration tooling
- sqlc setup
- health endpoints

### Step 3. Auth slice foundation
- create `users`, `sessions`, `gmail_accounts`
- wire Google OAuth config
- implement auth/session endpoints
- validate secure cookie session flow

### Step 4. Gmail ingestion slice foundation
- create `ingestion_runs`, `ingestion_errors`, `gmail_messages`
- implement Gmail client wrapper
- add manual sync endpoint
- persist raw Gmail messages and run outcomes

### Step 5. First UI wiring
- login page
- account/settings page
- connection state card
- sync trigger button
- recent run / message preview

## Definition of Ready for Broader Product Work
Before moving into parsing, jobs dashboard, or dedupe improvements, the repo should support:
- one-command local infrastructure start
- one-command migration run
- working Google OAuth login
- Gmail account persistence
- manual sync trigger
- raw message persistence
- visible sync run logs in DB and UI

## Out of Scope for This Scaffolding Package
- full parser coverage
n- enrichment scrapers
- fit analysis
- advanced dedupe review UI
- full jobs CRUD UI
