# Linux Learning Roadmap (Beginner to DevOps)

> Goal: Take someone with zero Linux knowledge to a confident DevOps-ready practitioner who can navigate servers, automate tasks, and troubleshoot.

## Phase Overview
| Phase | Focus | Outcome | Estimated Time |
|-------|-------|---------|----------------|
| 0. Orientation | What Linux is, distributions, installing | Understand ecosystem & pick a distro | 0.5 day |
| 1. Navigation & Files | Shell basics, filesystem layout, moving around | Comfort moving & inspecting files | 2–3 days |
| 2. Permissions & Users | Ownership, chmod, sudo, process IDs | Secure manipulation & user context awareness | 2 days |
| 3. Processes & Monitoring | ps, top, kill, logs, systemctl basics | Can observe & manage running services | 2–3 days |
| 4. Networking & Connectivity | ip, ping, curl, ssh, scp, ports | Can connect securely & diagnose network issues | 3 days |
| 5. Packages & Software | apt/dnf/pacman/zypper, rpm/dpkg | Installing, querying, verifying packages | 2 days |
| 6. Text Processing | grep, awk, sed, sort, uniq, cut, xargs | Efficient log/data parsing & one-liners | 4–5 days (ongoing) |
| 7. Scripting & Automation | bash scripting, shebang, variables, flow | Build reliable automation scripts | 1 week |
| 8. DevOps Integration | systemd, cron, environment, config mgmt intro | Ready to integrate with CI/CD & infra tools | 1 week |
| 9. Advanced & Hardening | Security basics, backups, performance tools | Proactive management & troubleshooting | Ongoing |

## Key Concepts (High-Level)
| Concept | Why It Matters in DevOps |
|---------|--------------------------|
| Kernel & userland | Understand where syscalls meet tools (optimization & troubleshooting) |
| Filesystem hierarchy | Know where configs/logs/binaries live to script & audit |
| Permissions & ownership | Prevent outages/security breaches in automation |
| Processes & signals | Manage application lifecycle gracefully |
| Resource monitoring | Detect bottlenecks early (CPU, memory, I/O) |
| Networking stack | Service discovery, connectivity checks, container networking |
| Package management | Reproducible environments & patching |
| Shell scripting | Glue tasks: deploy, migrate, rotate logs |
| Text processing | Rapid extraction of insights from logs & outputs |
| Automation scheduling | Cron/systemd timers for repeatable tasks |
| Security fundamentals | Safeguard credentials & escalate privileges responsibly |

## DevOps Importance
| Domain | Linux Skill Applied |
|--------|---------------------|
| CI/CD Pipelines | Build scripts, artifact packaging, log parsing |
| Infrastructure as Code | Understanding underlying OS state when writing Terraform/Ansible |
| Containers & Orchestration | Linux namespaces, cgroups basics, networking |
| Monitoring & Alerting | Gathering metrics via shell tools, log triage |
| Incident Response | Fast grep/sed/awk triage, permission fixes |
| Security & Compliance | Patch management, file integrity, least privilege |

## Detailed Phase Breakdown
### Phase 0: Orientation
Tasks:
- Read about Linux vs Windows/Mac.
- Compare 3 distros (Ubuntu, Fedora, Arch) and choose one for practice.
- Install in VM (VirtualBox / WSL / cloud free tier).
Checkpoint: You can login, open a terminal, run `uname -a`.

### Phase 1: Navigation & Files
Commands: `pwd`, `ls -l`, `cd`, `tree`, `cat`, `less`, `head`, `tail`, `touch`, `mkdir`, `rm`.
Exercises:
- List all files in `/etc` sorted by size.
- Create a `lab` directory with nested folders and remove one safely.
Checkpoint: You can locate logs in `/var/log` and explain `/etc` vs `/usr`.

### Phase 2: Permissions & Users
Commands: `ls -l`, `chmod`, `chown`, `chgrp`, `umask`, `sudo`, `id`.
Exercises:
- Create a shared group directory with setgid inheritance.
- Explain difference between `775` and `750` on a project folder.
Checkpoint: Describe rwx for directories vs files and show safe script perm.

### Phase 3: Processes & Monitoring
Commands: `ps`, `top`, `htop`, `kill`, `pkill`, `systemctl status`, `journalctl`, `df -h`, `free -h`.
Exercises:
- Start a long-running process; inspect it with `ps`.
- List services that failed to start.
Checkpoint: Kill a runaway process gracefully.

### Phase 4: Networking & Connectivity
Commands: `ip a`, `ip route`, `ping`, `curl`, `ssh`, `scp`, `ss -tulpn`.
Exercises:
- SSH into a VM and copy a file back.
- Check which process is listening on a custom port.
Checkpoint: Diagnose DNS failure vs port firewall issue.

