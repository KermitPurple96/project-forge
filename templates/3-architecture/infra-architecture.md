# 🔀 Infrastructure Architecture

> Claude can generate infrastructure diagrams from this description.

## High-level overview

<!-- Describe the architecture in words. Claude can turn this into a diagram. -->
<!-- e.g. "User hits CloudFlare CDN → Next.js on Vercel → API on Railway → PostgreSQL on Supabase" -->



## Components

| Component | Service/tool | Purpose |
|-----------|-------------|---------|
| CDN | | Static assets, caching |
| Web server | | Frontend rendering |
| API server | | Business logic |
| Database | | Data storage |
| Cache | | Session/query cache |
| Queue | | Background jobs |
| Storage | | Files, uploads |
| Email | | Transactional emails |
| DNS | | Domain management |

## Environments

| Environment | URL | Purpose |
|-------------|-----|---------|
| Local | localhost:3000 | Development |
| Staging | staging.yourapp.com | Pre-production testing |
| Production | yourapp.com | Live |

## Scaling strategy

<!-- How does each component scale? Auto-scaling, manual, N/A? -->


