# Module 05: Permissions & Ownership

Goal: Understand and apply the Unix permission model securely for files and directories.

## Concept Primer
Each file has three permission triplets: user (owner), group, others. Bits: read (r), write (w), execute (x). Directories interpret execute as the ability to traverse (enter and access names inside).

### Numeric vs Symbolic
Numeric combines values: r=4, w=2, x=1. Example: `chmod 750 script.sh` yields `rwxr-x---`.
Symbolic changes specify scope and operation: `chmod g-w file`, `chmod u+x script.sh`.

### Special Bits
| Bit | Applies To | Effect | Typical Use |
|-----|------------|-------|-------------|
| setuid (s) | Executable file | Runs with file owner's UID | Password change (`passwd`) |
| setgid (s) | Executable / Directory | File: runs with file's group GID; Dir: new files inherit group | Shared project directories |
| sticky (t) | Directory | Only owner/root can remove/rename contained files | `/tmp` safety in multi-user env |

### Ownership & Groups
Files track owner + group. Adjust with `chown user:group file`. Groups allow shared write without granting world access. Combine with setgid on directories to inherit group automatically.

### umask
Defines which permission bits are masked out for new files. Common default `022` -> new files `644`, dirs `755`.

## Why It Matters
Misconfigured permissions lead to accidental data leaks or operational failures (e.g., script not executable, config world-writable). Correct application establishes least privilege, collaborative workflows, and safe automation.

## Scenarios
| Scenario | Desired Outcome | Command(s) |
|----------|-----------------|------------|
| Private key file | Owner read/write only | `chmod 600 id_rsa` |
| Shared dev directory | Team read/write, others none | `chgrp devgrp project && chmod 2770 project` |
| Secure script | Executable by owner & group, not others | `chmod 750 deploy.sh` |
| Public static assets | Read by everyone, write restricted | `chmod 644 index.html` |
| Log directory group collaboration | Files inherit group | `chmod 2775 logs/` |

## Pitfalls & Fixes
| Pitfall | Symptom | Fix |
|---------|---------|-----|
| Using `chmod 777` broadly | World write/executable risk | Use least privilege; prefer group assignments |
| Forgetting execute on scripts | `Permission denied` on run | `chmod +x script.sh` |
| Removing read on directory but expecting listing | `ls` fails | Ensure execute bit set for traversal | 
| Wrong umask causing overly permissive files | New files world-readable when not desired | Adjust in shell profile: `umask 027` |
| setuid on scripts (ignored) | Security misunderstanding | Avoid; setuid applies to binaries, not shell scripts |

## Walkthrough
```bash
mkdir -p ~/lab/mod05 && cd ~/lab/mod05
echo "top secret" > secret.txt
ls -l secret.txt
chmod 600 secret.txt
ls -l secret.txt
groupadd devgrp 2>/dev/null || true
mkdir shared && chgrp devgrp shared && chmod 2775 shared
touch shared/note.txt && ls -l shared/note.txt
umask > umask_start.txt
echo "#!/usr/bin/env bash" > script.sh; echo "echo hi" >> script.sh
chmod 750 script.sh; ls -l script.sh
ls -ld /tmp | awk '{print $1,$3,$4,$9}'
```

## Practice Checklist
1. Create `team/` directory with group inheritance: `mkdir team && chgrp devgrp team && chmod 2775 team`.
2. Set restrictive permission on configuration: `chmod 640 config.ini` and explain why group read might be needed.
3. Compare `ls -l` outputs for `chmod 644` vs `chmod 640` on same file.
4. Adjust umask temporarily: `umask 077`; create file; inspect difference.
5. Demonstrate sticky behavior: create file in `/tmp` and attempt removal by another user (conceptual if single-user).

## Reflection Questions
1. Why is `chmod 777` almost never correct in production?
2. How does setgid on a directory reduce administrative overhead for group collaboration?
3. What security risk does world-writable configuration pose?

Exit Criteria: Explain difference between 775 and 750, apply least-privilege permissions, and configure a shared directory with setgid safely.

Next: Module 06 (Package Management).