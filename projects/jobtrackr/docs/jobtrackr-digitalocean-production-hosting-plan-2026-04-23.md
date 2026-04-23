# JobTrackr DigitalOcean Production Hosting Plan

## Purpose

Ship JobTrackr to production on DigitalOcean with a setup that is inexpensive, simple to operate, and easy to upgrade later without re-architecting the app.

This plan assumes the current JobTrackr architecture:
- Next.js web app
- Go API
- PostgreSQL database
- Gmail API integration
- Google OAuth login
- low-to-moderate early production traffic

## Executive Recommendation

Use a **single DigitalOcean Droplet for the app tier** and a **DigitalOcean Managed PostgreSQL database**.

Recommended production starting shape:
- **App server:** 1 Droplet, **Basic Premium AMD, 2 vCPU / 4 GB RAM / 80 GB SSD**
- **Database:** **Managed PostgreSQL, 1 node, 1 vCPU / 1 GB RAM / 10 GB storage**
- **Storage:** optional DigitalOcean Spaces later only if file storage is added
- **TLS / edge:** Nginx on the Droplet with Let's Encrypt
- **Process management:** systemd or Docker Compose on the Droplet
- **Region:** same region for app + DB, preferably **NYC3** unless user geography suggests another US region

This is the best cost-effective starting point because it:
- keeps fixed costs reasonable
- avoids running PostgreSQL yourself in production
- gives enough headroom for Next.js + Go API + background sync jobs
- supports a clean upgrade path to App Platform, Kubernetes, or split services later

## Recommended Architecture

```text
Internet
  -> DigitalOcean Floating/Public IP
  -> Nginx on Droplet
     -> Next.js web app
     -> Go API service
     -> DigitalOcean Managed PostgreSQL
     -> Gmail API / Google OAuth / external APIs
```

## Why This Is the Best Starting Point

### Avoid the cheapest possible single-box database setup
A single tiny Droplet running web, API, and PostgreSQL would be cheaper at first, but it creates avoidable risk:
- harder backups and restore process
- higher blast radius if the Droplet fills disk or crashes
- more maintenance burden
- more production risk around data durability

For JobTrackr, the database matters too much to optimize all the way down to the absolute minimum.

### Avoid over-buying early
A larger 4 vCPU / 8 GB setup is probably unnecessary on day one unless there is already real traffic volume or heavy background processing.

The 2 vCPU / 4 GB Premium AMD Droplet is the sweet spot for:
- Next.js runtime
- Go API
- reverse proxy
- scheduled ingestion/sync work
- room for logs, deploys, and bursts

## Exact Production Sizing Recommendation

### 1) Application Droplet
**DigitalOcean Droplet:** Basic Premium AMD

Recommended size:
- **2 vCPU**
- **4 GB RAM**
- **80 GB SSD**

Why this size:
- 1 GB RAM is too tight for Next.js production builds, process overhead, and reverse proxy headroom
- 2 GB RAM can work, but will be fragile once app, API, logs, deploys, and background jobs all share the box
- 4 GB RAM is the first size that feels comfortably production-safe without being wasteful
- 80 GB SSD leaves room for deploy artifacts, logs, local caching, and rollback tolerance

### 2) Database
**DigitalOcean Managed PostgreSQL**

Recommended starting size:
- **1 vCPU**
- **1 GB RAM**
- **10 GB storage**
- **single primary node** to start

Why this size:
- JobTrackr data volume should start fairly small
- the workload is mostly transactional app reads/writes, not analytics
- managed backups and operational simplicity matter more than raw size early on

Upgrade trigger:
- move to 2 GB or add HA node when you have real user load, sustained sync volume, or stricter uptime requirements

## Estimated Monthly Cost

Approximate starting budget:
- **Droplet 2 vCPU / 4 GB Premium AMD:** about **$24/month**
- **Managed PostgreSQL 1 GB / 1 vCPU:** about **$15/month**
- **Backups for Droplet:** about **20% of Droplet cost** if enabled
- **Bandwidth:** likely included within normal early-stage usage
- **Domain/DNS:** negligible if already owned; DigitalOcean DNS itself is effectively free

