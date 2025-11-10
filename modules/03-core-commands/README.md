# Module 03: Core Commands

Goal: Build muscle memory for everyday navigation, viewing, editing, copying, searching, and system inspection.

## Concept Primer
Core commands form a "terminal survival kit". Each family answers a diagnostic question:
| Family | Question Answered | Examples |
|--------|-------------------|----------|
| Navigation | Where am I? What's here? | `pwd`, `cd`, `ls` |
| File viewing | What's inside? | `cat`, `less`, `head`, `tail` |
| Editing | How do I change it? | `nano`, `vim` |
| Management | How do I move/copy/remove? | `cp`, `mv`, `rm` |
| Search | Where is X? Does text contain Y? | `find`, `grep` |
| System info | What's the system state? | `uname`, `hostnamectl`, `df`, `free` |
| Processes | What's running / consuming resources? | `ps`, `top` |

### Navigation Nuances
`pwd` shows current path; `cd -` toggles previous directory; `ls -lah` exposes hidden files & human sizes. Use `ls -l` before destructive actions to verify targets.

### Viewing vs Editing
Prefer non-editing viewers (`less`, `head`, `tail -f`) for inspection; only open editors when you intend to change content to avoid accidental edits.

### Search Strategies
`find` locates files by name/size/type; `grep` inspects contents. Combine them:
```bash
find . -type f -name '*.log' -exec grep -H "ERROR" {} +
```
This prints filenames alongside matching lines.

### Process & Resource Snapshot
`ps aux | head` for a quick listing; `top` (or `htop` if installed) for dynamic view. Exit `top` with `q`.

## Why It Matters
Efficient terminal usage reduces time-to-diagnosis. Recognizing patterns (e.g., a full disk from `df -h`) directs next actions (check `/var/log` growth, prune archives). Mastery allows safe automation later.

## Safety & Pitfalls
| Pitfall | Symptom | Safer Approach |
|---------|---------|----------------|
| Overuse of `sudo rm -r` | Accidental destructive deletions | Use `rm -ri` and stage test files in `~/lab` |
| Editing binary with text editor | Garbled output | Check type with `file` before editing |
| Grep large trees blindly | Slow scans / noise | Narrow scope: `grep -R "pattern" path --include='*.log'` |
| Forgetting quotes around patterns | Shell glob unintended expansion | Quote patterns: `grep "ERROR*"` |

## Progressive Practice Workflow
```bash
mkdir -p ~/lab/mod03 && cd ~/lab/mod03
echo "Alpha" > a.txt; echo "Beta" > b.txt; echo "Error: something" > app.log
pwd
ls -lah
head -n 1 app.log
tail -f app.log & sleep 1; kill %1
grep -i error app.log
cp a.txt copy_a.txt
mv b.txt renamed_b.txt
find . -type f -name '*.txt'
df -h | grep -E 'Filesystem|/dev/'
free -h
ps aux | head
```

## Micro Exercises
1. Add three lines to `app.log` and re-run `grep -n error app.log` (note line numbers).
2. Create subdir `data/` with files; list only directories in current path: `ls -l | grep '^d'`.
3. Use `find . -maxdepth 1 -type f -size -1k` to list small files.
4. Use `grep -E "(Alpha|Beta)" a.txt copy_a.txt` to match multiple alternatives.
5. Show which commands are builtins: `type cd`, `type echo`.

## Reflection Questions
1. When would you favor `tail -f` over `less`? (Live growth vs static inspection.)
2. How does `find -exec ... +` differ from `-exec ... \;`? (Aggregates args vs per-file invocation.)
3. Why use `head`/`tail` for large files instead of opening them fully?

Exit Criteria: You can perform safe file create/move/delete, combine `find` + `grep`, and interpret basic system resource snapshots.

Next: Module 04 (File Operations) for deeper pipelines and text manipulation.