# Module 04: File Operations & Text Processing Basics

Goal: Manipulate files/directories, build pipelines, and archive content safely.

## Concept Primer
Text processing lets you transform raw logs and data quickly. Pipelines chain simple tools so each does one job well (UNIX philosophy). Archiving groups files for transport or backup.

### Core Tools Roles
| Tool | Role | Example |
|------|------|--------|
| `grep` | Filter lines by pattern | `grep -i error app.log` |
| `cut` | Extract columns | `cut -d' ' -f1 access.log` |
| `sort` | Order lines | `sort -nr numbers.txt` |
| `uniq` | Collapse duplicates (needs sorted input) | `sort users.txt | uniq -c` |
| `xargs` | Build command from input | `find . -name '*.log' | xargs grep -H ERROR` |
| `tar` | Archive/unarchive | `tar -cvf logs.tar /var/log` |

### Redirection & Pipes
| Symbol | Meaning | Example |
|--------|---------|---------|
| `>` | Overwrite redirect | `echo hi > file` |
| `>>` | Append redirect | `echo hi >> file` |
| `2>` | Redirect stderr | `cmd 2> errors.txt` |
| `|` | Pipe stdout to next command | `grep ERROR app.log | wc -l` |

### Globbing vs Regex
Shell globbing (`*.log`) matches filenames before command runs; regex (used by grep) matches text inside files. Quote regex patterns to prevent shell expansion.

## Why It Matters
Operational tasks (finding hotspots in logs, summarizing access patterns, creating backups) rely on fast composition of these primitives—no bulky external tools needed.

## Safety & Pitfalls
| Pitfall | Symptom | Prevention |
|---------|---------|-----------|
| Using `rm *.log` blindly | Deletes unintended files | Dry-run: `echo rm *.log` |
| Forgetting to sort before `uniq` | Unexpected counts | Always `sort` first |
| Large `xargs` commands exceed line length | Command fails | Use `xargs -n 1` or `-0` with `find -print0` |
| Archiving with relative paths then extracting elsewhere | Disorganized extraction | Use stable root or absolute paths cautiously |

## Walkthrough
```bash
mkdir -p ~/lab/mod04 && cd ~/lab/mod04
seq 1 15 > nums.txt
grep '^[1-9]$' nums.txt > single_digits.txt
sort -nr nums.txt | head -5 > top5.txt
paste nums.txt nums.txt | cut -f1 | sort | uniq -c > counts.txt
tar -cvf bundle.tar nums.txt single_digits.txt top5.txt counts.txt
tar -tvf bundle.tar | head
```

## Practice Checklist
1. Create dataset: `seq 1 50 > data.txt`.
2. Pipeline: count even numbers: `grep -E '^[0-9]*[02468]$' data.txt | wc -l`.
3. Extract first and last 3 numbers using `head` and `tail` then combine: `(head -3 data.txt; tail -3 data.txt) > ends.txt`.
4. Safe removal rehearsal: `echo rm data.txt ends.txt`.
5. Archive plus extract test: `tar -cvf data.tar data.txt` then `mkdir extract && tar -xvf data.tar -C extract`.

## Micro Exercises
1. Replace spaces with commas: `tr ' ' ',' < words.txt > words.csv` (create words.txt first).
2. Find lines longer than 20 chars: `awk 'length($0)>20' words.txt` (optional awk introduction).
3. Use `xargs` to run `wc -l` on found `.txt` files: `find . -name '*.txt' -print0 | xargs -0 wc -l`.

## Reflection Questions
1. When is `xargs` preferred over a `-exec` in `find`? (Performance & aggregated invocation.)
2. Why must input be sorted for meaningful `uniq -c` counts?
3. Risks of extracting tar archives built with absolute paths?

Exit Criteria: You can build a multi-stage pipeline (filter + transform + summarize), safely preview destructive wildcard operations, and create & extract a tar archive.

Next: Module 05 (Permissions).