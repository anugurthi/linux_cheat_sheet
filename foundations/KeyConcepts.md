# Key Linux Concepts (Beginner Reference)

Use this as a quick conceptual map while following the roadmap.

## 1. What Is Linux?
Linux = Kernel + Userland (utilities, libraries) packaged as distributions (Ubuntu, Fedora, Arch). Kernel handles hardware access, process scheduling, memory, devices; userland provides commands.

## 2. Filesystem Hierarchy
Single rooted tree at `/`. Everything (devices, processes, sockets) represented as files.
| Path | Purpose |
|------|---------|
| `/etc` | System-wide configuration |
| `/var/log` | Logs (system & services) |
| `/home/user` | User data & settings |
| `/usr/bin` | Most installed user executables |
| `/bin` | Essential binaries for early boot |
| `/proc` | Virtual runtime process & kernel info |
| `/sys` | Kernel device & driver interface |
| `/dev` | Device nodes (disks, terminals) |

## 3. Shell & Command Line
Shell = command interpreter (bash, zsh). Executes programs, manages environment variables, expands wildcards, supports piping & redirection.
| Feature | Example |
|---------|---------|
| Pipe | `ls | grep txt` |
| Redirect | `echo hi > file.txt` |
| Append | `echo more >> file.txt` |
| Background | `longtask &` |
| Command substitution | ``DATE=$(date +%F)`` |

## 4. Processes & Signals
Process: running program instance with PID. Parent-child relationships form a tree. Signals control behavior (`SIGTERM`, `SIGKILL`, `SIGHUP`).
| Task | Command |
|------|---------|
| List | `ps aux` |
| Interactive | `top` |
| Kill (graceful) | `kill PID` |
| Force | `kill -9 PID` |
| Tree | `pstree -p` (install `pstree`) |

## 5. Users, Groups, Permissions
Three scopes: user, group, others. Bits: read (r), write (w), execute (x). Directories need execute to traverse. Ownership secures multi-user systems.
| Example | Meaning |
|---------|--------|
| `rwxr-x---` | Owner full; group read/execute; others none |
| `chmod 755 script.sh` | Set rwxr-xr-x |
| `chown app:deploy file` | Change owner & group |
| `umask 027` | New files become 640, dirs 750 |

Special bits: setuid, setgid (group inheritance on dirs), sticky (protect deletions in shared dirs like `/tmp`).

## 6. Packages & Repositories
Software packaged with metadata & dependencies. Package manager retrieves verified copies from repositories.
| Family | High-Level PM | Low-Level Tool |
|--------|---------------|---------------|
| Debian | `apt` | `dpkg` |
| Red Hat | `dnf`/`yum` | `rpm` |
| Arch | `pacman` | (same) |
| SUSE | `zypper` | `rpm` |

Low-level tools inspect installed files, verify integrity. High-level tools resolve dependencies, updates.

## 7. Networking Basics
IP addresses, routes, DNS resolution. SSH for remote login, ports define service endpoints.
| Task | Command |
|------|---------|
| Show interfaces | `ip a` |
| Routing table | `ip route` |
| Connectivity | `ping host` |
| Fetch content | `curl -L https://example.com` |
| Open ports | `ss -tulpn` |

## 8. Text Processing & Pipelines
Process logs & data quickly using stream tools.
| Tool | Purpose | Example |
|------|---------|---------|
| `grep` | Filter lines | `grep ERROR app.log` |
| `sed` | Edit/transform | `sed -i 's/debug/info/g' config` |
| `awk` | Column operations | `awk '{print $1}' access.log` |
| `cut` | Slice fields | `cut -d':' -f2 /etc/passwd` |
| `sort` | Order stream | `sort -nr` |
| `uniq` | Collapse duplicates | `uniq -c` |
| `xargs` | Build commands | `grep -l pattern . | xargs rm` |

Combine to produce insights with minimal tooling.

## 9. Scripting & Automation
Bash scripts orchestrate commands; include shebang, set safety flags, use functions. Cron/systemd timers schedule recurring automation.
| Safety Flags | Effect |
|--------------|-------|
| `set -e` | Exit on error |
| `set -u` | Error on unset variable |
| `set -o pipefail` | Pipeline fails if any stage fails |

## 10. Logging & Observability
Logs stored under `/var/log`. Systemd journal aggregates logs accessible via `journalctl`. Analyze performance with `top`, `vmstat`, `iostat`, `iotop`, `sar`.

## 11. Security Fundamentals
Least privilege (limit user rights), patch regularly, verify installed packages (`rpm -V`, `debsums`), use secure SSH (key-based). Monitor world-writable files and suspicious processes.

## 12. DevOps Integration
These concepts underpin CI/CD scripting (file ops + permissions), infrastructure automation (knowing placement of configs), containerization (process isolation, cgroups), and monitoring (parsing logs with text tools).

## Concept Relationships (Mental Map)
```
[Filesystem]--stores-->[Configs]--loaded by-->[Processes]
	|                                 |
	v                                 v
[Permissions] secure           [Logs] reveal behavior
	|                                 |
	+--> influences scripts (Scripting) -> Automation (Cron/systemd)
	|                                 |
[Packages] install tools -> used by -> [Monitoring/Text Processing]
```

## Quick Self-Check
- Can you explain difference between `/etc` and `/usr/share`?
- Can you change ownership of a directory for shared group collaboration?
- Can you tail a log and extract top error codes?
- Can you write a script that backs up `/var/log` daily with timestamps?
- Can you list open ports and identify the owning process?

## Next Deep Dives
Containers (namespaces & cgroups), systemd unit design, network performance, SELinux profiles, configuration management (Ansible), IaC (Terraform).

---
Use this page to fill conceptual gaps—if a term is unclear, jump to the related guide and run real commands.