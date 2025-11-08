# File Operations (Hands-On Guide)

Mastering file manipulation is core to daily Linux use and scripting.

## Creating Files & Directories
| Action | Command | Notes |
|--------|---------|-------|
| Empty file | `touch report.txt` | Updates timestamp if exists |
| From echo | `echo "Hello" > greet.txt` | Overwrites target |
| Append | `echo "Line" >> log.txt` | Adds to end |
| Directory | `mkdir data` | Fails if exists (use `-p`) |
| Nested dirs | `mkdir -p lab/run/output` | Creates full path |

## Viewing & Paging
| Tool | Use | Tips |
|------|-----|------|
| `cat file` | Print whole file | Small files only |
| `less file` | Page through file | /search, n next, q quit |
| `head -n 20 file` | First lines | Default 10 |
| `tail -n 50 file` | Last lines | Use `-f` to follow growth |
| `nl file` | Line numbers | Helpful for referencing |

## Editing
| Editor | Quick Save/Quit | Notes |
|--------|-----------------|-------|
| nano | Ctrl+O (write), Ctrl+X (exit) | Beginner friendly |
| vim | `:w` write, `:q` quit, `i` insert | Modal, powerful |

## Copy / Move / Rename
| Action | Command | Notes |
|--------|---------|-------|
| Copy file | `cp src.txt dst.txt` | Overwrites if exists |
| Copy dir | `cp -r dir1 dir2` | Recurses |
| Preserve attrs | `cp -a src dst` | Keeps timestamps, perms |
| Rename | `mv old.txt new.txt` | Same filesystem only |
| Move | `mv file ~/archive/` | Moves into target dir |
| Multiple | `mv *.log logs/` | Wildcards expand before move |

## Deleting (Be Careful)
| Action | Command | Notes |
|--------|---------|-------|
| Remove file | `rm file.txt` | No recovery by default |
| Interactive | `rm -i important.cfg` | Confirm prompt |
| Recursive | `rm -r olddir` | Prompts per file (unless `-f`) |
| Force recursive | `rm -rf olddir` | DANGEROUS—avoid trailing spaces |
| Empty dir | `rmdir emptydir` | Fails if not empty |

Safety tip: Expand globs first with `echo *.log` before deletion.

## Searching Files & Content
| Task | Command | Notes |
|------|---------|-------|
| Find by name | `find . -name "config.yaml"` | Case-sensitive unless using `-iname` |
| Find directories | `find /var -type d -maxdepth 1` | Restrict depth |
| Find large files | `find /var -type f -size +100M` | Size filters: `+`, `-` |
| Grep text | `grep -i "error" app.log` | `-i` ignore case |
| Grep recursive | `grep -R "TODO" src/` | Searches directory tree |
| Grep count | `grep -c "ERROR" app.log` | Number of matching lines |

Combine find + exec:
```bash
find . -name "*.tmp" -exec rm {} \;
```
Or batch with xargs:
```bash
find . -name "*.tmp" -print0 | xargs -0 rm
```

## Redirection & Pipes
| Symbol | Meaning | Example |
|--------|---------|---------|
| `>` | Redirect stdout (overwrite) | `ls > listing.txt` |
| `>>` | Redirect stdout (append) | `date >> log.txt` |
| `<` | Redirect stdin from file | `sort < unsorted.txt` |
| `2>` | Redirect stderr | `cmd 2> errors.txt` |
| `&>` | Redirect both stdout & stderr | `script &> all.log` |
| `|` | Pipe stdout to next command | `ps aux | grep nginx` |
| `tee` | Split output to file + screen | `ls | tee files.txt` |

## Globbing (Wildcard Expansion)
| Pattern | Matches |
|---------|---------|
| `*` | Any string (including empty) |
| `?` | Single character |
| `[abc]` | One of a, b, or c |
| `[A-Z]` | Any uppercase letter range |
| `{jpg,png}` | Either jpg or png (brace expansion) |

Examples:
```bash
ls *.log
cp image.{jpg,png} backup/
mv report_2023??.csv archive/
```

## File Metadata
| Command | Purpose |
|---------|---------|
| `stat file.txt` | Detailed timestamps & mode |
| `file bin/app` | Guess file type via magic bytes |
| `ls -l` | Basic ownership & permissions |
| `ls -lh` | Human-readable sizes |
| `touch -d "2023-12-31" file` | Set modification time |

## Counting & Summaries
| Task | Command |
|------|---------|
| Lines | `wc -l file` |
| Words | `wc -w file` |
| Characters | `wc -m file` |
| Unique lines | `sort file | uniq` |
| Frequency | `sort file | uniq -c | sort -nr` |

## Disk Usage
| Task | Command | Notes |
|------|---------|-------|
| FS usage | `df -h` | per filesystem |
| Dir size | `du -sh dir` | summary human |
| All items | `du -sh *` | size of each entry |

Identify top space consumers:
```bash
du -sh * | sort -h | tail
```

## Backups & Archiving
| Action | Command | Notes |
|--------|---------|-------|
| Create tar | `tar -cvf data.tar data/` | -c create, -v verbose, -f file |
| Extract tar | `tar -xvf data.tar` | -x extract |
| Compress gzip | `tar -czvf data.tar.gz data/` | -z gzip |
| List archive | `tar -tvf data.tar.gz | head` | -t list |
| Extract single | `tar -xvf data.tar.gz data/file.txt` | Specify path |

## Comparing Files
| Task | Command | Notes |
|------|---------|-------|
| Diff text | `diff old.cfg new.cfg` | Shows line changes |
| Colored diff | `diff --color old new` | Easier to read |
| Unified diff | `diff -u old new` | Patch-friendly |

## Safe Editing Strategy
1. Copy original: `cp config.cfg config.cfg.bak`.
2. Edit backup if large change.
3. Use version control for critical configs.

## Example Workflow
```bash
mkdir lab && cd lab
echo "alpha" > a.txt
echo "beta" > b.txt
cat a.txt b.txt > combined.txt
grep -n "beta" combined.txt
wc -l combined.txt
tar -czvf archive.tgz combined.txt
rm combined.txt
tar -xvf archive.tgz
```

## Exercises
1. Create `project/{logs,data}` and generate three log files with timestamps appended.
2. Find all `.log` files larger than 10K under `project`.
3. Combine all log files into `all.log` retaining original lines.
4. Count occurrences of the word `ERROR` in `all.log`.
5. Produce a compressed archive of `project` excluding `all.log`.

## Suggested Answers (Sketch)
```bash
mkdir -p project/{logs,data}
for i in 1 2 3; do echo "Run $i $(date +%F-%T)" > project/logs/run$i.log; done
find project -type f -name "*.log" -size +10k
cat project/logs/*.log > all.log
grep -c "ERROR" all.log
tar --exclude=all.log -czvf project.tgz project/
```

---
Next: Deep dive into permissions in `scripting/Permissions.md`.