# Module 01: Introduction

Goal: Understand what Linux is, major components, choose a distro, and become comfortable with the terminal prompt.

## Concept Primer
Linux is a kernel; a distribution is the kernel + curated userland packages (shell, coreutils, libraries, package manager) + policies/config defaults. When you type a command:
1. Shell parses the line (expands variables, wildcards).
2. Finds the executable via the `PATH` environment variable.
3. Launches a process the kernel schedules on CPU and manages in memory.

### Kernel vs Userland vs Distribution
| Layer | What It Provides | Example |
|-------|------------------|---------|
| Kernel | Hardware abstraction (processes, memory, filesystem drivers, networking stack) | `Linux 6.x` |
| Userland | Everyday tools & libraries | `bash`, `ls`, `glibc`, `coreutils` |
| Distribution | Packaging + updates + defaults | Ubuntu, Fedora, Debian, Arch |

### Choosing a Practice Distro
| Scenario | Recommended | Why |
|----------|------------|-----|
| Beginner desktop | Ubuntu / Linux Mint | Easy installer, large docs |
| Stable server | Debian / AlmaLinux | Conservative updates |
| Latest features dev workstation | Fedora | New kernels & tooling |
| DIY learning internals | Arch | Manual configuration teaches structure |

### Filesystem High-Level
| Path | Purpose | Starter Action |
|------|---------|---------------|
| `/etc` | System configs | `ls /etc | head` |
| `/var/log` | Logs (rotating) | `ls -lh /var/log | head` |
| `/home` | User data | `echo $HOME` |
| `/tmp` | Temporary scratch | Create + remove test file |

### Getting Help
| Tool | Use Case | Example |
|------|----------|---------|
| `man` | Full manual page | `man uname` |
| `--help` | Quick flags | `uname --help` |
| `tldr` (install) | Concise examples | `tldr tar` |

## Why It Matters
Grasping these distinctions early prevents confusion when troubleshooting: if a command fails, is it because the package isn’t installed (distribution/package layer) or because of kernel permissions/capabilities? Knowing where configs and logs live accelerates diagnosis.

## Common Pitfalls
| Pitfall | Symptom | Fix |
|---------|---------|-----|
| Confusing distro vs kernel version | Think  "Fedora 6.x" | Kernel: `uname -r`, Distro: `cat /etc/os-release` |
| Using `sudo` everywhere | Permission errors vanish, learning stalls | First try without `sudo`, then analyze permission bits |
| Ignoring logs | Can't explain failures | Check `/var/log` or `journalctl -xe` early |

## Mini Walkthrough
```bash
uname -a                # Kernel & build info
cat /etc/os-release | grep PRETTY_NAME  # Distro identification
echo $PATH              # Search path for executables
which bash; type ls     # Locate executables & get shell classification
ls -ld /etc /var/log /home /tmp
man uname | head -20    # Inspect first part of manual page
```

## Practice Checklist
1. Run identification commands: `uname -a`, `cat /etc/os-release`.
2. List top-level directories and write one line each in a notes file.
3. Use `man`, then `--help` for the same command; note difference in depth.
4. Choose a practice distro and justify with 3 criteria (community, update cadence, stability).
5. Create `~/lab/intro/notes.md` documenting new commands you discovered.

## Reflection Questions
1. What happens if you remove a binary in `/bin` vs `/usr/bin`? (Boot impact vs optional tool.)
2. Why is `/tmp` world-writable yet usually safe? (Permissions + ephemeral nature.)
3. How does `PATH` order affect which version of a tool runs?

Exit Criteria: You can clearly explain kernel vs userland vs distribution, locate configs & logs, and retrieve help in two ways.

Next: Module 02 (Filesystem) for a deeper directory model.