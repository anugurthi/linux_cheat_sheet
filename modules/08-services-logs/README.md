# Module 08: Services, Processes & Logs

Goal: Confidently manage systemd units, observe and interrogate running processes, and extract actionable signal from logs to drive troubleshooting.

---
## Concept Primer

| Concept | Essence | Mental Model |
|---------|---------|--------------|
| Unit | systemd configuration object (service, timer, socket, target) | Like a typed manifest declaring desired state |
| Service Types | `simple`, `forking`, `oneshot`, `notify`, `idle` | Lifecycle contract controlling when systemd considers startup done |
| Targets | Group of units (like runlevels) | Synchronization points |
| Dependencies | `Requires=`, `Wants=`, `Before=`, `After=` | A directed graph controlling ordering and activation |
| Journal | Structured, indexed log store | Central event ledger with rich query filters |
| PID / Process Tree | Parent-child relationship (PPID) | A forest of execution contexts |
| cgroups | Resource accounting/isolation | Buckets for CPU/memory tracking per unit |
| Signals | Asynchronous notifications (`SIGTERM`, `SIGKILL`) | Conversations for lifecycle control |

### Unit Anatomy (Service)
```
[Unit]
Description=Example Demo Service
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/demo --flag
Restart=on-failure
User=demo
Environment=MODE=prod
RuntimeMaxSec=300
LogsDirectory=demo

[Install]
WantedBy=multi-user.target
```

Key Directives:
| Directive | Purpose |
|-----------|---------|
| `Type=` | Startup detection mechanism |
| `ExecStart=` | Command launched |
| `Restart=` | Policy when non‑zero exit occurs |
| `User=` | Drop privileges |
| `Environment=` | Inject key/values |
| `RuntimeMaxSec=` | Kill service after duration |
| `WantedBy=` | Hook for `enable` (symlink target) |

---
## Why It Matters
Services embody your automation and application runtime; poor configuration yields cascading failures. Logs reduce guesswork by providing chronology; process/resource inspection prevents blind restarts and points toward root causes (memory leak, runaway CPU, I/O wait).

---
## Core Commands & Usage Patterns

| Task | Command | Notes |
|------|---------|-------|
| List loaded units | `systemctl list-units --type=service --state=loaded` | Scope with `--type=` |
| Status | `systemctl status nginx` | Includes recent logs tail |
| Enable + Start | `sudo systemctl enable --now nginx` | Creates symlink & starts immediately |
| View dependencies | `systemctl list-dependencies nginx` | Trace graph |
| Edit a unit (drop-in) | `sudo systemctl edit nginx` | Produces override in `/etc/systemd/system/nginx.service.d/` |
| Reload manager | `sudo systemctl daemon-reload` | After unit file changes |
| Analyze boot | `systemd-analyze blame` | Shows time per unit |
| Resource usage | `systemctl status --no-pager` + `systemd-cgtop` | cgroup-level view |

### Journal Queries
| Query Goal | Example | Explanation |
|------------|---------|-------------|
| Recent lines for unit | `journalctl -u nginx -n 30` | Last 30 entries |
| Follow live | `journalctl -u nginx -f` | Streaming tail |
| Since time | `journalctl -u nginx --since "1 hour ago"` | Relative time window |
| Between times | `journalctl --since "2024-01-01" --until "2024-01-02"` | Absolute range |
| By priority | `journalctl -p err..alert -u nginx` | Severity range filter |
| Kernel messages | `journalctl -k -b -1` | Prior boot kernel ring |
| Grep fields | `journalctl -u nginx | grep Timeout` | Quick text match (prefer structured flags first) |

Fields (view raw): `journalctl -u nginx -o verbose`.

---
## Process & Resource Inspection

| Tool | Focus | Quick Pattern |
|------|-------|---------------|
| `ps -ef` | Full process list | Filter with `grep '[n]ginx'` |
| `ps -o pid,ppid,cmd,%cpu,%mem -p <PID>` | Targeted metrics | CPU/mem snapshot |
| `top` / `htop` | Dynamic overview | Sort by shift+P (CPU) or shift+M (mem) |
| `systemd-cgtop` | cgroup resource | Per-unit consumption |
| `lsof -p <PID>` | Open files | Diagnose FD leaks |
| `df -h` | Filesystem usage | Human units |
| `free -h` | Memory usage | Buffers/cache clarity |
| `iostat -xz 1` | I/O wait | Extended stats per device |

