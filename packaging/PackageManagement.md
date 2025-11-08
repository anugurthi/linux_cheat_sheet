# Package Management Across Distributions

Different Linux distributions use different package managers. They all aim to install, update, remove, and query software. Learn the one for your distro; concepts transfer easily.

## Overview
| Family | Distro Examples | Manager | Package Format |
|--------|-----------------|---------|----------------|
| Debian | Ubuntu, Debian, Linux Mint | `apt` / `dpkg` | `.deb` |
| Red Hat | Fedora, CentOS Stream, RHEL, AlmaLinux | `dnf` / `yum` / `rpm` | `.rpm` |
| Arch | Arch, Manjaro | `pacman` | `.pkg.tar.zst` |
| SUSE | openSUSE Leap/Tumbleweed | `zypper` / `rpm` | `.rpm` |

## Basic Operations by Manager
### APT (Debian/Ubuntu)
```bash
sudo apt update                 # Refresh repositories index
sudo apt upgrade                # Upgrade all upgradable packages
sudo apt install htop           # Install package
sudo apt remove htop            # Remove package (keep config)
sudo apt purge htop             # Remove package + config files
sudo apt autoremove             # Remove unused dependencies
apt search nginx                # Search for packages
apt show nginx                  # Package details
dpkg -l | grep bash             # List installed packages via dpkg
```

### DNF / YUM (Fedora/RHEL/CentOS/Alma/Rocky)
```bash
sudo dnf check-update           # Check for updates
sudo dnf upgrade                # Upgrade packages
sudo dnf install htop           # Install
sudo dnf remove htop            # Remove
sudo dnf autoremove             # Remove unused dependencies
sudo dnf search nginx           # Search
sudo dnf info nginx             # Details
rpm -qa | grep bash             # List installed via rpm database
```
(Older systems may use `yum` with similar syntax.)

### Pacman (Arch/Manjaro)
```bash
sudo pacman -Syu                # Sync & upgrade (update all)
sudo pacman -S htop             # Install package
sudo pacman -R htop             # Remove (keep dependencies)
sudo pacman -Rs htop            # Remove + unneeded dependencies
sudo pacman -Q                  # List installed packages
sudo pacman -Ss nginx           # Search repositories
sudo pacman -Qi nginx           # Info about package
sudo pacman -Ql htop            # Files installed by package
```

### Zypper (openSUSE)
```bash
sudo zypper refresh             # Refresh repository metadata
sudo zypper update              # Update installed packages
sudo zypper install htop        # Install
sudo zypper remove htop         # Remove
sudo zypper search nginx        # Search
sudo zypper info nginx          # Details
rpm -qa | grep bash             # Query rpm database
```

## Installation from Local File
| Manager | Command |
|---------|---------|
| `apt` (Debian) | `sudo apt install ./package.deb` |
| `dpkg` direct | `sudo dpkg -i package.deb` (then `sudo apt -f install` to fix deps) |
| `dnf` | `sudo dnf install package.rpm` |
| `rpm` raw | `sudo rpm -ivh package.rpm` (manual deps) |
| `pacman` | Use repo or build from AUR (helpers: `yay`, `paru`) |
| `zypper` | `sudo zypper install package.rpm` |

## Searching and Filtering
```bash
apt search docker | head
sudo dnf search docker | head
sudo pacman -Ss docker | head
sudo zypper search docker | head
```

## Checking for Security / Integrity
| Action | Command |
|--------|---------|
| Verify rpm signature | `rpm -K file.rpm` |
| List installed versions | `apt list --installed | grep openssl` |
| Show advisory info (dnf) | `dnf updateinfo list` |
| Pacman keyring refresh | `sudo pacman-key --refresh-keys` |

## Cleaning Up
| Manager | Cleanup Commands |
|---------|------------------|
| apt | `sudo apt autoremove`, `sudo apt clean` |
| dnf | `sudo dnf autoremove`, `sudo dnf clean all` |
| pacman | `sudo pacman -Rns $(pacman -Qtdq)` (orphans) |
| zypper | `sudo zypper clean --all` |

## Inspecting Files & Dependencies
```bash
apt show bash | grep Depends
rpm -qR bash
sudo pacman -Qi bash | grep Depends
zypper info bash | grep Requires
```

## Common Pitfalls
| Issue | Cause | Fix |
|-------|-------|-----|
| Broken dpkg install | Missing dependencies | `sudo apt -f install` |
| Cannot import RPM key | Missing GPG key | `sudo rpm --import KEYFILE` or repo config |
| Pacman file conflict | Manual file created | Remove/rename file then retry |
| Slow mirrors | Bad server choice | Change mirror list (e.g., `/etc/pacman.d/mirrorlist`) |

## Recommended Practice
1. Always update index (apt update / zypper refresh) before installing.
2. Prefer distro repos over manual downloads to get updates automatically.
3. Remove unneeded packages to reduce surface and reclaim space.
4. Read changelogs for major upgrades (e.g., kernel, database servers).

## Example Multi-step Session (Debian-based)
```bash
sudo apt update
sudo apt upgrade -y
sudo apt install curl
curl --version
apt show curl | grep -E "Version|Depends"
sudo apt autoremove
```

## Next Steps
Dive deeper into RPM queries (`packaging/RPMCommands.md`) or learn about compilation from source and containerized packaging (Docker images, Flatpak, Snap).

---
Exercise: Pick a package (e.g., `htop`) and perform install, query, remove, reinstall using your distro's manager.