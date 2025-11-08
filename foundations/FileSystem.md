# Linux File System Layout

Everything on Linux starts at the single root directory `/`. Instead of drive letters, every file, directory, device, and running process hooks somewhere under this unified tree. Understanding the purpose of key top‑level directories helps you find configs, logs, binaries, and temporary data quickly.

## High-Level Directory Map
| Path | Purpose | Notes |
|------|---------|-------|
| `/` | Root of the entire hierarchy | Contains everything |
| `/bin` | Essential user binaries | Needed for early boot (ls, cp, mv) |
| `/sbin` | Essential system binaries | Admin tools (fdisk, ip, systemctl) |
| `/usr/bin` | Majority of user executables | Non-essential (apps, tools) |
| `/usr/sbin` | Non-essential system binaries | Advanced admin tools |
| `/usr/lib` | Libraries for programs in `/usr` | Multi-arch variants (lib64) |
| `/lib` | Essential shared libraries | Required to start system binaries |
| `/etc` | System-wide configuration | Text-based config files |
| `/var` | Variable data (logs, caches, spool) | Changes frequently |
| `/var/log` | Log files | Rotate via logrotate or journalctl |
| `/home` | User home directories | Personal files, settings |
| `/root` | Root user’s home | Restricted; admin only |
| `/tmp` | Temporary files (cleared) | World-writable; may wipe on reboot |
| `/dev` | Device nodes (block/char) | Virtual files representing hardware |
| `/proc` | Kernel & process info (virtual) | PID subdirs, cpu/mem stats |
| `/sys` | Kernel device model interface | Tuning & hardware details |
| `/run` | Early runtime state (volatile) | PID files, sockets after boot |
| `/opt` | Optional add-on software | Vendor apps, large packages |
| `/mnt` | Temporary mount point | Manual mounts (USB, NFS) |
| `/media` | Removable media auto-mount | Desktop environments use this |
| `/srv` | Data for served services | Web/FTP server content (if used) |

## Virtual vs Physical
Directories like `/proc`, `/sys`, and `/dev` are managed by the kernel and dynamically generated—files inside are interfaces, not stored data.

## Navigating and Inspecting
```bash
cd /etc
ls -lh
file /etc/passwd         # Reveals text format
stat /etc/passwd         # Metadata (ctime, mtime)
du -sh /var/log/* | head # Size of log files
```

## Locating Executables
`which`, `type`, and `whereis` help identify executable locations:
```bash
which bash
type ls
whereis sshd
```
Executables are found via the `PATH` environment variable. View with `echo $PATH`.

## Configuration File Conventions
- Plain text, often key=value or stanza based (e.g., `sshd_config`).
- System-wide defaults in `/etc`; user-specific overrides under `$HOME` (dotfiles, e.g., `~/.bashrc`).
- Many services read a directory of snippets (e.g., `/etc/sysctl.d/`, `/etc/systemd/system/`).

## Logs
System logs in `/var/log`. Examples:
| File | Purpose |
|------|---------|
| `/var/log/messages` | General system (distro dependent) |
| `/var/log/syslog` | System + services (Debian/Ubuntu) |
| `/var/log/secure` | Auth & security (RHEL/Fedora) |
| `/var/log/dnf.log` | Package operations (Fedora) |
| `/var/log/auth.log` | Authentication (Debian/Ubuntu) |

View rotating logs:
```bash
tail -f /var/log/syslog
journalctl -xe            # Enhanced systemd log viewer
```

## Process & Kernel Info (procfs)
Inspect a running process:
```bash
ls /proc/$$              # Current shell process directory
cat /proc/uptime         # System uptime
cat /proc/cpuinfo | grep 'model name' | head
cat /proc/meminfo | head
```
Each PID folder contains environment, command line, open file descriptors (fd), and more.

## Devices (dev)
Block devices (disks) appear as `/dev/sda`, `/dev/nvme0n1`; partitions as `/dev/sda1`. Character devices include terminals (`/dev/tty`), random number generator (`/dev/urandom`). Use `lsblk` to map block devices and mount points.

## Mount Points
Mounted filesystems integrate into the tree. Check current mounts:
```bash
mount | head
findmnt -t ext4
df -h
```
Temporary mounts can go under `/mnt` or created directories inside `/media`.

## Permissions Patterns
- `/root` restricts access (only root can read).
- `/tmp` is world-writable with sticky bit so users cannot delete others' files.
Check:
```bash
ls -ld /tmp
```
Expect: `drwxrwxrwt` (ending in `t`).

## Space Management
Large logs or caches in `/var` can consume space. Identify big directories:
```bash
sudo du -sh /var/* | sort -h | tail
```
Prune logs using rotation policies (`logrotate`) or clearing caches (`dnf clean all`).

## Finding Configuration Quickly
Use `grep -R` within `/etc`:
```bash
sudo grep -R "Port" /etc/ssh/
```

## Example Exploration Session
```bash
cd /
ls -1
for d in bin etc var lib home opt; do echo "--- $d"; ls -ld $d; done
ls /proc | head
lsblk
df -h /
```

## Best Practices
1. Avoid editing files directly under `/usr` or `/bin`; they are managed by the package manager.
2. Store custom scripts under `/usr/local/bin` or `$HOME/bin`.
3. Keep app data separate from config (config in `/etc/<app>`, data in `/var/lib/<app>`).
4. Mount external storage consciously (document in `/etc/fstab`).

## Exercises
1. List top 5 largest directories under `/var`.
2. Find which package owns `/bin/ls`.
3. Display the sticky bit on `/tmp`.
4. Show current mount points for disks only.
5. Count number of running process directories in `/proc`.

## Quick Answers
```bash
sudo du -sh /var/* | sort -h | tail -5
rpm -qf /bin/ls        # On rpm systems; dpkg -S /bin/ls for deb
ls -ld /tmp            # Look for trailing t
mount | grep -v tmpfs | grep '^/'
ls /proc | grep -E '^[0-9]+$' | wc -l
```

---
Next: Learn command usage in `foundations/BasicCommands.md` and package management in `packaging/PackageManagement.md`.