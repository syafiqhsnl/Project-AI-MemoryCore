# 📋 VPS Migration — Execution Plan
*Project: vps-migration | Created: 2026-04-20*

---

## Phase 1 — New Server Hardening (DO-Heavy-Node)

### 1.1 Create non-root user
```bash
adduser do
usermod -aG sudo do
rsync --archive --chown=do:do ~/.ssh /home/do
```
> ⚠️ Open a second terminal and test login as `do` BEFORE proceeding!

### 1.2 Harden SSH
```bash
nano /etc/ssh/sshd_config
# Set:
# Port 5522
# PermitRootLogin no
# PasswordAuthentication no
systemctl restart sshd
```

### 1.3 Update local SSH config (`~/.ssh/config`)
```
Host DO-Heavy-Node
    HostName 168.144.109.47
    Port 5522
    User do
    IdentityFile ~/.ssh/id_ed25519
```

---

## Phase 2 — Install Docker CE on DO Server

```bash
# Remove any existing docker
apt-get remove docker docker-engine docker.io containerd runc -y

# Add Docker's official GPG key and repo
apt-get update && apt-get install -y ca-certificates curl
install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
chmod a+r /etc/apt/keyrings/docker.asc
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
apt-get update
apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Verify
docker --version && docker compose version
```

---

## Phase 3 — Firewall + Fail2ban on DO Server

```bash
apt-get install -y fail2ban ufw git curl wget htop

# UFW — SSH only (Cloudflare tunnel needs no open ports)
ufw default deny incoming
ufw default allow outgoing
ufw allow 5522/tcp
ufw enable
ufw status verbose

# Fail2ban
systemctl enable fail2ban && systemctl start fail2ban
fail2ban-client status
```

---

## Phase 4 — Add PostgreSQL to Old Server (sandbox)

```bash
# On old server
mkdir -p /home/do/DB/postgres
nano /home/do/DB/postgres/docker-compose.yml
```

Compose file content:
```yaml
services:
  postgres:
    image: postgres:16-alpine
    container_name: postgres
    restart: always
    environment:
      - POSTGRES_USER=n8n
      - POSTGRES_PASSWORD=CHANGE_THIS_PASSWORD
      - POSTGRES_DB=n8n
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - db-net

volumes:
  postgres_data:

networks:
  db-net:
    external: true
```

```bash
# Start PostgreSQL
cd /home/do/DB/postgres && docker compose up -d

# Open port 5432 to DO server IP only
sudo ufw allow from 168.144.109.47 to any port 5432
sudo ufw status verbose
```

---

## Phase 5 — Extract n8n Encryption Key (OLD SERVER)

```bash
# On old server — get the encryption key
docker exec n8n cat /home/node/.n8n/config | grep encryptionKey
```
> 📝 Save the key value — you'll need it in Phase 8!

---

## Phase 6 — Export n8n Workflows (OLD SERVER)

In the n8n UI (old server):
1. Go to **Settings → Export all workflows**
2. Download the JSON file
3. Save it safely — will be imported to new n8n

---

## Phase 7 — Transfer OTP Graph Data

```bash
# From your LOCAL machine — copy graph.obj from old server to new server
scp -P 5522 student@103.20.241.234:/home/student/public-transport/otp/otp-data/graph.obj /tmp/graph.obj
scp -P 5522 /tmp/graph.obj do@168.144.109.47:/home/do/otp/otp-data/graph.obj
```

Or directly server-to-server (run on old server):
```bash
# First set up SSH key trust between servers, then:
scp -P 5522 /home/do/public-transport/otp/otp-data/graph.obj do@168.144.109.47:/home/do/otp/otp-data/
```

---

## Phase 8 — Deploy n8n on DO Server

### Directory structure on new server:
```
/home/do/n8n/
├── Dockerfile         (copy from old server)
├── docker-compose.yml (new version below)
```

### n8n Dockerfile (same as old server):
```dockerfile
FROM docker.n8n.io/n8nio/n8n:2.10.3

USER root
RUN npm install -g bestzip \
        && ln -sf "$(npm prefix -g)/bin/bestzip" /usr/local/bin/bestzip \
        && printf '%s\n' \
        '#!/bin/sh' \
        'set -e' \
        '' \
        '# Compatibility wrapper for workflows using: zip -r -j output.zip folder/' \
        'if [ "$#" -eq 4 ] && [ "$1" = "-r" ] && [ "$2" = "-j" ]; then' \
        '  out="$3"' \
        '  src="${4%/}"' \
        '  if [ -d "$src" ]; then' \
        '    tmpdir="$(mktemp -d)"' \
        '    if [ "${out#/}" = "$out" ]; then out_target="$(pwd)/$out"; else out_target="$out"; fi' \
        '    find "$src" -type f -exec cp -f {} "$tmpdir"/ \;' \
        '    (cd "$tmpdir" && /usr/local/bin/bestzip --force node "$out_target" ./*)' \
        '    rc=$?' \
        '    rm -rf "$tmpdir"' \
        '    exit $rc' \
        '  fi' \
        'fi' \
        '' \
        'exec /usr/local/bin/bestzip --force node "$@"' > /usr/local/bin/zip \
        && chmod +x /usr/local/bin/zip
USER node
```

