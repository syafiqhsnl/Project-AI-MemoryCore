# Decision Log
*Append-only record of non-obvious decisions.*
*Only log decisions where future-us would wonder "why did we do it this way?"*

---

<!-- Decisions are added below this line. Format: -->
<!-- ## YYYY-MM-DD -- Short title -->
<!-- **Context**: What situation prompted this decision -->
<!-- **Decision**: What was chosen (and what was rejected) -->
<!-- **Rationale**: Why -- trade-offs, constraints, evidence -->

## 2026-04-21 -- OTP Graph Build via GitHub Actions
**Context**: OTP graph building (OSM + GTFS) is memory-intensive and often causes OOM on 4GB VPS servers.
**Decision**: Offload the build process to GitHub Actions runners (7GB RAM) and only transfer the final `graph.obj` to the VPS.
**Rationale**: Keeps the VPS stable while allowing complex graph builds. Rejected local builds due to cross-platform compatibility issues between Windows/Linux for Java objects.

## 2026-04-21 -- GitHub Action Memory Limit (5G)
**Context**: Setting Java `-Xmx6G` on a 7GB runner caused stability issues/failed builds.
**Decision**: Lowered heap limit to `5G`.
**Rationale**: Leaves 2GB for the OS and runner overhead to prevent intermittent OOM kills.

## 2026-04-30 -- Session & Monitoring Stabilization
**Context**: Persistent 419 Page Expired errors and empty Pulse widgets in Production.
**Decision**: Implemented unique session cookie naming (`transport_api_session_prod`) and forced `session.domain = null`. Added a dedicated `pulse` container service for background monitoring and installed `procps`.
**Rationale**: Browser "Cookie Pollution" (duplicate cookies from parent/subdomains) was causing CSRF mismatches. Pulse server metrics require a persistent background process to execute `pulse:check` and OS-level tools like `top` (procps).