---
## Troubleshooting Workflow (Example: Service Fails to Start)
1. `systemctl status demo` → capture exit code & last log lines.
2. Check journal context: `journalctl -u demo --since "5m ago"`.
3. Inspect unit file & overrides: `systemctl cat demo` & `systemctl show demo -p Environment`.
4. Verify binary path & permissions: `command -v /usr/local/bin/demo` and `ls -l`.
5. Test command manually as service user: `sudo -u demo /usr/local/bin/demo --flag`.
6. Resource constraints? Check memory: `free -h` & cgroup limits: `systemctl status demo`.
7. Increase verbosity (if supported): add env `LOG_LEVEL=debug` via override (`systemctl edit`).
8. Apply changes: `sudo systemctl daemon-reload`; restart: `systemctl restart demo`; re-check status.
9. Persistent failure? Capture a strace snippet: `sudo strace -o /tmp/trace -f -p <PID>`.

Decision Point: If manual run fails identically → application bug; else mismatch between service environment and interactive shell.

---
## Common Pitfalls & Fixes
| Pitfall | Symptom | Fix |
|---------|---------|-----|
| Forget `daemon-reload` | Changes ignored | Run reload after file edits |
| Using `restart=always` for crashing binary | Rapid crash loop | Start with `on-failure`, add backoff (`RestartSec=`) |
| Cron used for long-running daemon | Orphaned processes, no supervision | Migrate to systemd service |
| Grepping logs textually only | Miss severity context | Use `-p` and unit filters first |
| Ignoring cgroups | Hidden resource starvation | Inspect with `systemd-cgtop` |
| Running as root unnecessarily | Elevated risk | Set `User=` directive |

---
## Guided Walkthrough: Creating & Managing a Custom Service

Scenario: Wrap the backup script from Module 07 as a managed service & timer.

1. Place script under `/usr/local/bin/backup.sh` (or user `~/bin/backup.sh`). Ensure executable.
2. Create service unit (system scope): `/etc/systemd/system/backup.service`
```
[Unit]
Description=Rotating Backup Service
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/local/bin/backup.sh
User=backup
Group=backup
Nice=10
LogsDirectory=backup

[Install]
WantedBy=multi-user.target
```
3. Create timer: `/etc/systemd/system/backup.timer`
```
[Unit]
Description=Periodic Backups

[Timer]
OnCalendar=hourly
RandomizedDelaySec=600
Unit=backup.service

[Install]
WantedBy=timers.target
```
4. Reload & enable: `systemctl daemon-reload && systemctl enable --now backup.timer`.
5. Inspect schedule: `systemctl list-timers --all`.
6. View logs: `journalctl -u backup.service -n 50`.
7. Force run: `systemctl start backup.service` (check new archive created).
8. Adjust retention by editing script & re-running.

---
## Practice Checklist
Foundational:
1. List failed units: `systemctl --failed` and describe one reason.
2. Query last boot performance: `systemd-analyze blame`.

Intermediate:
3. Create a oneshot service that writes a timestamp to `/var/log/custom/tick.log` via `LogsDirectory=`.
4. Add a timer to run it every 15 minutes.
5. Filter journal for priority warning and higher for that unit.

Advanced:
6. Add restart policy to a simple loop service and observe backoff.
7. Simulate failure & capture strace for diagnostic comparison.
8. Create override adding environment variable and confirm with `systemctl show -p Environment`.

Stretch:
9. Build a dashboard command summarizing CPU top 3 units + memory usage.
10. Write a script that extracts last 10 error lines for each failed service.

---
## Reflection Questions
1. What signal difference do you observe between manual binary run and systemd execution?
2. How does logging change when using `LogsDirectory=` vs redirect inside script?
3. Which dependency directive would you use to ensure your service waits for network readiness? Why?
4. When is a timer superior to cron for your use case?
5. How could you add observability without changing application code (systemd features)?

---
## Exit Criteria
You can:
| Capability | Demonstrated By |
|------------|-----------------|
| Inspect & interpret unit | `systemctl status` with log analysis |
| Modify behavior safely | Use drop-in override + reload |
| Query structured logs | Filter by unit, time range, priority |
| Diagnose startup failures | Apply workflow steps to a failing service |
| Manage scheduling | Timer creation & verification |
| Resource awareness | Use cgroup/process tools to identify pressure |

---
Next: Module 09 (DevOps Integration) → Compose scripts, services, containers & config management into a cohesive delivery pipeline.