# RPM Commands (Low-Level Package Inspection)

While `dnf`/`yum` perform dependency resolution and repository management, `rpm` lets you directly inspect and verify packages at a lower level.

## Core Concepts
| Concept | Explanation |
|---------|-------------|
| RPM Package | Archive with metadata: name, version, release, architecture, dependencies, file list, scripts (pre/post install) |
| RPM Database | Local system database of installed packages stored under `/var/lib/rpm` |
| Spec File | Build recipe describing how the RPM was created |
| GPG Signature | Ensures integrity & origin of package |

## Common Query Options
| Command | Purpose |
|---------|--------|
| `rpm -q pkg` | Is package installed? Show version |
| `rpm -qa` | List all installed packages |
| `rpm -qa | grep httpd` | Filter installed packages |
| `rpm -qi pkg` | Detailed info (description, build time) |
| `rpm -ql pkg` | List files installed by package |
| `rpm -qc pkg` | List configuration files |
| `rpm -qd pkg` | List documentation files |
| `rpm -q --scripts pkg` | Show maintainer scriptlets |
| `rpm -q --changelog pkg` | Changelog entries |

## Dependency & Requirement Queries
| Command | Purpose |
|---------|--------|
| `rpm -qR pkg` | List run-time dependencies (Requires) |
| `rpm -q --provides pkg` | Capabilities provided by package |
| `repoquery --requires pkg` | (dnf plugin) Dependencies via metadata |
| `repoquery --whatprovides capability` | Find which package provides a capability |

## Working with Local RPM Files
| Command | Purpose |
|---------|--------|
| `rpm -K file.rpm` | Verify signature & integrity |
| `rpm -qp file.rpm` | Query package name of rpm file |
| `rpm -qpi file.rpm` | Detailed info from rpm file |
| `rpm -qpl file.rpm` | List files within rpm file (not installed) |
| `rpm -qpR file.rpm` | Dependencies required by rpm file |

## Installing / Upgrading / Removing (Raw rpm)
Prefer `dnf` for dependency resolution. Raw commands:
| Command | Purpose |
|---------|--------|
| `sudo rpm -ivh file.rpm` | Install (verbose, hash progress) |
| `sudo rpm -Uvh file.rpm` | Upgrade or install if not present |
| `sudo rpm -e pkg` | Erase (remove) package |

If dependency errors occur, switch to `sudo dnf install file.rpm` which resolves them.

## Verifying Installed Packages
| Command | Purpose |
|---------|--------|
| `rpm -V pkg` | Verify integrity of installed package files |
| `rpm -Va` | Verify all packages (can be slow) |
| `rpm -Vf /usr/bin/bash` | Which package owns file & verify |

Verification result codes:
```
S Size differs
M Mode (permissions) differs
5 MD5 checksum differs
D Device major/minor differs
L Symlink path changed
U User ownership differs
G Group ownership differs
T Modification time differs
P Capabilities differ
```
If a package file changed legitimately (e.g., config file), you may see expected differences.

## Package Ownership of Files
| Command | Purpose |
|---------|--------|
| `rpm -qf /etc/passwd` | Which package (if any) owns file |
| `rpm -qf /usr/bin/ssh` | Ownership for binary |

Many files (like those created manually) won't belong to any RPM.

## Build Information
| Command | Purpose |
|---------|--------|
| `rpm -qi pkg | grep Build` | Build date, host |
| `rpm -q --changelog pkg | head` | Recent changes |

## Performance & Database Maintenance
Rebuild RPM database if corruption suspected:
```bash
sudo rpm --rebuilddb
```
Clean caches (handled mostly by dnf):
```bash
sudo dnf clean all
```

## Practical Examples
```bash
rpm -qa | wc -l                # Count installed packages
rpm -qa | grep kernel          # Installed kernel packages
rpm -qi bash                   # Info about bash
rpm -ql bash | head            # Files from bash
rpm -qR bash | head            # Dependencies
rpm -V bash                    # Verify integrity
rpm -qf /bin/ls                # Owner of /bin/ls
```

## Troubleshooting
| Issue | Symptom | Resolution |
|-------|---------|------------|
| DB corruption | Query errors | `sudo rpm --rebuilddb` |
| Signature failure | `FAILED` on `rpm -K` | Import GPG key (`rpm --import KEY`) |
| Missing dependencies (raw install) | Install fails | Use `dnf install file.rpm` |
| Modified critical file | Verification flags | Reinstall or inspect edits |

## When to Use rpm Directly
- Need granular inspection of package contents or scripts.
- Verifying integrity/security of critical packages.
- Debugging dependency issues where `dnf` output insufficient.
- Building custom packages (spec file development).

## Next Steps
Learn spec file basics and simple RPM building with `rpmbuild -ba specfile`. Explore `mock` for clean build roots.

---
Exercise: Pick an installed package (e.g., `bash`) and run `rpm -qi`, `rpm -ql`, `rpm -qR`, then modify a config file and see how verification output changes.