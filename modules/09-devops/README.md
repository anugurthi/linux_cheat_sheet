# Module 09: DevOps Essentials Integration

Goal: Compose scripting, services, containers, configuration management, and observability into a repeatable, idempotent delivery pipeline.

---
## Concept Primer

| Layer | Artifact | Purpose | Stability Lever |
|-------|----------|---------|-----------------|
| Script | `backup.sh` | Encapsulate task logic | Strict mode + logging |
| Service/Timer | `backup.service` / `backup.timer` | Reliable scheduling & supervision | systemd restart & dependency model |
| Container | `Dockerfile` | Reproducible runtime environment | Pin versions (tags, digests) |
| Config Mgmt | Ansible playbook | Declarative idempotent state | Handlers & conditional checks |
| Observability | Logs + metrics + checks | Feedback loop | Threshold alerts & structured formats |

Integration Outcome: Pipeline transitions from ad-hoc commands to a chain with defined inputs, outputs, and health signals.

---
## Why It Matters
Organizations scale on repeatability. Scripts alone do not provide environment consistency; services alone lack portability; containers isolate but omit host config; config management ensures base invariants; observability tells you if reality drifts. The synergy prevents configuration drift, fragile deployments, and silent failure.

---
## Architectural Flow
1. Author task logic (script) with clear parameter semantics.
2. Wrap script in systemd unit (host execution path) OR container entrypoint.
3. Build container for portable/cloud execution; run locally for parity.
4. Use Ansible to enforce prerequisites (packages/users/dirs) across hosts.
5. Expose logs & basic metrics; schedule recurring health checks.

Data Flow: Source directories → Script archives → Service schedules → Containerizes for portability → Playbook ensures dependencies → Logs feed monitoring.

---
## Implementation Components

### Script (Enhanced Backup)
Key improvements beyond Module 07: argument parsing (`getopts`), JSON log option, checksum.

### Systemd Service & Timer
Deploy on host for native scheduling. Provide exit code signaling to orchestrators.

### Containerization
| Concern | Host Script | Containerized |
|---------|-------------|---------------|
| Dependencies | Implicit on host | Explicit in image |
| Portability | Limited | High (across hosts) |
| Security | Host libs shared | Isolation namespace |
| Versioning | Manual tracking | Image tag/digest |

### Ansible Idempotence
| Concept | Pattern |
|---------|---------|
| Idempotent Install | `package:` module ensures present |
| Conditional Action | `when:` prevents unnecessary steps |
| Handlers | Trigger restart only upon change |
| Inventory | Group hosts logically |
| Variables | Parameterize differences |

### Observability Checklist
| Domain | Check | Tool |
|--------|-------|------|
| Logs | Recent errors for unit | `journalctl -u backup.service -p err -n 20` |
| Storage | Free space threshold | `df -h /` |
| Memory | Usage vs baseline | `free -h` |
| Runtime Status | Service active & last run success | `systemctl status backup.service` |
| Container Health | Exit code / restart count | `docker ps --format ...` |
| Configuration Drift | Playbook reports `changed=0` | Ansible summary |

---
## Guided Walkthrough

### 1. Script (backup.sh)
```bash
#!/usr/bin/env bash
set -euo pipefail
log(){ printf '%s %s %s\n' "$(date -Iseconds)" "$1" "$2"; }
SRC=${1:-/var/log}
DEST=${2:-/opt/backups}
mkdir -p "$DEST"
ts=$(date +%Y%m%d-%H%M%S)
tmp=$(mktemp)
tar -C "$SRC" -cf - . | gzip -9 > "$tmp"
sha256sum "$tmp" | awk '{print $1}' > "$tmp.sha"
mv "$tmp" "$DEST/backup-${ts}.tar.gz"
mv "$tmp.sha" "$DEST/backup-${ts}.tar.gz.sha256"
log INFO "Archive created $DEST/backup-${ts}.tar.gz"
```

### 2. Service & Timer
`/etc/systemd/system/backup.service`
```
[Unit]
Description=Backup Logs
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/local/bin/backup.sh /var/log /opt/backups
User=backup
Group=backup
LogsDirectory=backup

[Install]
WantedBy=multi-user.target
```
`/etc/systemd/system/backup.timer`
```
[Unit]
Description=Periodic Log Backup

[Timer]
OnCalendar=hourly
RandomizedDelaySec=300
Unit=backup.service

[Install]
WantedBy=timers.target
```

