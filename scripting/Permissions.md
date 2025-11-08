# Permissions & Ownership (Security Fundamentals)

Linux permissions control access to files and directories. Understanding them prevents accidental exposure and enables secure collaboration.

## Permission Model
Each file has:
1. Owner (user)
2. Group
3. Mode bits (r, w, x for user, group, others)

Example long listing:
```
-rwxr-x--- 1 alice dev 2048 Jan  1 10:00 deploy.sh
```
Breakdown:
| Segment | Meaning |
|---------|---------|
| `-` | Regular file (d=directory, l=symlink) |
| `rwx` (user) | Owner can read/write/execute |
| `r-x` (group) | Group can read & execute |
| `---` (others) | No access to others |

## Numeric (Octal) Representation
| Bit | Value |
|-----|-------|
| r | 4 |
| w | 2 |
| x | 1 |

Combine per scope: `rwx`=7, `rw-`=6, `r-x`=5, `r--`=4.
Common modes:
| Mode | Meaning |
|------|---------|
| 644 | rw-r--r-- (files) |
| 600 | rw------- (private) |
| 755 | rwxr-xr-x (public executable) |
| 700 | rwx------ (private executable) |
| 750 | rwxr-x--- (team executable) |

Set with:
```bash
chmod 644 notes.txt
chmod 755 script.sh
chmod 750 /srv/app
```

## Symbolic Changes
| Command | Effect |
|---------|-------|
| `chmod u+x file` | Add execute to user |
| `chmod g-w file` | Remove write from group |
| `chmod o-r file` | Remove read from others |
| `chmod ug=rw file` | Set user & group to rw |
| `chmod a+r file` | Everyone gets read |

## Directories vs Files
Execute bit on directory allows traversal (entering & accessing names). Without `x`, you cannot `cd` into or list contents even if read bit set.

## Ownership
Change with `chown` / `chgrp` (needs root for most changes):
```bash
sudo chown alice:dev deploy.sh
sudo chgrp dev shared/
```
Ownership determines whose permissions apply; group membership influences collaborative access.

## Umask (Default Permission Mask)
Umask subtracts bits from default creation modes.
Defaults: files start 666; directories 777.
Umask 022 => files 644, dirs 755.
Show/change:
```bash
umask          # Display
umask 027      # New files 640, dirs 750
```

## Special Permission Bits
| Bit | Symbol | Octal Prefix | Effect |
|-----|--------|--------------|--------|
| setuid | s on user execute | 4xxx | Run with file owner's UID |
| setgid | s on group execute | 2xxx | Run with file's group GID or directory group inheritance |
| sticky | t on others execute | 1xxx | Prevent deletion by non-owners in world-writable dir |

Examples:
```bash
ls -l /usr/bin/passwd   # Shows setuid (s)
ls -ld /tmp             # Shows sticky (t)
chmod 4755 mytool       # Add setuid (use cautiously)
chmod 2755 shareddir    # setgid for group inheritance
```
Directory setgid ensures new files inherit directory group -> simplifies team collaboration.

## Inspecting Permissions
| Command | Purpose |
|---------|--------|
| `namei -l path` | Break down each component permissions |
| `stat file` | Detailed mode & numeric value |
| `ls -ld dir` | Directory mode |
| `find . -perm 777` | World-writable files |
| `find / -xdev -type f -perm -4000` | All setuid files (security audit) |

## Restricting Access
Private notes:
```bash
chmod 600 notes.txt
```
Team scripts:
```bash
chown alice:dev deploy.sh
chmod 750 deploy.sh
```
Public web content (static):
```bash
chmod 644 index.html
```

## Security Considerations
- Avoid 777 modes; use least privilege.
- Limit setuid binaries; audit regularly.
- Sticky bit on shared dirs (`/tmp`) prevents file takeover.
- Use dedicated groups for collaboration instead of wide open others.

## Example Workflow
```bash
mkdir project
touch project/run.sh
chmod 700 project/run.sh      # Private script initially
groupadd devs                 # (sudo; skip if exists)
sudo chown $USER:devs project/run.sh
chmod 750 project/run.sh      # Now share with devs group
umask 027                     # Future files more restrictive
```

## Auditing Script
```bash
find /var/www -type f -perm -o=w -print
```
List any world-writable files under web root.

## Exercises
1. Create a directory `teamshare` with mode 2770 so new files inherit group.
2. Make a script executable for everyone but writable only by owner.
3. Find all setuid binaries on system.
4. Set umask so new directories are 750 and files 640.
5. Remove write permission for others on all `.log` files in current dir.

## Answers (Sketch)
```bash
mkdir teamshare && chmod 2770 teamshare
chmod 755 script.sh
find / -xdev -type f -perm -4000 2>/dev/null
umask 027
chmod o-w *.log
```

---
Next: Learn to automate with scripts in `scripting/RunningScripts.md`. See DevOps hardening in forthcoming `devops/DevOpsEssentials.md`.