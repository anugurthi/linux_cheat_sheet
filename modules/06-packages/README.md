# Module 06: Package Management

Goal: Install, query, remove, verify, and clean up software across major Linux distribution families.

## Concept Primer
Package management ensures consistent installation of software plus automatic handling of dependencies and updates. High-level managers (apt, dnf, pacman, zypper) resolve dependencies and consult repositories; low-level tools (dpkg, rpm) manipulate package databases directly.

### Manager Comparison
| Action | Debian/Ubuntu | RHEL/Fedora | Arch | openSUSE |
|--------|---------------|-------------|------|----------|
| Refresh index | `apt update` | `dnf check-update` (metadata auto) | `pacman -Sy` | `zypper refresh` |
| Upgrade installed | `apt upgrade` | `dnf upgrade` | `pacman -Syu` | `zypper update` |
| Install package | `apt install pkg` | `dnf install pkg` | `pacman -S pkg` | `zypper install pkg` |
| Remove keep config | `apt remove pkg` | `dnf remove pkg` | `pacman -R pkg` | `zypper remove pkg` |
| Remove incl config | `apt purge pkg` | (configs usually in /etc) | `pacman -Rns pkg` | `zypper rm pkg` |
| List files | `dpkg -L pkg` | `rpm -ql pkg` | `pacman -Ql pkg` | `rpm -ql pkg` |
| Package info | `apt show pkg` | `dnf info pkg` | `pacman -Si pkg` | `zypper info pkg` |
| Verify integrity | `debsums pkg` (if installed) | `rpm -V pkg` | `pacman -Qk pkg` | `rpm -V pkg` |

### Low-Level Tools
| Tool | Purpose | Example |
|------|---------|---------|
| `dpkg` | Install/query .deb files without dependency resolution | `sudo dpkg -i file.deb` |
| `rpm` | Install/query .rpm files & verify | `rpm -qi bash` |

Using low-level tools alone risks unresolved dependencies; follow with high-level manager to fix (`sudo apt -f install`).

### Verification & Integrity
`rpm -V` compares installed RPM files against metadata (size, md5, permissions). Differences flagged with codes (e.g., `M` for permissions, `5` for checksum). For Debian-based systems, `debsums` (not always preinstalled) can verify MD5 sums.

### Cleanup
| Manager | Orphans / Unused | Cache Cleaning |
|---------|------------------|----------------|
| apt | `apt autoremove` | `apt clean` (remove cache) |
| dnf | `dnf autoremove` | `dnf clean all` |
| pacman | `pacman -Rns $(pacman -Qtdq)` | `pacman -Sc` (optional) |
| zypper | (deps auto pruned) | `zypper clean --all` |

## Why It Matters
Understanding differences avoids mistakes like mixing manual binary installs with package-managed files (leading to orphaned libraries) and enables auditing exactly which files belong to which package during troubleshooting or security review.

## Pitfalls & Fixes
| Pitfall | Symptom | Fix |
|---------|---------|-----|
| Installing local RPM directly with unmet deps | Install fails / missing libs | Use `dnf install file.rpm` or resolve deps after | 
| Skipping index refresh | Package not found / 404 errors | Run `apt update` or refresh equivalent first |
| Mixing AUR helpers without understanding | Broken Arch system | Review PKGBUILD, trust sources; prefer official repos |
| Ignoring verify output | Undetected tampered config | Integrate `rpm -V` / `debsums` checks in audit script |
| Leaving orphaned packages | Bloat & surface area | Routine `autoremove` / orphan cleanup |

## Walkthrough
```bash
mkdir -p ~/lab/mod06 && cd ~/lab/mod06
sudo apt update 2>/dev/null || sudo dnf check-update 2>/dev/null || true
echo "Info for bash:" > pkg_info.txt
(apt show bash 2>/dev/null | head -20) >> pkg_info.txt || (dnf info bash 2>/dev/null | head -20) >> pkg_info.txt || true
which bash > path_bash.txt
# File list (one of these depending on family)
dpkg -L bash 2>/dev/null | head > files_bash.txt || rpm -ql bash 2>/dev/null | head > files_bash.txt || pacman -Ql bash 2>/dev/null | head > files_bash.txt || true
```

## Practice Checklist
1. Refresh index (family appropriate).
2. Install a small tool (`htop` or `tree`); record version and install time.
3. List files the package installed; identify config vs binary.
4. Verify integrity (`rpm -V` or `debsums`) noting any mismatches.
5. Remove package (keep config), then purge/remove config (if applicable); document file differences.

## Reflection Questions
1. Difference between high-level and low-level manager responsibilities?
2. When would you manually use `rpm -Uvh file.rpm` instead of `dnf install`?
3. Why regularly prune orphaned packages?

Exit Criteria: Explain difference between high-level and low-level tools, list files for a package, and perform integrity verification & cleanup.

Next: Module 07 (Scripting).