### Expected total
Reasonable starting total:
- **about $39/month without Droplet backups**
- **about $44 to $49/month with safer backup choices enabled**

This is the cost-effective recommendation.

## What Not to Use First

### Not recommended for v1
1. **Kubernetes**
   - too much ops overhead for current stage
2. **Large multi-node app cluster**
   - unnecessary until traffic proves it
3. **Self-managed PostgreSQL on the same app box**
   - cheaper, but worse operationally
4. **App Platform as the default first choice**
   - simpler in some ways, but usually less cost-efficient than a Droplet for a mixed Next.js + Go API stack you want to control tightly

## Deployment Model

## Option A, Recommended: One Droplet, Two Services
Run on the app Droplet:
- `jobtrackr-web` for Next.js
- `jobtrackr-api` for Go API
- `nginx` for reverse proxy and TLS termination

Use either:
- **systemd services** for the simplest production setup
- or **Docker Compose** if you want containerized deploys

### Recommendation
Use **Docker Compose** if the team wants repeatable deploys and easier packaging.
Use **systemd** if you want the simplest and lightest-weight production host.

Given the current repo already has infra files and mixed app components, **Docker Compose is the better recommendation**.

## Production Networking

### Public ports
Open only:
- **80/tcp** for HTTP redirect to HTTPS
- **443/tcp** for HTTPS
- **22/tcp** for SSH, ideally restricted by source IP if possible

### Private connectivity
- connect app Droplet to Managed PostgreSQL over DigitalOcean trusted networking if available in chosen configuration
- keep PostgreSQL non-public if possible

## Domain and Routing

Recommended domains:
- `app.yourdomain.com` -> Next.js frontend
- `api.yourdomain.com` -> Go API

Alternative:
- serve both behind one domain with path routing
  - `/` -> Next.js
  - `/api` -> Go API

### Recommendation
Use **one primary domain with Nginx path routing** if you want the simplest external footprint.

Example:
- `jobtrackr.yourdomain.com`
  - `/` -> web
  - `/api` -> API

This keeps Google OAuth and app configuration simpler.

## Environment and Secrets

Store these as production secrets, not in repo:
- `DATABASE_URL`
- `GOOGLE_CLIENT_ID`
- `GOOGLE_CLIENT_SECRET`
- `GOOGLE_REDIRECT_URL`
- `FRONTEND_URL`
- session secrets / cookie secrets
- Gmail query and scheduler settings as needed

### Secret management recommendation
Good enough for v1:
- `.env` files on the server with locked-down permissions
- injected into Docker Compose or systemd

Better later:
- DigitalOcean Secrets via App Platform or external secret manager if the stack grows

## Background Jobs and Sync

JobTrackr needs scheduled Gmail sync behavior.

### Recommendation
Do **not** rely on ad hoc cron calling a localhost endpoint forever.

For production, use one of these:
1. **Internal scheduler in the Go service** for simple single-instance operation
2. **Host cron** that runs a dedicated sync command or authenticated internal endpoint

### Best production choice now
Use the **Go service scheduler** for the primary cadence, with clear env-configured interval settings.

Why:
- fewer moving parts
- easier to reason about in a single-instance deployment
- no separate cron drift to maintain

## Backups

### Required
1. **Enable Managed PostgreSQL backups**
2. **Enable Droplet backups or weekly snapshot policy**
3. **Store repo in GitHub as source of truth for code/config templates**

### Suggested backup posture
- PostgreSQL automated backups: enabled
- Droplet backups: enabled if budget allows, otherwise weekly snapshots minimum
- test restore procedure at least once before trusting the setup

## Monitoring and Ops

