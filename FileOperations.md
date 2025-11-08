# File Operations

Learn how to create, view, edit, move, delete, and search files. Mastering these basics accelerates everything else in Linux.

## Creating Files & Directories
| Action | Command | Notes |
|--------|---------|-------|
| Empty file | `touch file.txt` | Creates or updates timestamp |
| Directory | `mkdir mydir` | Simple directory |
| Nested dirs | `mkdir -p a/b/c` | Creates parents if missing |
| From output | `echo "Hello" > greet.txt` | Overwrites if exists |
| From here-doc | `cat <<'EOF' > script.sh` ... `EOF` | Multi-line file creation |

## Viewing Files
| Action | Command | Notes |
|--------|---------|-------|
| Print entire | `cat file` | For small files |
| Paginated view | `less file` | Space/PageDown scroll, q quit |
| Start lines | `head -n 20 file` | Default 10 without `-n` |
| End lines | `tail -n 20 file` | Logs tailing |
| Follow growth | `tail -f logfile` | Ctrl+C to stop |
| Numbered lines | `nl file` | Useful for references |
| File type | `file some.bin` | Magic bytes detection |
| Metadata | `stat file` | Timestamps & permissions |

## Editing Files
| Tool | Command | Notes |
|------|---------|-------|
| Nano | `nano file.txt` | Save: Ctrl+O, Exit: Ctrl+X |
| Vim | `vim file.txt` | `i` insert, `Esc :wq` save & quit |
| Sed in-place | `sed -i 's/old/new/g' file.txt` | Quick substitution |
| Append line | `echo "New line" >> file.txt` | Adds to end |

## Copying & Moving
| Action | Command | Notes |
|--------|---------|-------|
| Copy file | `cp a.txt b.txt` | Overwrites target |
| Copy dir | `cp -r dir1 dir2` | Recursive |
| Move/Rename | `mv old new` | Works across paths |
| Safe copy | `cp -i a.txt b.txt` | Prompt before overwrite |
| Preserve attrs | `cp -p a.txt b.txt` | Keep timestamps, mode |

## Deleting Safely
| Action | Command | Notes |
|--------|---------|-------|
| Remove file | `rm file.txt` | Permanent (no trash) |
| Interactive | `rm -i file.txt` | Confirm prompt |
| Remove dir tree | `rm -r dir/` | Recursively delete |
| Force remove | `rm -rf dir/` | Skip prompts (DANGEROUS) |
| Empty dir | `rmdir emptydir` | Only works if empty |

## Searching Files & Content
| Goal | Command | Notes |
|------|---------|-------|
| Find by name | `find . -name "main.py"` | Case-sensitive |
| Case-insensitive | `find . -iname "readme.md"` | Ignore case |
| Find by type | `find . -type f -size +10M` | Size filter |
| Exec action | `find . -name "*.log" -delete` | Deletes matches |
| Grep text | `grep -i "error" app.log` | Case-insensitive |
| Recursive grep | `grep -R "TODO" src/` | Search directory tree |
| Show line numbers | `grep -n "pattern" file` | For references |
| Extended regex | `grep -E "WARN|ERROR" file` | Alternation |

## Redirection & Pipes
| Operator | Meaning | Example |
|----------|---------|---------|
| `>` | Redirect stdout (overwrite) | `ls > listing.txt` |
| `>>` | Redirect stdout (append) | `echo "Line" >> listing.txt` |
| `<` | Redirect stdin from file | `wc -l < file.txt` |
| `2>` | Redirect stderr | `cmd 2> errors.log` |
| `>&` | Redirect both (bash) | `command &> all.log` |
| `|` | Pipe output to another command | `ls -l | grep "^d"` |

## Combining Tools
```bash
# Count unique IPs in a log
cut -d' ' -f1 access.log | sort | uniq -c | sort -nr | head

# Replace text in many files
grep -Rl "oldValue" . | xargs sed -i 's/oldValue/newValue/g'

# Find largest files
find . -type f -printf '%s %p\n' | sort -nr | head
```

## Compression & Archiving
| Task | Command | Notes |
|------|---------|-------|
| Create tar | `tar -cvf archive.tar dir/` | c create, v verbose, f file |
| Extract tar | `tar -xvf archive.tar` | |
| Create gzip tar | `tar -czvf archive.tar.gz dir/` | z gzip |
| Extract gzip tar | `tar -xzvf archive.tar.gz` | |
| List archive | `tar -tvf archive.tar` | |
| Zip | `zip -r archive.zip dir/` | Needs `zip` tool |
| Unzip | `unzip archive.zip` | |

## Permissions Snapshot
See `Permissions.md` for full coverage.
```bash
ls -l file.txt      # rwxr-xr-- style
chmod u+x script.sh # Add execute for user
```

## Safety Practices
- Use `-i` with `rm` until comfortable.
- Double-quote variables in scripts: `rm "$TARGET"` to avoid glob expansion.
- Test destructive find operations without `-delete` first by printing: `find . -name "*.log" -print`.

## Exercises
1. Create `lab` directory; inside it create `app.log` with three lines, then show only the second line.
2. Find all `.sh` files larger than 1 KB.
3. Replace the word `DEBUG` with `INFO` in all `.conf` files.

---
Try now: `mkdir practice && cd practice && echo "Test" > one.txt && ls -l`.
