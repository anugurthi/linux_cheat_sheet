# Mastering `ls`

The `ls` command lists directory contents. Combine options to tailor output for scripts, inspection, or troubleshooting.

## Core Forms
| Command | Purpose |
|---------|---------|
| `ls` | Names only |
| `ls -l` | Long listing (permissions, owner, size, timestamp) |
| `ls -lh` | Long listing with human-readable sizes |
| `ls -a` | Include hidden files (starting with `.`) |
| `ls -la` | Long + hidden |
| `ls -1` | One entry per line (script-friendly) |

## Common Options
| Option | Meaning |
|--------|---------|
| `-l` | Long format |
| `-h` | Human-readable sizes (combine with `-l`) |
| `-a` | All files including `.` and `..` |
| `-A` | Almost all (excludes `.` and `..`) |
| `-F` | Classify (`/` dirs, `*` executables, `@` symlink) |
| `-r` | Reverse sort order |
| `-t` | Sort by modification time |
| `-S` | Sort by size |
| `-R` | Recursive descent |
| `-i` | Show inode numbers |
| `-n` | Numeric user/group IDs |
| `--color=auto` | Color output (default on many distros) |
| `--group-directories-first` | GNU ls: directories before files |

## Combined Examples
| Command | Effect |
|---------|--------|
| `ls -ltr` | Long, time sort, reverse (oldest first) |
| `ls -lSh` | Long, size sort, human sizes |
| `ls -laF` | Long, hidden, classify indicators |
| `ls -R` | Recursively list subdirectories |
| `ls -l --group-directories-first` | Group directories at top |

## Filtering & Grep Tricks
| Pattern | Purpose |
|---------|---------|
| `ls -l | grep "^d"` | Show only directories |
| `ls -l *.log` | Long list matching pattern (expanded by shell) |
| `ls -ld */` | List directories themselves, not their contents |

## Inodes & Hard Links
| Command | Description |
|---------|-------------|
| `ls -li file` | Show inode number |
| `ls -li other` | Compare inode for hard link detection |

## Human vs Raw Sizes
| Command | Description |
|---------|-------------|
| `ls -l file` | Raw byte count |
| `ls -lh file` | Human readable size |

## Recursion Caution
Recursive listing of `/` or large trees can produce massive output. Prefer `tree -L depth` (after installing `tree`) when you need structured hierarchy.

## Sample Workflow
```bash
ls -l              # Inspect permissions
ls -lh *.tar.gz    # Archive sizes
ls -ltr            # Most recently changed files at bottom
ls -lSh            # Largest files first
```

## Useful Aliases
Add to your shell config (`~/.bashrc` or `~/.zshrc`):
```bash
alias ll='ls -lh'
alias la='ls -lah'
alias lS='ls -lSh'
```
Reload shell: `source ~/.bashrc` or restart session.

## Practice Task
List all directories sorted by size:
```bash
ls -l | grep "^d" | awk '{print $9}' | xargs -I{} du -sh {} | sort -h
```

---
Explore: Run `ls -laF` in your home directory and note suffixes.
