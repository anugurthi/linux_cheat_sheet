# Module 07: Bash Scripting Fundamentals

Goal: Write safe, maintainable shell scripts that are predictable, debuggable, and integrate cleanly with scheduling (cron or systemd timers).

---
## Concept Primer

| Concept | Essence | Mental Model |
|---------|---------|--------------|
| Shebang (`#!`) | Tells kernel which interpreter to exec | Like a file-level contract: `/bin/bash`, `/usr/bin/env bash` |
| Execution Permission | Marks file runnable by user/group/others | Gate before kernel loads interpreter |
| Expansion Order | Brace → Tilde → Parameter → Command Substitution → Arithmetic → Globbing → Word Splitting | A pipeline of transformations; quoting stops parts of the pipeline |
| Quoting | `"..."` preserves spaces; `'...'` literal; `$'...'` allows escapes | Choose the minimal quoting that protects intent |
| Safety Flags | `set -euo pipefail` | Convert silent failure into early, explicit failure |
| Variables & Scope | Shell is dynamic; functions inherit unless locally declared (`local`) | Treat globals as shared state—minimize |
| Parameters | `$1`, `$@`, `$*`, `$#`, `$?`, `$_` | API surface of your script |
| Functions | Reusable blocks returning status (0–255) | Prefer verbs: `backup_db()`, `log_info()` |
| Exit Codes | 0 success; non‑zero = category of failure | Communicate outcome to upstream automation |
| Scheduling | `cron`, `systemd` timers | Transport layer for your repeatable script |

### Why Use Shell (and When Not To)
Good for: glue tasks, orchestration of existing CLI tools, lightweight automation.
Not ideal for: heavy data structures, complex error handling trees—consider Python/Go there.

---
## Why It Matters
Poorly written scripts create brittle CI/CD steps, hide failures, or delete data. Discipline in quoting, error checking, and logging produces reproducibility and trust—critical attributes when scripts later become cron jobs or systemd services.

---
## Core Building Blocks

| Building Block | Pattern | Example | Notes |
|----------------|---------|---------|-------|
| Shebang | `#!/usr/bin/env bash` | Top line | Portable to differing bash paths |
| Strict Mode | `set -euo pipefail` | After shebang | Add `IFS=$'\n\t'` optionally |
| Usage Guard | `[[ $# -lt 1 ]] && { echo "Usage: $0 <arg>" >&2; exit 64; }` | Input validation | Exit code 64 (EX_USAGE) communicates misuse |
| Readonly Vars | `readonly VERSION="1.0.0"` | Constants | Prevent accidental reassignment |
| Temp Files | `tmp=$(mktemp)`; `trap 'rm -f "$tmp"' EXIT` | Resource lifecycle | Always register cleanup early |
| Logging | `log(){ printf '%s [%s] %s\n' "$(date -Iseconds)" "$1" "$2"; }` | `log INFO "Starting"` | Provide severity dimension |
| Functions | `do_backup(){ rsync ...; }` | Verb names | Keep them < 50 lines |
| Return vs Output | `return 1` vs `echo` | Separation of status vs data | Enables piping safe data |

---
## Execution Model & Parameter Passing

| Token | Meaning |
|-------|---------|
| `$@` | All parameters as separate words (use in loops) |
| `$*` | All parameters as a single word (rarely what you want) |
| `"$@"` | Perfectly preserves argument boundaries |
| `$?` | Status of last command |
| `$$` | PID of current shell |
| `$!` | PID of last background job |

Prefer: `for arg in "$@"; do ...; done`

Subshell vs Current Shell:
`( cd /tmp && do_thing )` leaves caller's PWD unchanged.
`cd /tmp && do_thing` mutates current shell.

---
## Safety & Defensive Patterns
1. Immediately enable strict mode: `set -euo pipefail`.
2. Use `trap` for cleanup and debugging: `trap 'echo "ERR at $BASH_SOURCE:$LINENO" >&2' ERR`.
3. Quote all variable expansions unless you explicitly want word splitting or globbing.
4. Validate external dependencies: `command -v rsync >/dev/null || { echo "rsync missing" >&2; exit 127; }`.
5. Fail fast on partial pipelines: rely on `pipefail` and test statuses.

---
## Scheduling: cron vs systemd Timers

| Aspect | cron | systemd timer |
|--------|------|---------------|
| Granularity | Minute minimum | Sub-minute (OnBootSec=, OnUnitActiveSec=) |
| Environment | Sparse (minimal PATH) | Controlled via unit (`Environment=`) |
| Logging | Mail or custom redirect | journalctl integration |
| Dependency Ordering | None | `After=`, `Requires=` relationships |
| Randomization | Manual scripting | `RandomizedDelaySec=` built-in |

Rule of Thumb: Use cron for simple, legacy single-line tasks; systemd timers for anything needing dependencies, logging, or jitter.

---
## Logging Strategies
Lightweight: `echo` with timestamps.
Structured: `log LEVEL MESSAGE` function.
File + Rotate: Use systemd service + journal or redirect: `exec >>"$LOGFILE" 2>&1` at script start.

