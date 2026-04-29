# 🚍 Project: Public Transport (transport-api + GTFS ETL)
*[Integrated API, GraphQL, and high-performance GTFS data engine]*

## Project Overview
- **Type**: Full-Stack Transit Platform
- **Period**: 2026-04-09 - Active
- **Tech Stack**: Laravel (PHP), MySQL, OpenTripPlanner v2 (GraphQL), n8n (ETL)
- **Completion**: 98% (API) | Phase 2 Complete (GTFS Pipeline)

## 🏗️ Architecture & Infrastructure
- **API**: Laravel v12, Circuit Breaker, Per-endpoint Rate Limiting, **Bulk Sync API**.
- **ETL Engine**: n8n (Self-Hosted on DigitalOcean), Dashboard-integrated trigger.
- **GTFS Strategy**: 15,000 rows per transaction, 4x parallelism, prefix-based data integrity, **Batch Upserts**.

### Server Map
- **DO Server (168.144.109.47)**: n8n, OTP, Postgres (Ubuntu 24.04). Connected via Tailscale.
- **Sandbox Server (103.20.241.234)**: MySQL, MongoDB, transport-api, Nginx Proxy Manager (Ubuntu 24.04). Connected via Tailscale.
- **Networking**: Cloudflare Edge (sandboxapp.tech) -> NPM. **Local PHP Serve: --host=0.0.0.0 for Tailscale visibility.**

## 📅 Session History (Recent)

### 2026-04-27 - Sprint 4: Command Center & Automation
- **Changes**: Built Analytics Hub (Trend/Radar), Database Overview, and n8n Heartbeat bridge. Implemented `/api/sync/batch` (Upsert) and `/api/sync/report` endpoints.
- **Achievement**: Fully automated GTFS data pipeline with dashboard triggering and real-time status monitoring. Fixed Tailscale connection bottleneck for local dev.
- **Time Spent**: ~3 hours

### 2026-04-25 - Infrastructure & Integrity Guard
- **Changes**: Mapped full server/device infrastructure (DO + Sandbox + PC/Laptop). Implemented "Double Guard" (Routes + Stop Times > 0) in n8n.
- **Achievement**: 100% accurate reporting for Agency node. Pipeline stabilized for 16 providers.
- **Time Spent**: ~2 hours

### 2026-04-24 - GTFS Turbo Architecture
- **Changes**: Implemented 15k SQL batching and 4x parallelism in n8n.
- **Achievement**: Reduced pipeline execution from 60m to 2.5m (16 providers).
- **Time Spent**: ~4 hours

### 2026-04-12 - Issue #3 Completion
- **Changes**: Implemented Rate Limiting with Per-IP isolation. PR #60 merged.
- **Achievement**: 872 tests passed.

## 📝 Technical Notes
- **Repository**: d:\OneDrive\Project\Laravel\transport-api
- **n8n Workflow**: https://auto.sandboxapp.tech/workflow/jsZTEHJ7egbClBcg
- **Sync Endpoints**: `/api/sync/batch` (Upsert logic) and `/api/sync/report` (Dashboard Heartbeat).
- **Prefix Logic**: Always use `prefix = category ?? provider` and slugify with underscores.
- **MySQL Fix**: Set "Detailed Output: ON" in Execute a SQL nodes to ensure reports see the SQL string.

---
**Last Updated**: 2026-04-27 | **Position**: #1 Active Project
