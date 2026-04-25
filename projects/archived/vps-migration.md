# Project: VPS Migration (n8n + OTP → DO-Heavy-Node)
*Created: 2026-04-20 | Status: Planning Complete — Ready to Execute*

## Objective
Migrate n8n and OpenTripPlanner (OTP) from old sandbox VPS to new DigitalOcean server to resolve persistent OOM (Out of Memory) issues. Old server will continue running all other light containers.

## Server Map

| | Old Server (sandbox) | New Server (DO-Heavy-Node) |
|--|--|--|
| **IP** | 103.20.241.234 | 168.144.109.47 |
| **SSH Port** | 5522 | TBD (will set to 5522) |
| **User** | do | root → will create `do` |
| **RAM** | 1.9GB (heavily swapping) | 3.8GB |
| **Disk Free** | 25GB | 74GB |

## Final Architecture

```
Internet
   │
   ▼
Cloudflare Edge (SSL + DDoS)
   │
   ├── Tunnel ──► cloudflared @ DO-Heavy-Node
   │                  ├── n8n        (port 5678, internal)
   │                  └── OTP        (port 8080, internal)
   │
   └── DNS ──► Old Sandbox (103.20.241.234)
                  └── NPM ──► transport-api, portainer, etc.

Old Sandbox databases:
   ├── MongoDB      (transport-api)
   ├── MySQL        (transport-api)
   └── PostgreSQL   (NEW — for n8n on DO server)
                    └── UFW: port 5432 open to 168.144.109.47 only
```

## Source Files (Old Server)
- n8n compose: `/home/do/n8n/docker-compose.yml`
- n8n Dockerfile: `/home/do/n8n/Dockerfile`
- OTP compose: `/home/do/public-transport/otp/docker-compose.yml`
- OTP graph data: `/home/do/public-transport/otp/otp-data/` (482MB)
- n8n volume: `n8n_n8n_data` (named Docker volume)

## Key Technical Decisions
- **n8n DB**: Migrate SQLite → PostgreSQL (PostgreSQL stays on old server)
- **Credentials**: Recover `encryptionKey` from old n8n config → set as `N8N_ENCRYPTION_KEY` in new compose
- **License**: Community license key re-entered via Settings → License on new instance
- **Network exposure**: Cloudflare Tunnel (zero open ports on DO server, SSH only)
- **n8n custom image**: Built on `n8nio/n8n:2.10.3` + `bestzip` npm package + custom `zip` wrapper script

## Status
- [x] Phase 0 — Recon both servers complete
- [x] Phase 0 — Architecture decisions finalized
- [x] Phase 1 — New server hardening (do user + Port 5522)
- [x] Phase 2 — Install Docker CE on DO server
- [x] Phase 3 — Swap Space + Firewall + Fail2ban
- [x] Phase 4 — Add PostgreSQL to old server
- [x] Phase 5 — Get n8n encryption key from old server
- [x] Phase 6 — Export n8n workflows (JSON backup)
- [x] Phase 8 — Set up Tailscale Mesh Network (Old + New)
- [x] Phase 9 — Re-configure UFW (Drop public ports, allow Tailscale)
- [x] Phase 10 — Deploy n8n (Connected via Tailscale IP)
- [x] Phase 11 — Import n8n workflows & credentials
- [x] Phase 12 — Point Old NPM to New Tailscale IP
- [x] Phase 13 — Build OTP Graph (Handled by GitHub Action) -> BLOCKED by empty GTFS zips (All files 0-6KB)
- [ ] Phase 14 — Deploy OTP (Bridge to Transport API)
- [ ] Phase 15 — Final Verify + Cutover