### Minimum production monitoring
Set up:
- DigitalOcean Droplet CPU, RAM, disk alerts
- uptime check on `/health/live`
- log rotation on the Droplet
- alert if Gmail sync starts failing repeatedly

### Nice next step
Add lightweight error tracking such as:
- Sentry for web + API
- or at minimum structured logs and a simple alerting path

## Security Baseline

Production baseline should include:
- Ubuntu LTS on the Droplet
- non-root deploy user
- SSH key auth only
- firewall enabled, only 22/80/443 open
- automatic security updates enabled
- fail2ban optional but reasonable
- TLS via Let's Encrypt
- database not exposed publicly unless absolutely required
- least-privilege Google OAuth credentials

## Recommended Region

Pick the region closest to expected users and keep app + DB together.

If users are mostly US-based and there is no stronger requirement yet:
- **NYC3** is a solid default

Other good US options depending on user base:
- **SFO3** for west-heavy traffic
- **TOR1** only if Canada-centric requirements matter

## Scaling Plan

### Phase 1, recommended now
- 1 app Droplet
- 1 managed PostgreSQL primary
- Nginx + Docker Compose

### Phase 2, when usage grows
Upgrade app Droplet to:
- **4 vCPU / 8 GB RAM**

Upgrade DB to:
- **2 GB+ managed PostgreSQL**
- add high availability if uptime becomes more important

### Phase 3, if traffic or team complexity grows further
Split services:
- web on App Platform or separate Droplet
- API on separate Droplet/App Platform service
- keep managed PostgreSQL
- add managed Redis only if job queues or caching actually require it

## Concrete Production Build Recommendation

### Final recommended stack
- **Hosting provider:** DigitalOcean
- **App compute:** 1 x Basic Premium AMD Droplet, **2 vCPU / 4 GB / 80 GB SSD**
- **OS:** Ubuntu 24.04 LTS
- **Database:** Managed PostgreSQL, **1 vCPU / 1 GB / 10 GB**, same region
- **Web server:** Nginx
- **App runtime:** Docker Compose
- **TLS:** Let's Encrypt
- **Deploy strategy:** GitHub Actions or manual deploy to Droplet
- **Backups:** managed DB backups + Droplet backups/snapshots
- **Monitoring:** DigitalOcean alerts + health endpoint checks

## Rollout Checklist

1. Create DigitalOcean project for JobTrackr
2. Provision one **Basic Premium AMD 2 vCPU / 4 GB / 80 GB** Droplet in **NYC3**
3. Provision Managed PostgreSQL in the same region
4. Configure DNS
5. Harden Droplet security baseline
6. Install Docker and Docker Compose plugin
7. Add Nginx reverse proxy
8. Add production env vars and secrets
9. Configure Google OAuth production redirect URLs
10. Deploy web and API containers
11. Run DB migrations
12. Validate health checks and login flow
13. Validate Gmail sync behavior
14. Enable backups and alerts
15. Document restore and rollback steps

## Cost-Down Alternative

If budget pressure is extreme, the lowest viable cheaper option is:
- **1 Droplet, Basic Premium AMD, 2 vCPU / 2 GB RAM / 60 GB SSD**
- self-managed PostgreSQL on the same box

I do **not** recommend this as the primary production plan.

Reason:
- it saves money up front, but increases risk and maintenance significantly
- the first time the box runs hot, fills disk, or needs restore work, the savings disappear fast

## Final Recommendation

For JobTrackr production on DigitalOcean, start with:

- **1 Basic Premium AMD Droplet: 2 vCPU / 4 GB RAM / 80 GB SSD**
- **1 Managed PostgreSQL instance: 1 vCPU / 1 GB RAM / 10 GB storage**
- **Ubuntu 24.04 LTS + Docker Compose + Nginx + Let's Encrypt**
- **Single-region deployment, same region for app and DB, default NYC3**

This is the best cost-effective production setup because it is:
- affordable
- simple to run
- safer than self-hosting Postgres
- strong enough for the current stage
- easy to scale up later without redoing the whole platform
