# Linux & DevOps Interview Questions (Beginner to Intermediate)

Each question includes a concise answer for quick review. Practice expanding verbally.

## Basics
| Q | A |
|---|---|
| What is Linux? | Open-source UNIX-like OS composed of kernel + userland utilities forming distributions. |
| Difference between Linux kernel and a distribution? | Kernel manages hardware; distro bundles kernel + packages + package manager + configs. |
| Why choose Linux for servers? | Stability, security model, performance, strong tooling & automation ecosystem. |
| What is a shell? | Command interpreter translating user input into system calls/program executions. |
| What does `uname -a` show? | Kernel name, version, architecture, and build info. |

## Filesystem & Navigation
| Q | A |
|---|---|
| Purpose of `/etc`? | System-wide configuration files. |
| Difference `/bin` vs `/usr/bin`? | `/bin` essential for boot/recovery; `/usr/bin` majority of applications. |
| What is an inode? | Metadata structure storing file attributes and pointers to data blocks. |
| How to find a file named `config.yml` from current directory? | `find . -name config.yml` |
| Command to show hidden files? | `ls -a` |

## Permissions & Ownership
| Q | A |
|---|---|
| Decode `rwxr-xr--` | User: rwx, Group: r-x, Others: r--. |
| Numeric for `rwxr-xr--`? | 754. |
| Change owner to user `app` and group `deploy`? | `chown app:deploy file` |
| Purpose of sticky bit on `/tmp`? | Prevent users from deleting others' files in shared directory. |
| What does setuid do? | Executes binary with file owner's UID (e.g. root for `passwd`). |

## Processes & Monitoring
| Q | A |
|---|---|
| Show all processes? | `ps aux` |
| Interactive CPU/mem view? | `top` or `htop` |
| Kill process gracefully? | `kill PID` (SIGTERM) then escalate with `kill -9 PID` if needed. |
| Check service status (systemd)? | `systemctl status servicename` |
| View recent systemd logs? | `journalctl -xe` |

## Networking
| Q | A |
|---|---|
| Show IP addresses? | `ip a` |
| Test connectivity to host? | `ping host` |
| Fetch URL headers only? | `curl -I https://example.com` |
| Show listening TCP ports? | `ss -tulpn` |
| Secure copy file to remote host? | `scp file user@host:/path` |

## Package Management
| Q | A |
|---|---|
| Update package index (Debian)? | `sudo apt update` |
| Install package on Fedora? | `sudo dnf install pkg` |
| Difference between `rpm -ivh` and `dnf install`? | `rpm -ivh` installs locally without resolving dependencies automatically; `dnf` resolves dependencies from repos. |
| List files from installed RPM? | `rpm -ql packagename` |
| Remove an APT package but keep config files? | `sudo apt remove pkg` |

## Text Processing
| Q | A |
|---|---|
| Find lines containing ERROR in log? | `grep ERROR logfile` |
| Count unique IPs in access log first column? | `cut -d' ' -f1 access.log | sort | uniq -c` |
| Replace all `foo` with `bar` in place? | `sed -i 's/foo/bar/g' file` |
| Extract 3rd column separated by `:`? | `cut -d':' -f3 file` |
| Print lines 10–20 of file? | `sed -n '10,20p' file` |

## Scripting & Automation
| Q | A |
|---|---|
| Purpose of shebang? | Specifies interpreter for script execution. |
| Make script exit on first error? | Add `set -e` (or `set -euo pipefail` for stricter). |
| Difference between `$*` and `$@`? | `$@` preserves arguments with spaces; both expand to positional params. |
| Pass variable to script inline? | `MODE=prod ./deploy.sh` |
| Schedule a script hourly? | Cron entry: `0 * * * * /path/script.sh` |

## DevOps Context
| Q | A |
|---|---|
| Why logs matter? | Enable troubleshooting, audit trails, performance insight. |
| Common log locations? | `/var/log/` (e.g. `syslog`, `messages`, app-specific logs). |
| Role of systemd in DevOps? | Manages service lifecycle, restarts on failure, logging integration. |
| Why use configuration management tools? | Consistency, scalability, repeatability across environments. |
| Containers rely on which Linux features? | Namespaces, cgroups, union filesystems. |

## Security Basics
| Q | A |
|---|---|
| Principle of least privilege? | Give only necessary rights to reduce attack surface. |
| Check world-writable files? | `find /path -perm -0002 -type f` |
| Secure SSH best practices? | Key-based auth, disable root login, restrict users/groups. |
| Verify integrity of package? | `rpm -V packagename` or signature `rpm -K file.rpm`. |
| What is SELinux/AppArmor? | Mandatory access control frameworks enhancing security. |

## Troubleshooting Scenarios
| Scenario | Approach |
|----------|----------|
| High CPU usage | `top`, identify PID, inspect process, logs, maybe `strace` |
| Service won't start | `systemctl status`, check logs (`journalctl -u svc`), config syntax |
| Disk full | `df -h`, `du -sh *`, rotate logs, archive/remove old data |
| Network unreachable | Test DNS (`dig`/`host`), route (`ip route`), firewall rules, `ping`, `curl` |
| Permission denied error | `ls -l`, ownership check, groups, SELinux/AppArmor status |

## Behavioral / Conceptual
| Q | A (Guideline) |
|---|----------------|
| Describe a time you automated a manual task. | Outline problem -> script -> impact (time saved, fewer errors). |
| How do you learn new tooling fast? | Official docs, man pages, small test lab, incremental experiments. |
| Difference between proactive and reactive monitoring? | Proactive predicts issues (threshold alerts); reactive responds after failure. |

## Advanced (Stretch) – Optional Early Review
| Q | A |
|---|---|
| What are cgroups? | Kernel feature controlling resource usage for process groups. |
| What is a namespace? | Isolation mechanism for resources (PID, mount, network) used in containers. |
| How does `systemd` restart a crashed service automatically? | `Restart=` directives in unit file (e.g. `Restart=on-failure`). |

## Practice Tips
- Write each answer in your own words.
- Explain commands by their components (flags, pipeline stages).
- Pair a concept with a real incident you could resolve using it.

---
Next: Pick 10 questions randomly and answer aloud, then refine with deeper examples.