### 3. Containerization
`Dockerfile`
```
FROM alpine:3.19
RUN apk add --no-cache bash tar gzip coreutils
COPY backup.sh /usr/local/bin/backup.sh
RUN chmod +x /usr/local/bin/backup.sh
ENTRYPOINT ["/usr/local/bin/backup.sh", "/var/log", "/data"]
```
Build & run (conceptual): build image; run with bind mount to host logs & data volume.
Mount Example: `docker run --rm -v /var/log:/var/log:ro -v /opt/backups:/data backup:latest`

### 4. Ansible Playbook
`playbook.yml`
```yaml
---
- hosts: backup_hosts
	become: true
	vars:
		backup_src: /var/log
		backup_dest: /opt/backups
	tasks:
		- name: Ensure backup user exists
			user:
				name: backup
				system: yes
		- name: Install required packages
			package:
				name: [ "tar", "gzip", "bash" ]
				state: present
		- name: Deploy script
			copy:
				src: backup.sh
				dest: /usr/local/bin/backup.sh
				mode: '0755'
		- name: Deploy service unit
			copy:
				src: backup.service
				dest: /etc/systemd/system/backup.service
		- name: Deploy timer unit
			copy:
				src: backup.timer
				dest: /etc/systemd/system/backup.timer
		- name: Reload systemd
			command: systemctl daemon-reload
			notify: restart timer
		- name: Enable timer
			systemd:
				name: backup.timer
				enabled: true
				state: started
	handlers:
		- name: restart timer
			systemd:
				name: backup.timer
				state: restarted
```
Idempotence Test: Re-run – expect minimal `changed` count.

### 5. Observability Sweep Script
`health.sh` collects quick status report.
```bash
#!/usr/bin/env bash
set -euo pipefail
echo "==== Service Status ===="
systemctl is-active backup.service || echo "backup.service inactive"
echo "==== Recent Errors (backup) ===="
journalctl -u backup.service -p err -n 5 || true
echo "==== Disk ===="
df -h / | tail -n +2
echo "==== Memory ===="
free -h | awk 'NR==2{print $3" used / "$2}'
```

---
## Common Pitfalls & Fixes
| Pitfall | Symptom | Fix |
|---------|---------|-----|
| Script assumes host packages | Container fails | Install dependencies in Dockerfile |
| Non‑idempotent playbook | Re-runs always show changes | Use declarative modules; remove shell steps where possible |
| Missing daemon-reload | Updated units ignored | Explicit task after unit copy |
| Timer not enabled | No executions occur | Ensure `enabled: true` and state started |
| Unpinned base image | Version drift | Pin tag (e.g., `alpine:3.19`) or digest |
| No checksum/verification | Silent corrupted backups | Generate & validate hash file |

---
## Practice Checklist
Foundational:
1. Build the container and run one backup.
2. Run playbook twice; capture change summary.

Intermediate:
3. Add checksum verification in service script (fail if mismatch).
4. Add retention logic inside container script variant.
5. Extend playbook to deploy `health.sh` and a cron/systemd timer for it.

Advanced:
6. Add tag/label metadata to container (e.g., `org.opencontainers.image.source`).
7. Implement notification handler triggered only when script file changes.
8. Add an Ansible task to assert free space > 10%. Fail gracefully otherwise.

Stretch:
9. Generate a JSON report combining disk usage & last backup time.
10. Integrate a lightweight metrics endpoint (e.g., push timestamp to a file consumed by Prometheus node exporter `textfile` collector).

---
## Reflection Questions
1. Which integration layer provided the largest stability improvement? Why?
2. How does idempotence reduce operational risk?
3. What would you version-control vs generate on the fly in this pipeline?
4. Which failure mode is still insufficiently monitored? Plan a signal.
5. How would you adapt this pipeline for multi-host scaling?

---
## Exit Criteria
You can:
| Capability | Demonstrated By |
|------------|-----------------|
| Script reliability | Strict backup script with checksum |
| Service scheduling | Working service+timer combination |
| Container portability | Image built & run with mounted volumes |
| Config mgmt idempotence | Playbook second run minimal changes |
| Observability sweep | Health script surfaces key metrics/errors |
| Drift prevention | Version-pinned image & declarative packages |

---
Next: Module 10 (Interview Readiness) → Translate practical skill into confident explanation & scenario reasoning.