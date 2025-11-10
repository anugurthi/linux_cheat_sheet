# Linux for DevOps – 7 Day Roadmap

One compact repository to learn the Linux pieces that matter for beginners. You’ll touch navigation, packages, logs, services, scripting, networking, containers, and a taste of config management—without deep server admin detours.

Start with `ROADMAP.md` (flattened one‑week plan) then open each guide and run every command in your own lab environment—try an EC2 instance, an Azure VM, a local VM, or a GCP VM for hands-on practice.

## Guides (Flat Files)
| Topic | File | Focus |
|-------|------|-------|
| Orientation & distros | `LinuxIntro.md` | What Linux is & choosing a distro |
| Filesystem layout | `FileSystem.md` | Where configs, logs, binaries live |
| Everyday commands | `BasicCommands.md` | Core command muscle memory |
| Key conceptual map | `KeyConcepts.md` | How pieces fit together |
| File manipulation | `FileOperations.md` | Create, search, archive, globbing |
| Permissions & ownership | `Permissions.md` | rwx, umask, special bits |
| Package management | `PackageManagement.md` | apt/dnf/pacman/zypper workflow |
| RPM deep dive | `RPMCommands.md` | Low-level package inspection & verify |
| Scripting basics | `RunningScripts.md` | Shebangs, arguments, safety flags |
| DevOps essentials | `DevOpsEssentials.md` | Services, logs, monitoring, containers |
| Interview practice | `InterviewQuestions.md` | Q&A quick reference |
| 7‑Day plan | `ROADMAP.md` | Daily objectives & artifacts |

Optional legacy `lsCommands.md` still present (detailed `ls` usage); integrate as needed.

## External Learning References (Curated)
The following external resource clusters can deepen topics beyond this repo. These are organized thematically based on large publicly available tutorial collections (e.g. TecMint). Use them for drill-down; do NOT attempt to consume everything linearly.

| Theme | Why It Matters | External Topic Cluster (Examples to Search) |
|-------|----------------|---------------------------------------------|
| Intro & Install | Pick a distro, understand boot basics | "Linux boot process", "VirtualBox install <distro>", "beginner distro comparison" |
| Core Commands | Build muscle memory with variations | "ls command examples", "find practical examples", "grep patterns" |
| Advanced CLI | Efficiency & text wrangling | "sort advanced options", "wc usage", "rename batch files", "lsof examples" |
| Users & Permissions | Secure multi-user environments | "useradd vs adduser", "sudo configuration", "ACLs linux" |
| Package Management | Multi-distro proficiency | "apt vs apt-get", "dnf commands", "rpm verify", "zypper basics" |
| Monitoring & Scheduling | Operate and automate | "top command flags", "cron scheduling examples", "performance monitoring tools" |
| Archiving & Backup | Data safety & migration | "tar advanced examples", "rsync remote sync", "snapshot tools" |
| Filesystems & Storage (Later) | Scaling & reliability | "ext4 vs xfs", "LVM snapshot", "RAID levels explained" |
| Services & systemd | Reliable app lifecycle | "systemctl manage services", "journalctl filtering" |
| Security & Firewall | Hardening foundations | "SSH hardening tips", "iptables basics", "firewalld common rules" |
| Web Stacks (Optional) | Deploy application stacks | "LAMP setup", "LEMP nginx basics", "Apache virtual hosts SSL" |
| Database Admin (Optional) | Persist & tune data | "mysql backup restore", "master slave replication basics" |
| Shell Scripting Deep Dive | Stronger automation | "bash arrays", "functions in shell", "parameter expansion" |
| Interview Prep | Assessment readiness | "linux interview questions", "systemd interview questions" |
| Certification (Optional) | Structured milestones | "RHCSA objectives", "LFCS study guide" |

Minimal strategy: for each day in `ROADMAP.md`, pick ONE external deep-dive article aligned to the day's theme and extract 3 actionable notes into your lab journal.

> Tip: Avoid passive reading loops. Convert each external article into: (a) command you tried, (b) error you triggered & resolved, (c) note you can teach someone else.

### External Resource Selection Principles
1. Prefer distro-agnostic conceptual guides first (filesystem, permissions, processes).  
2. When a command differs by family (apt/dnf/pacman/zypper), practice the equivalent on a second distro in a disposable VM.  
3. Skip advanced storage (LVM/RAID) until core daily DevOps tasks feel automatic.  
4. Revisit security articles after you can reliably create and manage services.  
5. Treat certification guides as optional structured checklists, not mandatory early reading.

### Example External Deep-Dive Mapping to This Repo
| This Repo Page | Suggested External Follow-Up Query | Outcome to Capture |
|----------------|------------------------------------|--------------------|
| `FileSystem.md` | "Linux directory structure explained" | 2 directories you hadn't explored + one config file path |
| `Permissions.md` | "Linux sticky bit example" | Difference between sticky vs setgid on directories |
| `PackageManagement.md` | "rpm verify examples" | One altered config file detection scenario |
| `RunningScripts.md` | "cron scheduling tasks examples" | A cron entry plus how to list existing crontabs safely |
| `DevOpsEssentials.md` | "systemctl manage services" | 3 states (active, failed, enabling) screenshot or notes |
| `FileOperations.md` | "find command practical examples" | One pipeline combining find + xargs safely |

Keep external links in a personal notes file instead of cluttering this README; update only if a resource fundamentally changes workflow best practices.

---

# Linux Learning Modules (Structured Format)

This repository now uses a module-based progression (01–10). Each module has a concise summary and hands-on checklist. Work inside a sandbox directory like `~/lab`.

## Module Index
| # | Folder | Theme |
|---|--------|-------|
| 01 | `modules/01-intro` | Introduction & distro choice |
| 02 | `modules/02-filesystem` | Filesystem layout & navigation |
| 03 | `modules/03-core-commands` | Everyday core commands |
| 04 | `modules/04-file-ops` | File ops & basic pipelines |
| 05 | `modules/05-permissions` | Permissions & ownership |
| 06 | `modules/06-packages` | Package management fundamentals |
| 07 | `modules/07-scripting` | Bash scripting fundamentals |
| 08 | `modules/08-services-logs` | Services, processes & logs |
| 09 | `modules/09-devops` | DevOps integration (scripts + service + container + Ansible) |
| 10 | `modules/10-interview` | Interview readiness consolidation |

## How to Use
1. Read the module summary.
2. Execute each checklist item in your lab environment.
3. Record notes (questions, outputs, errors) in a personal journal.
4. Mark exit criteria; only then proceed.
5. Revisit earlier modules if an interview question feels weak.

## 7-Day Compression
See `ROADMAP.md` for a day-by-day mapping.

## Sandbox Setup (Quick)
```bash
mkdir -p ~/lab && cd ~/lab
mkdir notes && echo "Start" > notes/progress.txt
```

## Validation Command Set (Sample)
When finished with core modules run:
```bash
uname -a
ls -ld /tmp
find . -name '*.sh'
ps aux | head
systemctl status sshd 2>/dev/null || echo 'Skip service check'
```

## Contributing
- Keep modules short (under ~150 lines).
- Include Goal, Focus Points, Checklist, Exit Criteria, Next pointer.
- Avoid duplicating deep theory; link externally in personal notes.

## License
Open use; attribute if redistributed.

---
Start at `modules/01-intro`. Happy learning.
| Root directory `/` | Top of the filesystem tree |

---

## Connect
Created by [Anugurthi](https://www.linkedin.com/in/anugurthi/). Feel free to connect or reach out for feedback, collaboration, or questions!