### New docker-compose.yml for n8n (PostgreSQL + raised memory):
```yaml
services:
  n8n:
    build: .
    container_name: n8n
    restart: always
    command: start
    environment:
      - GENERIC_TIMEZONE=Asia/Kuala_Lumpur
      - TZ=Asia/Kuala_Lumpur
      - N8N_ENFORCE_SETTINGS_FILE_PERMISSIONS=true
      - N8N_SECURE_COOKIE=false
      - 'NODES_EXCLUDE=["n8n-nodes-base.localFileTrigger"]'
      - 'N8N_RESTRICT_FILE_ACCESS_TO=/tmp/gtfs;/home/node/.n8n-files'

      # Encryption key (copy from old server - Phase 5)
      - N8N_ENCRYPTION_KEY=PASTE_KEY_FROM_PHASE_5_HERE

      # PostgreSQL (on old sandbox server)
      - DB_TYPE=postgresdb
      - DB_POSTGRESDB_HOST=103.20.241.234
      - DB_POSTGRESDB_PORT=5432
      - DB_POSTGRESDB_DATABASE=n8n
      - DB_POSTGRESDB_USER=n8n
      - DB_POSTGRESDB_PASSWORD=CHANGE_THIS_PASSWORD

      # MEMORY LIMITS — raised for 4GB server
      - NODE_OPTIONS=--max-old-space-size=2048

      # Binary data on disk
      - N8N_DEFAULT_BINARY_DATA_MODE=filesystem

      # Execution logging
      - EXECUTIONS_DATA_SAVE_ON_SUCCESS=none
      - EXECUTIONS_DATA_SAVE_ON_ERROR=all
      - EXECUTIONS_DATA_SAVE_MANUAL_EXECUTIONS=false
      - EXECUTIONS_DATA_SAVE_EXECUTION_PROGRESS=false
      - EXECUTIONS_DATA_PRUNE=true
      - EXECUTIONS_DATA_MAX_AGE=12
      - EXECUTIONS_DATA_PRUNE_MAX_COUNT=50

    volumes:
      - n8n_data:/home/node/.n8n
    networks:
      - internal-net

volumes:
  n8n_data:

networks:
  internal-net:
    driver: bridge
```

```bash
# Build and start
cd /home/do/n8n && docker compose up -d --build
docker logs n8n -f
```

After starting:
1. Visit n8n via Cloudflare tunnel URL
2. Set up owner account (same email as before)
3. **Settings → License** → Enter community license key
4. **Import workflows** from Phase 6 JSON backup

---

## Phase 9 — Deploy OTP on DO Server

```bash
mkdir -p /home/do/otp/otp-data
# graph.obj should already be here from Phase 7

nano /home/do/otp/docker-compose.yml
```

```yaml
name: otp
services:
  otp:
    image: docker.io/opentripplanner/opentripplanner:2.5.0
    container_name: otp
    restart: always
    volumes:
      - ./otp-data:/var/opentripplanner
    command: ["--load", "--serve"]
    environment:
      # Raised from 1500m (old) to 2500m — server has 3.8GB
      - JAVA_OPTS=-Xmx2500m
    deploy:
      resources:
        limits:
          # Raised from 1800M to 3000M
          memory: 3000M
    networks:
      - internal-net

networks:
  internal-net:
    external: true
```

```bash
cd /home/do/otp && docker compose up -d
docker logs otp -f   # Watch for "Grizzly server running" message
```

---

## Phase 10 — Set up Cloudflare Tunnel on DO Server

```bash
# Install cloudflared
curl -L --output cloudflared.deb https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
dpkg -i cloudflared.deb

# Authenticate (follow browser link)
cloudflared tunnel login

# Create tunnel
cloudflared tunnel create do-heavy-node

# Configure tunnel
nano ~/.cloudflared/config.yml
```

Tunnel config:
```yaml
tunnel: <TUNNEL_ID>
credentials-file: /root/.cloudflared/<TUNNEL_ID>.json

ingress:
  - hostname: n8n.yourdomain.com
    service: http://localhost:5678
  - hostname: otp.yourdomain.com
    service: http://localhost:8080
  - service: http_status:404
```

```bash
# Run as service
cloudflared service install
systemctl start cloudflared
systemctl enable cloudflared
```

---

## Phase 11 — Verify + Cutover

- [ ] Visit `n8n.yourdomain.com` — login works, workflows present
- [ ] Visit `otp.yourdomain.com` — OTP responds to API calls
- [ ] n8n credentials still decrypt correctly
- [ ] n8n community license active

---

## Phase 12 — Stop on Old Server

```bash
# On old sandbox — stop the migrated containers
cd /home/do/n8n && docker compose down
cd /home/do/public-transport/otp && docker compose down
```
> Keep the files — don't delete yet until new server is stable for a few days!

---

*Plan by Aliq | Continue with: "resume plan" or "load project vps-migration"*
