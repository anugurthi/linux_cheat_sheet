# Permissions & Ownership

Linux uses a permission model to control access to files and directories. Understanding it prevents accidental exposure and enables secure scripting.

## Users & Groups
| Term | Description |
|------|-------------|
| User (owner) | Account that owns a file (first permission triad) |
| Group | Group ownership (second triad) |
| Others | Everyone else (third triad) |
| UID/GID | Numeric identifiers for user and group |

Check identity:
```bash
whoami
id
```

## Permission Bits
Representation examples:
```
rwxr-xr--  # Typical file
rw-rw-r--  # Collaborative (group write)
-rwsr-xr-x  # Setuid binary
```
Each triad: read (r), write (w), execute (x). For directories, execute means **enter/search** (permission to traverse).

| Type | Read | Write | Execute |
|------|------|-------|---------|
| File | View contents | Modify contents | Run as program/script |
| Directory | List names | Create/remove/rename entries | Access inside, follow path |

## Symbolic vs Numeric Modes
| Numeric | Symbolic | Meaning |
|---------|----------|---------|
| 7 | rwx | read + write + execute |
| 6 | rw- | read + write |
| 5 | r-x | read + execute |
| 4 | r-- | read only |
| 3 | -wx | write + execute |
| 2 | -w- | write only |
| 1 | --x | execute only |
| 0 | --- | no permissions |

Setting permissions:
```bash
chmod 644 file.txt      # rw-r--r--
chmod 755 script.sh     # rwxr-xr-x (common for executables)
chmod u+x script.sh     # Add execute to user
chmod g-w notes.txt     # Remove write from group
chmod o= file.txt       # Remove all permissions from others
```

Recursive change:
```bash
chmod -R 755 /opt/myapp
```

## Ownership
Change owner & group (needs sudo):
```bash
chown alice file.txt          # Change owner
chown alice:devs file.txt     # Owner alice, group devs
chgrp devs file.txt           # Change group only
chown -R root:root /srv/data  # Recursive on directory
```

## umask (Default Creation Permissions)
`umask` subtracts permissions from the defaults (666 for files, 777 for directories). Common umask values:
| umask | File result (base 666) | Dir result (base 777) |
|-------|------------------------|-----------------------|
| 022 | 644 | 755 |
| 027 | 640 | 750 |
| 077 | 600 | 700 |

Check current:
```bash
umask
```
Set (shell session):
```bash
umask 027
```

## Special Bits
| Bit | Symbol | Applies To | Effect |
|-----|--------|------------|--------|
| Setuid | s (user x) | Executable file | Runs with owner's UID (e.g. `passwd` runs as root) |
| Setgid | s (group x) | Executable file / directory | File: runs with group GID. Directory: new files inherit group |
| Sticky | t (other x) | Directory | Prevent users deleting others' files (e.g. `/tmp`) |

Examples:
```bash
ls -l /usr/bin/passwd    # -rwsr-xr-x (setuid root)
ls -ld /tmp              # drwxrwxrwt (sticky bit)
chmod g+s shared_dir     # Enable setgid on directory
```

Setting special bits numerically (4 setuid, 2 setgid, 1 sticky prefixes):
```bash
chmod 4755 mybin         # setuid + 755
chmod 2755 mydir         # setgid + 755
chmod 1777 /shared/tmp   # sticky + world-writable
```

## Checking Permissions Quickly
```bash
stat script.sh           # Detailed view including octal
namei -l /path/to/target # Break down each component permissions
```
(Install `namei` from util-linux if missing.)

## Common Patterns
| Scenario | Mode | Notes |
|----------|------|-------|
| Public readable docs | 644 | Owner edit only |
| Executable script | 755 | Avoid world write |
| Private key | 600 | Owner only |
| Shared project dir | 2775 | Setgid keeps group inheritance |
| Scratch temp dir | 1777 | Sticky prevents deletion (like /tmp) |

## Hardening Tips
- Remove world write except where truly needed (`/tmp`, maybe shared build dirs with sticky bit).
- Use setgid on collaborative directories so new files inherit group.
- Avoid setuid on custom scripts (use sudoers instead).
- Restrict private credentials: `chmod 600 id_rsa`.

## Practice
1. Create `shared` directory with group inheritance: `mkdir shared && chmod 2775 shared`.
2. Make a script executable only by you: `touch run.sh && chmod 700 run.sh`.
3. Change ownership of a folder to a service account: `sudo chown service:service /srv/appdata`.

---
Try: `touch test.txt && chmod 640 test.txt && ls -l test.txt`.
