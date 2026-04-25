# Active Reminders
*Persistent reminders that survive session changes. Updated at session end.*

## Open
- **GTFS Prefix Logic Migration**: Update the remaining Code nodes (Stops, Calendars, Trips, Stop Times, Shapes, Frequencies) with the Ultra-Stable Prefix logic to ensure no "unknown" agency_ids occur.
- **Aggregate Telegram Reports**: Move the Telegram node outside the loop to send one single summary message instead of 16 individual alerts.
- **API Environment Migration**: After n8n performance issues are fixed, update the database host and OTP endpoints in `transport-api` for both `local` and `prod` environments.
- **API Full Testing**: Run a complete test suite on `transport-api` post-migration to ensure no breakages.
- **Transport View Update**: Connect `transport-view` to the new OTP endpoint to resume development on the `nearby` page.
- **Nearby Page Map Swap**: In `transport-view`'s nearby page, replace the current map component with `shadcn-map` (https://shadcn-map.vercel.app/).
- **Local Dev Cleanup**: Assist user in migrating from XAMPP to a full Docker setup on the Laptop.

## Completed
- **Unify Public Transport Project** (completed 2026-04-25): Combined transport-api, GTFS ETL, and OTP logic into a single project file.
- **VPS Migration Archival** (completed 2026-04-25): Successfully moved n8n and OTP to DO-Heavy-Node; archived planning doc.
- **GTFS Integrity Guard** (completed 2026-04-25): Implemented double-check for empty data to prevent graph build failures.
- **GTFS Performance Optimization** (completed 2026-04-24): Reduced execution time from 1 hour to 2.5 minutes using Batch 15k and Loop Concurrency 4.
- **Check GTFS zip in n8n** (completed 2026-04-23): Solved empty zip issue using native n8n nodes.
- **OTP Build Config** (completed 2026-04-23): Add `build-config.json` with Asia/Kuala_Lumpur timezone.
- **Multi-Agent Sync PR** (completed 2026-04-23): Draft a Pull Request with the Feature/Multi-Agent-Sync-System folder files (README, install-script, template) for the upstream repository.
- **transport-api v3.0.0 Phase 6 & Issue #3** (completed 2026-04-12): Completed REST restructuring, ISO 8601 dates, and Issue #3 (Per-endpoint Rate Limiting). Verified with 872 tests.