### Phase 5: Packages & Software
Commands: `apt`, `dnf`, `rpm -qi`, `dpkg -l`, `snap`, `flatpak` (awareness).
Exercises:
- Install a tool, query its version and files, remove and reinstall.
Checkpoint: Explain difference between `apt install` and `dpkg -i`.

### Phase 6: Text Processing
Commands: `grep`, `grep -R`, `awk`, `sed`, `cut`, `sort`, `uniq`, `wc`, `xargs`.
Exercises:
- Extract top 10 IPs from access log.
- Replace a config value across many files.
Checkpoint: Compose a pipeline that counts error lines per hour.

### Phase 7: Scripting & Automation
Topics: shebang, variables, quoting, arrays, functions, exit codes, `set -euo pipefail`, cron.
Exercises:
- Script to backup a directory with timestamp.
- Cron job that rotates a log monthly.
Checkpoint: Script returns non-zero on failure and logs actions.

### Phase 8: DevOps Integration
Topics: `systemctl` service units, environment variables, `.env` sourcing, simple Ansible playbook introduction, container basics (Docker CLI).
Exercises:
- Create a systemd service for a custom script.
- Containerize a simple script (Dockerfile) and run it.
Checkpoint: Explain environment flow from systemd unit -> process.

### Phase 9: Advanced & Hardening (Ongoing)
Topics: SELinux/AppArmor basics, file integrity (`rpm -V`), backups (`tar`, `rsync`), performance tools (`iotop`, `vmstat`, `sar`), security scanning.
Exercises:
- Verify integrity of a core package.
- Create encrypted archive of sensitive files.
Checkpoint: Provide a short incident response plan for high CPU alert.

## Practice Cadence Suggestion
| Day | Focus | Output |
|-----|-------|--------|
| 1 | Orientation | VM installed, terminal working |
| 2–3 | Navigation & Files | Notes file listing commands |
| 4–5 | Permissions | Shared dir + permission cheat sheet |
| 6–7 | Processes/Monitoring | Resource snapshot script |
| 8–10 | Networking | SSH key-based access configured |
| 11–12 | Packages | Installed & removed sample packages |
| 13–17 | Text Processing | Log parsing toolkit script |
| 18–24 | Scripting | Backup & deploy scripts + cron jobs |
| 25+ | DevOps Integration | systemd unit + Dockerfile |

## Milestone Checks
| Milestone | Demonstration |
|-----------|---------------|
| Beginner | Navigate FS, manage files safely |
| Intermediate | Scripts tasks, manage permissions, monitor processes |
| DevOps-ready | Automate deployments, secure environments, troubleshoot quickly |

## Interview Preparation Strategy
1. Finish phases 1–7.
2. Write a README summary of what you built (scripts + commands).
3. Answer interview questions (see `InterviewQuestions.md`).
4. Perform a mock scenario: "Disk usage rising" or "Service offline".

## Learning Resources (External)
| Topic | Resource |
|-------|----------|
| General | The Linux Documentation Project |
| Arch Concepts | Arch Wiki (even if not using Arch) |
| Bash | GNU Bash manual + ShellCheck tool |
| Systemd | systemd docs + `man systemd.unit` |
| Networking | `man ip`, Julia Evans networking zines |
| Text Processing | `awk` and `sed` guides (GNU) |

## Common Pitfalls & Prevention
| Pitfall | Prevention |
|---------|-----------|
| `rm -rf` wrong path | Echo variable first; use `-i` flag |
| Scripts fail silently | `set -euo pipefail` + logging |
| Permissions too open | Use least privilege, review with `find . -perm -o+w` |
| Forgotten cron jobs | Document in version-controlled ops repo |
| Unpatched system | Schedule periodic `apt upgrade` / `dnf upgrade` |

## Self-Assessment Checklist
Tick off before claiming DevOps readiness:
- [ ] Explain difference between process, thread, service.
- [ ] Parse logs to isolate an error cause with one command.
- [ ] Automate a backup + rotation.
- [ ] Secure an SSH server (no password auth, limited users).
- [ ] Roll back a misconfiguration quickly.
- [ ] Monitor disk/memory and act before thresholds.

## Next Steps After This Roadmap
Move into: Containers (Docker, Podman), Orchestration (Kubernetes), Configuration Management (Ansible), Infrastructure as Code (Terraform), Observability (Prometheus, ELK), Security scanning (Trivy, Lynis).

---
Start now: create `~/lab`, run `uname -a`, list `/etc`, and write down 5 directories you don't recognize.
