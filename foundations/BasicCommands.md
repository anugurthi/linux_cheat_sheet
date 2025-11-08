# Essential Linux Commands (Beginner Guide)

This guide groups foundational commands by task. Practice them to build confidence.

## System Identity & Info
| Command | Description |
|---------|-------------|
| `hostname` | Show current host name |
| `hostnamectl` | Show detailed system info (OS, kernel) |
| `hostnamectl set-hostname newname` | Change host name (needs sudo) |
| `whoami` | Current user name |
| `id` | User ID, group ID, supplementary groups |
| `uname -r` | Kernel version |
| `uname -a` | All kernel/system info |

## IP / Network Basics
| Command | Description |
|---------|-------------|
| `ip a` | Show IP addresses (modern tool) |
| `ifconfig` | Legacy network interface display (needs net-tools) |
| `ip route` | Show routing table |
| `ping host` | Test connectivity (Ctrl+C to stop) |

## Navigation & Directories
| Command | Description |
|---------|-------------|
| `pwd` | Print working directory |
| `ls`, `ls -lh`, `ls -a` | List files basic / detailed / include hidden |
| `cd /` | Go to root |
| `cd ~` | Go to home directory |
| `cd -` | Previous directory |
| `cd ../..` | Up two levels |
| `mkdir name` | Create directory |
| `mkdir -p a/b/c` | Create nested directories |
| `rmdir emptydir` | Remove empty directory |
| `rm -r dir` | Remove directory tree (careful!) |

## File Creation & Viewing
| Command | Description |
|---------|-------------|
| `touch file` | Create empty file or update timestamp |
| `cat file` | Print file contents |
| `less file` | Paginated view (space to scroll, q to quit) |
| `head -n 10 file` | First 10 lines |
| `tail -n 20 file` | Last 20 lines |
| `tail -f log` | Follow growth of a file |

## Editing Files
| Command | Description |
|---------|-------------|
| `nano file` | Simple editor (Ctrl+O save, Ctrl+X exit) |
| `vim file` | Powerful modal editor (i insert, :w write, :q quit) |

## Copy / Move / Delete
| Command | Description |
|---------|-------------|
| `cp src dest` | Copy file |
| `cp -r dir1 dir2` | Copy directory recursively |
| `mv old new` | Rename or move |
| `mv file /target/` | Move into directory |
| `rm file` | Remove file |
| `rm -i file` | Prompt before delete |
| `rm -rf dir` | Force remove tree (DANGEROUS) |

## Users & Sessions
| Command | Description |
|---------|-------------|
| `su - otheruser` | Switch user (requires password) |
| `exit` / `logout` | Leave subshell / session |
| `who` | Logged-in users |
| `last` | Recent login history |

## Permissions (Preview)
| Command | Description |
|---------|-------------|
| `ls -l file` | Permission string (e.g. rwxr-xr--) |
| `chmod u+x script.sh` | Add execute for user |
| `chown user:group file` | Change owner and group (sudo) |

## Searching
| Command | Description |
|---------|-------------|
| `find . -name "main.py"` | Find file by name recursively |
| `grep -i "error" logfile` | Case-insensitive search in file |
| `grep -R "TODO" src/` | Recursive search in directory |

## Monitoring & Resources
| Command | Description |
|---------|-------------|
| `top` | Interactive process viewer (q quit) |
| `ps aux | head` | Snapshot of all processes |
| `free -h` | Memory usage |
| `df -h` | Disk usage per filesystem |
| `du -sh *` | Size of each item in current directory |

## Package Management Examples
RPM-based (RHEL, CentOS, Fedora):
```bash
sudo dnf install htop
rpm -qi bash
```
Debian-based (Ubuntu, Debian):
```bash
sudo apt update
sudo apt install htop
dpkg -l | grep bash
```

## Shortcuts & Symbols
| Symbol | Meaning |
|--------|---------|
| `~` | Home directory |
| `.` | Current directory |
| `..` | Parent directory |
| `*` | Wildcard pattern |
| `|` | Pipe output to next command |
| `>` | Redirect (overwrite) |
| `>>` | Redirect (append) |

## Putting It Together (Example Session)
```bash
hostname
pwd
mkdir demo && cd demo
touch app.log script.sh
ls -lh
echo "Hello" > app.log
tail app.log
chmod u+x script.sh
du -sh .
```

## Next Steps
Move to detailed topics: `scripting/FileOperations.md`, `scripting/Permissions.md`, `packaging/PackageManagement.md`, `scripting/RunningScripts.md`.

---
Practice: Create a directory `lab`, add two files, list them sorted by size.