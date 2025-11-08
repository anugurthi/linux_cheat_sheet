# Running Scripts

Scripts automate tasks. They are plain text files interpreted by a program (shell, Python, etc.). Key concepts: shebang line, execute permission, environment.

## Shebang (#!)
First line that tells the system which interpreter to use.
```bash
#!/bin/bash      # Bash shell script
#!/usr/bin/env bash  # Portable bash lookup in PATH
#!/usr/bin/env python3  # Python script
#!/usr/bin/env node     # Node.js script
```
If absent, the script may be run by the current shell when invoked with `bash script.sh`.

## Making a Script Executable
```bash
echo -e "#!/bin/bash\necho 'Hello'" > hello.sh
chmod +x hello.sh
./hello.sh
```

## Running Without Execute Bit
```bash
bash hello.sh         # Pass file explicitly to bash
sh hello.sh           # POSIX sh (may differ from bash)
python3 app.py        # Run Python file
node app.js           # Run Node file
```

## Variables & Arguments
Script `show.sh`:
```bash
#!/bin/bash
NAME="Alice"
echo "Name: $NAME"
echo "First arg: $1"
echo "All args: $@"
```
Run:
```bash
./show.sh 123 xyz
```
Output uses `$1` (first argument) and `$@` (all arguments).

## Exit Codes
Every process returns an integer (0 success, non-zero error). Check last exit code:
```bash
echo $?    # Prints exit code of previous command
```
Set exit code in script:
```bash
exit 2
```

## Conditionals / Loops (Bash Example)
```bash
#!/bin/bash
if [ -f "$1" ]; then
  echo "File exists"
else
  echo "Missing file"; exit 1
fi
for f in *.txt; do
  echo "Processing $f"
done
```

## Reading Input
```bash
read -p "Enter name: " NAME
echo "Hello $NAME"
```

## Environment Variables
Show environment:
```bash
env | head
printenv PATH
```
Export a variable so child processes see it:
```bash
export MODE=debug
python3 run.py
```
Inside Python:
```python
import os
print(os.environ.get("MODE"))
```

## Passing Environment Inline
```bash
MODE=prod ./deploy.sh
```
`deploy.sh` can read `$MODE`.

## Script Safety Patterns
| Goal | Practice |
|------|----------|
| Stop on errors | `set -e` (exit if any command fails) |
| Treat unset vars as errors | `set -u` |
| Safer pipes | `set -o pipefail` |
| Combine | `set -euo pipefail` |

Example header:
```bash
#!/usr/bin/env bash
set -euo pipefail
IFS=$'\n\t'
```

## Logging
```bash
log() { echo "[$(date +%H:%M:%S)] $*"; }
log "Starting"
```
Redirect all output:
```bash
./task.sh > task.out 2>&1
```

## Cron (Scheduled Scripts)
Edit crontab:
```bash
crontab -e
```
Entry example (run every hour):
```
0 * * * * /home/user/scripts/cleanup.sh
```
Ensure script has absolute paths and proper permissions.

## Interpreter Discovery
`which bash` or `command -v bash` shows path.
Use `/usr/bin/env` for portability across systems.

## Shebang vs Manual Invocation
| Situation | Use Shebang? | Run Command |
|-----------|--------------|-------------|
| Portable shell script | Yes | `./script.sh` |
| Quick one-off | Optional | `bash script.sh` |
| Python script distribution | Yes | `./tool.py` if executable |
| Library module | No | `python3 -m package.module` |

## Debugging Scripts
| Method | Command |
|--------|---------|
| Trace lines (bash) | `bash -x script.sh` |
| Enable tracing inside | `set -x` / `set +x` |
| Check shell syntax | `bash -n script.sh` |
| ShellCheck (lint) | `shellcheck script.sh` (install separately) |

## Example: Backup Script
```bash
#!/usr/bin/env bash
set -euo pipefail
SRC="$1"; DEST="$2"
if [ ! -d "$SRC" ]; then
  echo "Source missing" >&2; exit 1
fi
mkdir -p "$DEST"
DATE=$(date +%Y%m%d_%H%M%S)
tar -czf "$DEST/backup_${DATE}.tar.gz" -C "$SRC" .
echo "Backup created at $DEST/backup_${DATE}.tar.gz"
```
Run:
```bash
./backup.sh /var/log /tmp/log_backups
```

## Practice
1. Write a script that prints the number of `.txt` files in current directory.
2. Add error handling if no files found.
3. Convert it to exit with code 2 on error.

---
Try: Create `hello.sh` with a shebang, echo your user, and run it.
