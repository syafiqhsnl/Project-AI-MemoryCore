# 🚍 Project: Public Transport (transport-api + GTFS ETL)
*[Integrated API, GraphQL, and high-performance GTFS data engine]*

## Project Overview
- **Type**: Full-Stack Transit Platform
- **Period**: 2026-04-09 - Active
- **Tech Stack**: Laravel (PHP), MySQL, OpenTripPlanner v2 (GraphQL), n8n (ETL)
- **Completion**: 95% (API) | Phase 2 Complete (GTFS Pipeline)

## 🏗️ Architecture & Infrastructure
- **API**: Laravel v11, Circuit Breaker, Per-endpoint Rate Limiting.
- **ETL Engine**: n8n (Self-Hosted on DigitalOcean).
- **GTFS Strategy**: 15,000 rows per transaction, 4x parallelism, prefix-based data integrity.

### Server Map
- **DO Server (168.144.109.47)**: n8n, OTP, Postgres (Ubuntu 24.04). Connected via Tailscale.
- **Sandbox Server (103.20.241.234)**: MySQL, MongoDB, transport-api, Nginx Proxy Manager (Ubuntu 24.04). Connected via Tailscale.
- **Networking**: Cloudflare Edge (sandboxapp.tech) -> Nginx Proxy Manager / Cloudflared Tunnel.
- **Local Dev**: PC (Full Docker), Laptop (XAMPP/Docker).

## 📅 Session History (Recent)

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
- **Prefix Logic**: Always use `prefix = category ?? provider` and slugify with underscores.
- **MySQL Fix**: Set "Detailed Output: ON" in Execute a SQL nodes to ensure reports see the SQL string.

---
**Last Updated**: 2026-04-25 | **Position**: #1 Active Project