Ensure logs include: timestamp, severity, context (function or PID), message.

---
## Common Pitfalls & Fixes

| Pitfall | Symptom | Fix |
|---------|---------|-----|
| Unquoted `$@` | Words merge or split unexpectedly | Always use `"$@"` |
| Globbing data | Filenames with `*` expand | Quote variable or `set -f` if needed |
| Silent pipeline failure | Early stage failed but overall exit 0 | Use `set -o pipefail` |
| Cron PATH issues | Script works manually, fails in cron | Set explicit PATH inside script |
| Partial writes | Overwrite target mid-copy | Write to temp then atomic `mv` |
| Race on temp file | Two runs collide | Use `mktemp` (random unique name) |

---
## Guided Walkthrough

We will build a resilient rotating backup script.

1. Skeleton
```bash
#!/usr/bin/env bash
set -euo pipefail
IFS=$'\n\t'
trap 'echo "[ERR] $BASH_SOURCE:$LINENO status=$?" >&2' ERR
readonly SRC_DIR="${1:-$HOME/projects}" # default if not provided
readonly DEST_BASE="${2:-$HOME/backups}" 
[[ -d "$SRC_DIR" ]] || { echo "Source missing: $SRC_DIR" >&2; exit 70; }
mkdir -p "$DEST_BASE"
``` 

2. Logging & Dependency Checks
```bash
log(){ printf '%s [%s] %s\n' "$(date -Iseconds)" "$1" "$2"; }
for dep in rsync gzip; do command -v "$dep" >/dev/null || { echo "Missing $dep" >&2; exit 127; }; done
```

3. Core Function
```bash
do_backup(){
	local ts target tmp
	ts=$(date +%Y%m%d-%H%M%S)
	target="${DEST_BASE}/backup-${ts}.tar.gz"
	tmp=$(mktemp)
	log INFO "Creating archive"
	tar -C "$SRC_DIR" -cf - . | gzip -9 > "$tmp"
	mv "$tmp" "$target"
	log INFO "Created $target"
}
```

4. Retention (keep last 7)
```bash
prune(){
	ls -1t "$DEST_BASE"/backup-*.tar.gz 2>/dev/null | tail -n +8 | while read -r old; do
		log INFO "Pruning $old"; rm -f -- "$old"
	done
}
```

5. Orchestration & Exit
```bash
main(){
	do_backup
	prune
	log INFO "Backup cycle complete"
}
main "$@"
```

Make executable: `chmod +x backup.sh`.

6. Dry Run Test
Run with synthetic source: create test directory with a few files; invoke script and confirm archive & pruning.

7. Scheduling
Cron (daily 02:15): `15 2 * * * /home/USER/bin/backup.sh >>/home/USER/log/backup.log 2>&1`

systemd timer (example):
`~/.config/systemd/user/backup.service`
```
[Unit]
Description=User Backup

[Service]
Type=oneshot
ExecStart=%h/bin/backup.sh
```
`~/.config/systemd/user/backup.timer`
```
[Unit]
Description=Run backup every 6 hours

[Timer]
OnBootSec=5m
OnUnitActiveSec=6h
RandomizedDelaySec=5m
Unit=backup.service

[Install]
WantedBy=timers.target
```
Enable: `systemctl --user enable --now backup.timer` then inspect: `systemctl --user list-timers`.

---
## Practice Checklist
Foundational:
1. Write `hello.sh` that prints a greeting and argument count.
2. Modify it to fail gracefully if no arguments.

Intermediate:
3. Add logging function with severity.
4. Add a function that validates a directory parameter and returns code 3 on failure.
5. Create a script that downloads a URL list concurrently (background jobs) and waits.

Advanced:
6. Convert backup script to accept `--retain N` flag (default 7).
7. Add a `--dry-run` mode that prints actions only.
8. Write a timer unit that runs every hour but randomizes by up to 10 minutes.

Stretch:
9. Add checksum verification after archive creation.
10. Emit JSON log line (structured) for each backup event.

---
## Reflection Questions
1. Which safety flag saved you from a silent bug today? How?
2. Did any variable expansion behave unexpectedly until you added quotes? Describe.
3. At what point would you re‑implement a script in another language? Why?
4. How can you make your script idempotent?
5. What observability signal (exit code, log, metric) would help future debugging most?

---
## Exit Criteria
You can:
| Capability | Demonstrated By |
|------------|-----------------|
| Safe start | Shebang + strict mode + traps |
| Parameter handling | Correct use of `"$@"`, validation & usage message |
| Functions & exits | Modular functions with meaningful status codes |
| Logging | Consistent timestamped lines or journal integration |
| Scheduling | Working cron entry OR systemd timer verified with `list-timers` |
| Cleanup | Temporary files removed and retention policy enforced |

---
Next: Module 08 (Services & Logs) → Move from ad‑hoc script execution to managed long‑running units & structured log interrogation.