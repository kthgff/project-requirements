# JobTrackr DigitalOcean Production Hosting Plan

## Purpose
Define a DigitalOcean production hosting plan for JobTrackr that fits a **hard target of under $15/month** while staying operationally realistic for an MVP.

## Executive Summary
If the budget cap is truly **under $15/month**, the architecture has to be aggressively simple.

### Recommended sub-$15/month production shape
- **1 DigitalOcean Basic Droplet:** **1 vCPU / 1 GB RAM / 25 GB SSD**
- **Ubuntu LTS** on that Droplet
- **Caddy** or Nginx for reverse proxy and TLS
- **Next.js frontend** on the same box
- **Go API** on the same box
- **PostgreSQL** on the same box
- **Cloudflare free plan** for DNS and optional proxying
- **No Managed PostgreSQL**
- **No Spaces initially**
- **No load balancer**
- **No Redis**
- **No staging server always running**

This is the only cost-realistic way to keep the JobTrackr production stack on DigitalOcean under the requested budget.

## The Honest Tradeoff
I want to be very direct here: under **$15/month**, you are choosing **cost minimization over resilience**.

That means:
- one machine hosts both the app and the database
- backups are leaner and more manual
- memory headroom is tight
- deploy errors have a bigger blast radius
- the first real scale or reliability need will require an upgrade

Still, if the requirement is a hard budget ceiling, this is the right answer.

## Recommended Topology

```text
Internet
  -> Cloudflare DNS / proxy
  -> Single DigitalOcean Droplet
     -> Caddy or Nginx
     -> Next.js web app
     -> Go API
     -> PostgreSQL
     -> scheduler / cron for Gmail sync
```

## Exact Infrastructure Recommendation

### 1) App + API + DB host
**Service:** DigitalOcean Basic Droplet  
**Size:** **1 vCPU / 1 GB RAM / 25 GB SSD**  
**Region:** **NYC3**  
**OS:** **Ubuntu 24.04 LTS**

### Why this exact size
This is the smallest practical production starting point on DigitalOcean for this stack if cost is the main constraint.

It gives you just enough room for:
- Linux OS
- reverse proxy
- Go API
- local PostgreSQL
- lightweight Node/Next.js runtime
- basic logs and deploy artifacts

It will be tight, but workable if the app is tuned carefully.

### 2) Database
**Service:** local PostgreSQL on the same Droplet

### Why local PostgreSQL
Managed PostgreSQL immediately breaks the budget. If the monthly target must stay under $15, the database has to live on the application server.

### PostgreSQL operating notes
- keep memory settings conservative
- keep max connections low
- tune for small-instance usage
- run regular dumps
- do not expose PostgreSQL publicly

### 3) Object storage
Do **not** provision DigitalOcean Spaces initially.

To stay under budget:
- avoid user-uploaded file storage if possible in the MVP
- keep the MVP focused on application records and metadata
- revisit object storage only when the product needs durable files

### 4) Edge and networking
Use:
- **Cloudflare free plan** for DNS and optional proxying
- **DigitalOcean Cloud Firewall**
- only ports **80**, **443**, and **22** open
- SSH restricted by source IP where possible

## Cost Estimate
These are planning estimates and should be verified against current DigitalOcean pricing at provisioning time.

### Expected monthly cost
- **DigitalOcean Basic Droplet, 1 vCPU / 1 GB / 25 GB:** target this as the main spend
- **Cloudflare free plan:** $0
- **Managed PostgreSQL:** $0, not used initially
- **Spaces:** $0, not used initially
- **Load balancer:** $0, not used initially

### Total target
**Expected monthly total:** approximately **$12 to $14/month**

### Important note on backups
DigitalOcean automated Droplet backups may push the total over the cap, so this plan assumes:
- no paid Droplet backup add-on initially
- manual snapshots only when needed
- regular SQL dumps for database safety

## Runtime Recommendation

### Process layout on the single Droplet
- **Caddy** preferred for TLS and reverse proxy simplicity
- **Go API** as a persistent service
- **PostgreSQL** as a local persistent service
- **Next.js** app as a persistent service
- **scheduler** inside the Go app or a lightweight cron job

