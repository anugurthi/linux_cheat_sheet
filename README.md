# Linux Beginner & DevOps Launchpad

This repository is a progressive learning path for absolute beginners to go from “I’ve never used Linux” to “I can manage and automate servers like a junior DevOps engineer.” Start with the roadmap, then open each guide and practice every command.

## Core Guides (Structured Folders)
| Topic | File (Path) | What You'll Learn |
|-------|-------------|-------------------|
| Orientation & distros | `foundations/LinuxIntro.md` | What Linux is, choosing a distribution |
| Roadmap | `roadmap/ROADMAP.md` | Phases, milestones, practice cadence |
| Filesystem | `foundations/FileSystem.md` | Directory purposes, space inspection |
| Everyday commands | `foundations/BasicCommands.md` | Navigation, system info, files, monitoring basics |
| Listing mastery | `lsCommands.md` (to be moved under foundations) | Sorting, filtering, flags, aliases |
| Key concepts map | `foundations/KeyConcepts.md` | Concept relationships, mental model |
| Packages | `packaging/PackageManagement.md` | apt, dnf/yum, pacman, zypper fundamentals |
| RPM deep dive | `packaging/RPMCommands.md` | Low-level queries & verification |
| File operations | `scripting/FileOperations.md` | Create, edit, move, search, archive |
| Permissions | `scripting/Permissions.md` | rwx, ownership, umask, special bits |
| Scripting | `scripting/RunningScripts.md` | Shebangs, execution, error handling |
| DevOps essentials | `devops/DevOpsEssentials.md` (incoming) | Services, logging, containers, hardening |
| Interview prep | `interview/InterviewQuestions.md` | Common Linux & DevOps questions |

## Quick Start (First 10 Minutes)
```bash
# Where am I?
pwd
# What kernel am I on?
uname -r
# What does the top-level filesystem look like?
ls /
# Make a playground and explore
mkdir -p ~/play && cd ~/play
# Create and inspect files
touch notes.txt
ls -l
# Write something
echo "Linux is fun" > notes.txt
cat notes.txt
# Disk and memory overview
df -h | head
free -h
```

Next: Open `FileSystem.md` and read the directory table; then reproduce the examples in a terminal.

## Recommended Sequence
1. `foundations/LinuxIntro.md` – Understand the landscape.
2. `roadmap/ROADMAP.md` – See the full journey.
3. `foundations/BasicCommands.md` – Build terminal muscle memory.
4. `foundations/FileSystem.md` – Know where things live.
5. `lsCommands.md` (will relocate under foundations) – Efficient file inspection.
6. `foundations/KeyConcepts.md` – Connect the conceptual dots.
7. `scripting/FileOperations.md` – Manipulate data safely.
8. `scripting/Permissions.md` – Secure access.
9. `packaging/PackageManagement.md` & `packaging/RPMCommands.md` – Install & audit software.
10. `scripting/RunningScripts.md` – Automate tasks.
11. `devops/DevOpsEssentials.md` – Integrate services, logging & containers.
12. `interview/InterviewQuestions.md` – Solidify & prepare.

## Core Command Categories (Snapshot)
Navigation: `pwd`, `cd`, `ls`
Files: `touch`, `cat`, `less`, `head`, `tail`, `rm`, `cp`, `mv`
System info: `hostnamectl`, `uname`, `df`, `free`
Processes: `ps`, `top`
Search: `find`, `grep`
Networking: `ip`, `ping`, `curl`
Packages: `apt install`, `dnf install`, `pacman -S`, `zypper install`, `rpm -qi`

## Safety First
* Use `rm -i` until confident; beware of wildcards.
* Quote variables: `echo "$FILE"` to avoid glob expansion.
* Prefer `ip` instead of deprecated `ifconfig`.
* Sandbox everything in `~/lab` before touching system paths.

## Glossary (Starter)
| Term | Meaning |
|------|---------|
| Root directory `/` | Top of the filesystem tree |
| Home directory `~` | User workspace (`/home/username`) |
| Process | Running instance of a program |
| Package | Installable bundle of files + metadata |
| Inode | Metadata record pointing to file data blocks |
| Shebang | First line selecting interpreter (`#!/usr/bin/env bash`) |
| umask | Mask deciding default new file permissions |

## Why Linux Matters in DevOps
| Area | Linux Skill Impact |
|------|--------------------|
| CI/CD | Script builds, manage artifacts, parse logs |
| Infra Automation | Understand state for Ansible/Terraform modules |
| Containers | Kernel features (namespaces, cgroups) underpin Docker/K8s |
| Monitoring | Command-line triage accelerates incident response |
| Security | Permissions, patching, file integrity verification |
| Performance | Tools (`top`, `iotop`, `vmstat`) to detect bottlenecks |

See `roadmap/ROADMAP.md` for phased progression and milestone checks.

## Contributing
Add or improve guides with:
* Clear learning objective at top
* Runnable examples
* Safety notes for destructive commands
* Practice tasks + next steps

## License
Free to use and share. Attribute if heavily reused.

---
Start now: open `LinuxIntro.md`, then create `~/lab` and replicate the Quick Start commands.
