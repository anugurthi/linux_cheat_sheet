# Module 02: Filesystem Layout

Goal: Navigate the Linux hierarchy and understand purposes of key top-level directories.

## Concept Primer
Linux presents a single rooted tree (`/`). Devices, processes, sockets, and pseudo files all appear under this tree—some are real data on disk, others are interfaces provided by the kernel.

### Core Directories
| Path | Purpose | Typical Contents | Why You Care |
|------|---------|------------------|--------------|
| `/bin` | Essential user binaries | `ls`, `cp`, `mv`, shells | Needed in early boot & recovery environments |
| `/usr/bin` | Non-essential executables | Editors, utilities, app binaries | Most commands you install live here |
| `/sbin` / `/usr/sbin` | System admin binaries | `iptables`, `sysctl`, `systemd-*` | Tools for configuration & networking |
| `/etc` | System-wide configuration | `passwd`, `ssh/`, service configs | Change behavior of services & system |
| `/var` | Variable data | `log/`, `cache/`, `spool/` | Grows over time; monitor disk usage |
| `/var/log` | Log files | `syslog`, `dmesg`, service logs | First stop for troubleshooting |
| `/home` | User home directories | Dotfiles, personal data | Per-user customization & data persistence |
| `/tmp` | Temporary scratch space | Ephemeral files | Cleaned on reboot (often); don't store important data |
| `/dev` | Device nodes | `sda`, `tty`, `null` | Interface to hardware and pseudo devices |
| `/proc` | Process & kernel info (virtual) | PID dirs, status, meminfo | Inspect runtime state quickly |
| `/sys` | Kernel device model (virtual) | Bus/device metadata | Tuning/performance diagnostics |
| `/run` | Early runtime state | PID files, sockets | Transient info after boot |

### Virtual vs Physical
Virtual filesystems (`/proc`, `/sys`, parts of `/dev`) do not consume disk the same way regular files do; reading them asks the kernel for live data. Attempting to write there often alters kernel settings (danger: know what you change).

### Executable Discovery
Shell looks through directories in `PATH` left to right:
```bash
echo $PATH | tr ':' '\n'
which bash
type ls   # Distinguishes builtins, aliases, functions
```
Placing your script in a directory earlier in `PATH` overrides later commands (be careful overriding system tools).

## Why It Matters
Troubleshooting disk usage, locating configuration, and understanding why a file "disappears" (it was in `/run` or `/tmp`) relies on mental mapping of this layout. Knowing `/proc` & `/sys` reduces guesswork when gathering system metrics.

## Common Pitfalls
| Pitfall | Symptom | Fix |
|---------|---------|-----|
| Editing wrong config template | Changes ignored | Confirm actual file path under `/etc` used by service |
| Disk fills unexpectedly | `/var` huge | Use `du -sh /var/* | sort -h | tail` to locate culprits |
| Confusing virtual size | Think `/proc` uses gigabytes | Understand virtual entries; check actual disk usage with `df -h` |
| Storing long-term data in `/tmp` | Data lost after reboot | Use `$HOME` or `/var/lib/appname` for persistence |

## Mini Walkthrough
```bash
ls -ld /bin /usr/bin /etc /var/log /home /tmp
df -h | grep -E 'Filesystem|/dev/'
du -sh /var/log/* | head
ls /proc/$$    # Current shell process info
cat /proc/meminfo | head -5
```

## Practice Checklist
1. View permissions and sticky bit: `ls -ld /tmp`.
2. List 5 largest log files: `du -sh /var/log/* | sort -h | tail -5`.
3. Confirm location of `grep`: `which grep`; then verify it exists via `ls -l $(which grep)`.
4. Inspect your shell process: list `/proc/$$/status` and note the `State:` line.
5. Record top 5 directories by size under `/var`: `du -sh /var/* | sort -h | tail -5`.

## Reflection Questions
1. Why do some paths under `/proc` change each time you list them?
2. What risk is there in placing a custom script named `ls` early in `PATH`?
3. How would you locate where a service writes its logs?

Exit Criteria: You can differentiate config vs executable locations, identify large disk consumers, and explain virtual vs physical filesystem entries.

Next: Module 03 (Core Commands).