### Recommendation on process management
Use **systemd**, not Kubernetes, not a full orchestration stack.

Why:
- less RAM overhead
- simpler recovery model
- fewer moving parts on a tiny box

## Best-effort reliability guidance on a tiny server

### Reduce memory pressure
To make this fit better on 1 GB RAM:
- minimize always-on processes
- avoid unnecessary observability agents
- keep Node runtime lean
- limit concurrent background jobs
- prefer one scheduler path, not overlapping scheduler + cron setups

### Frontend recommendation
If possible, reduce runtime complexity by:
- keeping the Next.js app simple
- avoiding heavy SSR patterns unless required
- considering more static rendering where feasible

On a 1 GB server, waste gets expensive fast.

## Security Baseline

### Required controls
- Ubuntu LTS
- SSH keys only
- disable password SSH login
- DigitalOcean Cloud Firewall enabled
- ports 80/443 public, 22 restricted when practical
- automatic security updates enabled where safe
- app secrets stored on the host, not in git
- PostgreSQL bound locally or to private interfaces only
- TLS enabled through Caddy or Nginx + Let’s Encrypt

## Backup Plan
Since managed services are out of budget, the backup plan needs to be lean and disciplined.

### Recommended backup approach
1. nightly `pg_dump` of the PostgreSQL database
2. compress dump files
3. keep a short local retention window
4. copy dumps off-box when possible
5. create manual Droplet snapshots before major releases or upgrades

### Important warning
If there is **no off-box backup**, then this is a real production risk and should be acknowledged explicitly.

## Deployment Recommendation

### Keep deployment simple
Use one host with:
- reverse proxy
- app services
- local database

### Suggested deployment path
- build artifacts in CI or locally
- copy deployable app to Droplet
- restart services via `systemctl`
- run migrations carefully before app restart when needed

## Monitoring Recommendation
On this budget, monitoring should stay lightweight.

### Minimum viable monitoring
- health endpoint for API
- simple uptime check from an external monitor
- DigitalOcean basic monitoring enabled
- log rotation enabled
- alert if Gmail sync repeatedly fails

## What not to do at this budget
Do **not** add these on day one if the budget ceiling is hard:
- Managed PostgreSQL
- Spaces
- load balancer
- Redis
- a second Droplet for workers
- a full staging environment
- Kubernetes

Every one of those breaks the budget or increases complexity too quickly.

## Upgrade Path
This budget architecture should be treated as the first step, not the forever shape.

### First upgrade, when budget can increase
Spend the next dollars in this order:
1. move PostgreSQL to **Managed PostgreSQL**
2. increase the Droplet to **2 vCPU / 2 GB** or **2 vCPU / 4 GB**
3. add object storage if uploads/files become part of the product

### Why this order
Moving the database off the app box gives the biggest operational safety improvement per dollar.

## Provisioning Checklist
1. Create a DigitalOcean project for JobTrackr
2. Provision **1 Basic Droplet, 1 vCPU / 1 GB / 25 GB** in **NYC3**
3. Install Ubuntu updates
4. Create non-root deploy user
5. Configure SSH keys and disable password login
6. Configure DigitalOcean Cloud Firewall
7. Install Caddy or Nginx
8. Install PostgreSQL locally
9. Deploy Go API and Next.js app
10. Configure systemd services
11. Configure TLS
12. Set production environment variables
13. Run DB migrations
14. Configure Gmail sync scheduler
15. Configure nightly `pg_dump`
16. Validate login, sync, and health endpoints

## Final Recommendation
If the requirement is **under $15/month on DigitalOcean**, the best-fit production setup for JobTrackr is:

- **1 Basic Droplet: 1 vCPU / 1 GB RAM / 25 GB SSD**
- **Ubuntu 24.04 LTS**
- **Caddy or Nginx**
- **Next.js + Go API + PostgreSQL all on the same host**
- **Cloudflare free plan**
- **No managed DB, no Spaces, no load balancer**

It is lean, constrained, and not luxurious, but it is the most honest and workable architecture that meets the budget target.

## Recommendation from me
If you want the absolute cheapest realistic production start, use this.

If you want the **first reliability upgrade**, spend the next dollars on moving PostgreSQL off-box before almost anything else.
