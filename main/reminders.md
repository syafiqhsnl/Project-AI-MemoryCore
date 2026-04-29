# Active Reminders
*Persistent reminders that survive session changes. Updated at session end.*

## Open
<!-- Active reminders go here. Format: -->
<!-- - **Short title**: Description of what needs to be done -->
<!-- - **Title** (by YYYY-MM-DD): Description with deadline -->

- **[URGENT BLOCKER] Phase 2 Prep: Error Logs UI Testing**: ✅ **COMPLETED** (Verified by user in production).
- **Week-Long API Soak Test (k6)**: Draft the Javascript k6 simulation script and set up a temporary $5 DigitalOcean "Attacker" droplet. The test will run 24/7 over a week to blast `transport-api` (Sandbox IP) with simulated user traffic (ramp-ups and lulls) to check for memory leaks and connection timeouts.
- **transport-view**: Connect to the new OTP endpoint to resume development on the nearby page, and swap the current map component to shadcn-map.
- **Laptop Migration**: Move the laptop development environment to Docker to match the production/VPS parity.

## Completed
- **Production Dashboard Stabilization** (completed 2026-04-30): Resolved "Cookie Pollution" 419 errors, fixed "Dubious Ownership" Git issues, and fully enabled Laravel Pulse system metrics (CPU/RAM) via dedicated background worker.
- **Phase 2 & Production Deployment** (completed 2026-04-30): Verified Error Logs UI in prod, resolved 419/404 issues via asset publishing, and merged all features into main.
- **Production Overhaul & PHP 8.3 Migration** (completed 2026-04-30): Successfully merged deployment fixes, upgraded Docker infrastructure to PHP 8.3, and verified production stability with the new tabbed dashboard.
- **Full Provider Sync Test & n8n Payload Sizes** (completed 2026-04-29): Fixed KTMB provider node, successfully tracked accurate file payload sizes using raw buffer injection in n8n, and verified dashboard tracking with proper GTFS sequential breakdown UI.
- **Command Center & n8n Bridge** (completed 2026-04-27): Finalized transport-api Sprint 4. Built Analytics Hub, Database Overview, n8n Bulk Sync (Upsert), and Heartbeat Monitoring.
- **transport-api** (completed 2026-04-27): Updated DB/OTP endpoints for local/prod; verified system health gauges and n8n webhook integration.
- **Unify Public Transport Project** (completed 2026-04-25): Combined transport-api, GTFS ETL, and OTP logic into a single project file.
- **VPS Migration Archival** (completed 2026-04-25): Successfully moved n8n and OTP to DO-Heavy-Node; archived planning doc.
- **GTFS Integrity Guard** (completed 2026-04-25): Implemented double-check for empty data to prevent graph build failures.
- **GTFS Performance Optimization** (completed 2026-04-24): Reduced execution time from 1 hour to 2.5 minutes using Batch 15k and Loop Concurrency 4.
- **Check GTFS zip in n8n** (completed 2026-04-23): Solved empty zip issue using native n8n nodes.
- **OTP Build Config** (completed 2026-04-23): Add `build-config.json` with Asia/Kuala_Lumpur timezone.
- **Multi-Agent Sync PR** (completed 2026-04-23): Draft a Pull Request with the Feature/Multi-Agent-Sync-System folder files (README, install-script, template) for the upstream repository.
- **transport-api v3.0.0 Phase 6 & Issue #3** (completed 2026-04-12): Completed REST restructuring, ISO 8601 dates, and Issue #3 (Per-endpoint Rate Limiting). Verified with 872 tests.
