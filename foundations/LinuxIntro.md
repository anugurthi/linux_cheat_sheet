# What Is Linux?

Linux is a family of open-source operating systems built around the Linux kernel (the core program that talks to hardware and manages resources). A "Linux distribution" (distro) bundles the kernel with system utilities, libraries, package manager, and often a desktop environment.

## Key Components
| Component | Role | Example |
|-----------|------|--------|
| Kernel | Talks to hardware, schedules processes, manages memory, security | `linux 6.x` |
| Shell | Command interpreter | `bash`, `zsh`, `fish` |
| GNU/Userland | Core tools (ls, cp, grep, coreutils) | `/usr/bin/*` |
| Package Manager | Install/update/remove software | `apt`, `dnf`, `yum`, `pacman`, `zypper` |
| Init/System Manager | Boot & service control | `systemd`, `OpenRC` |
| Desktop Environment | Graphical interface | `GNOME`, `KDE Plasma`, `XFCE` |

## Why Linux?
- Free & open source
- Stable and secure (permissions + user separation)
- Massive ecosystem (servers, cloud, embedded, desktops)
- Scriptable & automatable
- Strong community support

## Popular Distributions (Flavours)
| Distro | Family | Package Manager | Use Case Highlights |
|--------|--------|-----------------|---------------------|
| Ubuntu | Debian | `apt` | Beginner friendly, huge community, server & desktop |
| Debian | Debian | `apt` | Stability, conservative updates |
| Linux Mint | Debian/Ubuntu | `apt` | Desktop ease, Cinnamon desktop |
| Fedora | Red Hat | `dnf` | Latest features, developer workstation |
| CentOS Stream | Red Hat | `dnf` | Upstream of RHEL, server environments |
| RHEL | Red Hat (Commercial) | `dnf` | Enterprise support & certifications |
| openSUSE Leap | SUSE | `zypper` | Stable desktop/server |
| openSUSE Tumbleweed | SUSE | `zypper` | Rolling release (bleeding edge) |
| Arch Linux | Independent | `pacman` | Rolling release, DIY customization |
| Manjaro | Arch | `pacman` | Easier Arch experience |
| AlmaLinux/Rocky | Red Hat Clone | `dnf` | Community rebuilds of RHEL |
| Kali Linux | Debian | `apt` | Security testing & forensics |
| Raspberry Pi OS | Debian | `apt` | Education, hobby hardware |

## Choosing a Distribution
Beginner desktop: Ubuntu, Linux Mint, Fedora Workstation.
Minimal/server: Debian, AlmaLinux, Rocky, Ubuntu Server.
Learning internals: Arch, Gentoo (more advanced), Void.
Rolling release (always newest): Arch, openSUSE Tumbleweed.
Enterprise support: RHEL, SLES (commercial).

## Filesystem Snapshot
Everything hangs off `/` (root). Users live under `/home/username`. Config files in `/etc`. Logs in `/var/log`. Apps mostly in `/usr/bin` or `/usr/local/bin` (user-installed).

See detailed layout in `FileSystem.md`.

## Terminal Basics
A terminal runs a shell. You type commands, press Enter, shell parses them, executes programs, and shows output.

Prompt example:
```
user@host:~/projects$ 
```
Parts:
- `user` current username
- `host` hostname
- `~/projects` current directory
- `$` regular user (`#` would indicate root)

## Getting Help
- `man command` (manual pages)
- `command --help` (quick help)
- `tldr command` (install with package manager for simplified pages)
- Community forums & documentation (Arch Wiki is excellent even for non-Arch users)

## Next Topics
1. Package management (`packaging/PackageManagement.md`)
2. File operations (`scripting/FileOperations.md`)
3. Permissions (`scripting/Permissions.md`)
4. Running scripts & automation (`scripting/RunningScripts.md`)

---
Ready? Open a terminal and type: `uname -a` then explore.