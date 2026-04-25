---
name: docker-migrate-inspect
description: "MUST use when planning a Docker container migration, when user asks to move containers
             between servers, says 'migrate containers', 'move docker', 'what do I need to migrate',
             'inspect containers for migration', or when a new server is being prepared to receive
             containers from an existing server. Also triggers on 'migration plan', 'copy containers'."
---

# Docker Migrate Inspect — Container Migration Inspection Protocol
*Full structured inspection of Docker containers before any migration*

## Activation

When this skill activates, output:

`🐳 Running Docker migration inspection — mapping everything we need to move safely...`

Then execute the protocol below.

## Context Guard

| Context | Status |
|---------|--------|
| **Planning a container migration** | ACTIVE — full protocol |
| **Moving services between servers** | ACTIVE — full protocol |
| **Setting up a new server to receive containers** | ACTIVE — full protocol |
| **General Docker debugging** | DORMANT — use standard debugging instead |
| **Already completed inspection this session** | DORMANT — skip, reference existing data |

## Protocol

### Step 1: Container Discovery
Ask user to run on source server (or run via SSH if access available):
```bash
docker ps -a --format "table {{.Names}}\t{{.Image}}\t{{.Status}}\t{{.Ports}}"
docker network ls
find /home -name "docker-compose.yml" 2>/dev/null | grep -v "/var/lib/docker"
docker volume ls
```

### Step 2: Deep Inspect Each Target Container
For each container marked for migration, inspect its compose file:
```bash
cat /path/to/docker-compose.yml
```
For each compose file, extract and document:
- [ ] **Image**: official or custom build (`build: .`)? If custom, need Dockerfile too
- [ ] **Volumes**: named volume vs bind mount? Named = export/import needed. Bind = copy directory
- [ ] **Networks**: which external networks does it use?
- [ ] **Environment variables**: flag any sensitive keys (encryption keys, passwords, API keys)
- [ ] **Ports**: what ports are exposed? Internal only or public?
- [ ] **Memory limits**: any `deploy.resources.limits.memory` or `NODE_OPTIONS` set?
- [ ] **Dependencies**: does it depend on another container (e.g. DB)?

### Step 3: Volume Size Check
For each named volume being migrated:
```bash
docker system df -v | grep <volume-name>
# Or check size directly:
docker run --rm -v <volume-name>:/data ubuntu du -sh /data
```

### Step 4: Flag Critical Items
Before migration is approved, check for:
- [ ] 🔑 **Encryption keys** in env vars — must be preserved exactly (e.g. `N8N_ENCRYPTION_KEY`)
- [ ] 🗄️ **Database connections** — does container connect to a DB? Where will that DB be post-migration?
- [ ] 🏗️ **Custom builds** — if `build: .`, copy the entire build context (Dockerfile + assets)
- [ ] 📁 **Bind mounts** — these are raw directories, not Docker volumes. Must be `scp`'d separately
- [ ] 🌐 **Network topology changes** — if proxy (NPM) stays on old server, container ports must be re-exposed or tunnel set up

### Step 5: Produce Migration Checklist
Output a per-container table:

| Container | Type | Data to Move | Critical Flags | Estimated Size |
|-----------|------|-------------|----------------|---------------|
| `name` | official/custom | volume name or bind path | encryption key, etc. | Xmb |

Then summarize:
- What stays on old server
- What moves to new server
- Network/proxy changes needed
- Order of operations (databases before apps, etc.)

## Mandatory Rules

1. **Never start migration without checking encryption keys** — credentials will break if key changes
2. **Always check volume sizes before planning transfer** — large files need time/bandwidth planning
3. **Document network topology changes** — proxy, DNS, and tunnel implications must be planned before touching anything
4. **Databases migrate with data exports, not volume copies** — volume copies can corrupt if container was running

## Edge Cases

| Situation | Behavior |
|-----------|----------|
| **Custom Docker image** (`build: .`) | Must copy entire build context, not just compose file |
| **n8n migration specifically** | Flag `N8N_ENCRYPTION_KEY` — extract from `/home/node/.n8n/config` if not in env |
| **OTP migration** | Check for `graph.obj` or `.pbf` files in bind mount — can be 100MB–2GB |
| **DB container migration** | Always use proper dump tools (`mongodump`, `mysqldump`, `pg_dump`) — never raw volume copy for running DBs |
| **Cross-server migration** | Plan for: source backup → transfer → destination restore → verify → cutover → decommission |

## Level History

- **Lv.1** — Base: Full Docker container migration inspection checklist with per-container analysis and critical flag detection. (Origin: 2026-04-20, forged from VPS migration planning session pattern)
