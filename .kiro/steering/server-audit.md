---
inclusion: always
---
---
name: server-audit
description: "MUST use when user asks to audit a server, asks 'what is on this server',
             says 'gather server info', 'inspect server', 'server recon', 'check server specs',
             or when starting any migration or deployment planning session on a new or existing server.
             Also triggers on 'survey server', 'what's running on', 'check the vps'."
---

# Server Audit — Full Server Reconnaissance
*One-shot read-only audit covering specs, security, Docker, and services*

## Activation

When this skill activates, output:

`🔍 Running full server audit — I'll need you to paste the output back...`

Then execute the protocol below.

## Context Guard

| Context | Status |
|---------|--------|
| **New server onboarding** | ACTIVE — full protocol |
| **Migration planning** | ACTIVE — full protocol |
| **Deployment preparation** | ACTIVE — full protocol |
| **Routine curiosity check** | ACTIVE — full protocol |
| **Already have full server info this session** | DORMANT — skip, use existing data |

## Protocol

### Step 1: Confirm SSH Access First
Before running any remote commands, verify access:
```bash
ssh HOST "whoami && hostname"
```
- If access confirmed → proceed to Step 2
- If access denied → ask user to run commands manually and paste output

### Step 2: Deliver Audit Command
Provide the user with this single compound command to run on the target server:

```bash
echo "=== OS ===" && cat /etc/os-release && \
echo "=== CPU ===" && nproc && lscpu | grep "Model name" && \
echo "=== MEMORY ===" && free -h && \
echo "=== DISK ===" && df -h && \
echo "=== SSH CONFIG ===" && grep -E "^Port|^PermitRootLogin|^PasswordAuthentication|^PubkeyAuthentication" /etc/ssh/sshd_config && \
echo "=== UFW STATUS ===" && sudo ufw status verbose && \
echo "=== FAIL2BAN ===" && sudo fail2ban-client status 2>/dev/null || echo "fail2ban not installed" && \
echo "=== INSTALLED PACKAGES ===" && dpkg -l | grep -E "fail2ban|nginx|docker|ufw|curl|git|htop|certbot|nodejs|npm|python3|unzip|wget" && \
echo "=== DOCKER VERSION ===" && docker --version 2>/dev/null && docker compose version 2>/dev/null || echo "Docker not installed" && \
echo "=== DOCKER CONTAINERS ===" && docker ps -a --format "table {{.Names}}\t{{.Image}}\t{{.Status}}\t{{.Ports}}" 2>/dev/null || echo "Docker not running" && \
echo "=== DOCKER VOLUMES ===" && docker volume ls 2>/dev/null && \
echo "=== DOCKER NETWORKS ===" && docker network ls 2>/dev/null && \
echo "=== COMPOSE FILES ===" && find /home -name "docker-compose.yml" 2>/dev/null && \
echo "=== SYSTEMD SERVICES ===" && systemctl list-unit-files --type=service --state=enabled | grep -v "@" && \
echo "=== CRON JOBS ===" && sudo crontab -l 2>/dev/null; crontab -l 2>/dev/null
```

### Step 3: Analyze Output
When user pastes the output, analyze and summarize:
- [ ] **Specs table** — OS, CPU cores, RAM (total/used/available), Disk (total/used/free)
- [ ] **Security status** — SSH port, root login, UFW rules, Fail2ban status
- [ ] **Docker inventory** — list containers (running vs stopped), images, volumes, networks
- [ ] **Compose file map** — which services live where
- [ ] **Tools installed** — what security/utility tools are present
- [ ] **Flag any concerns** — high memory usage, swap abuse, open ports, missing security tools

### Step 4: Produce Summary Report
Output a clean formatted table/report covering all findings.
Flag any items that need attention with 🔴 (critical) / 🟡 (important) / 🟢 (healthy).

## Mandatory Rules

1. **Always test SSH access before running remote commands** — never assume key works
2. **Never modify anything** — this skill is read-only reconnaissance only
3. **Always flag OOM signals** — if swap is heavily used or available RAM < 200MB, flag it prominently

## Edge Cases

| Situation | Behavior |
|-----------|----------|
| **No Docker installed** | Skip Docker sections, note "Docker not installed" |
| **No sudo access** | Skip UFW/Fail2ban sections, note the gap |
| **Multiple servers to audit** | Run protocol once per server, produce side-by-side comparison table |
| **SSH denied from Aliq's terminal** | Ask user to run manually and paste the compound command output |

## Level History

- **Lv.1** — Base: Full server recon in one compound command + structured analysis. (Origin: 2026-04-20, forged from VPS migration planning session pattern)
