> ← Back to [README.md](./README.md) — Text processing utilities (grep, awk, sed, sort, etc.)

# 🐧 Linux Core Utils — File & System Cheat Sheet

This document covers file operations, permissions, archiving, process management, and network transfer utilities: `find`, `xargs`, `ls`, `cp`, `mv`, `rm`, `mkdir`, `chmod`, `chown`, `ln`, `df`, `du`, `ps`, `kill`, `tar`, `rsync`, and `curl`.

[![GNU Coreutils](https://img.shields.io/badge/GNU-coreutils%208.x-blue)](https://www.gnu.org/software/coreutils/)
[![License](https://img.shields.io/badge/license-MIT-green)](../LICENSE)

---

## Table of Contents

- [Installation](#-installation)
- [Core Concepts](#-core-concepts)
- [Quick Reference](#-quick-reference)
  - [find operations](#find-operations)
  - [chmod quick reference](#chmod-quick-reference)
- [Common Commands](#-common-commands)
  - [find](#find)
  - [xargs](#xargs)
  - [File Operations](#file-operations-cp-mv-rm-mkdir-ln)
  - [File Permissions](#file-permissions-chmod-chown)
  - [Disk Usage](#disk-usage-df-du)
  - [Process Management](#process-management-ps-kill)
  - [tar](#tar--archiving)
  - [rsync](#rsync--efficient-file-sync)
- [Real-World Examples](#-real-world-examples)
- [Pro Tips](#-pro-tips)
- [Resources](#-resources)

---

## 📦 Installation

These tools are preinstalled on all Linux and macOS systems. `rsync` may need installation on minimal setups:

```bash
# macOS
brew install rsync findutils coreutils

# Ubuntu/Debian
sudo apt install rsync
```

---

## 🧠 Core Concepts

The Unix filesystem is a tree rooted at `/`. Everything is a file — directories, devices, sockets, pipes, and regular files all live in the same namespace. Permissions control who can read, write, or execute each file, and are split into three categories: the file's owner (user), a group, and everyone else (others).

Understanding these concepts is the foundation for everything in this sheet: `find` traverses the tree, `chmod`/`chown` manage ownership and permissions, `tar` and `rsync` move trees around, and `ps`/`kill` manage running processes.

---

## ⚡ Quick Reference

### find operations

| Command | Description |
| ------- | ----------- |
| `find . -name "*.log"` | Find by name pattern |
| `find . -type f` | Files only |
| `find . -type d` | Directories only |
| `find . -size +10M` | Larger than 10MB |
| `find . -mtime -7` | Modified in last 7 days |
| `find . -name "*.tmp" -delete` | Find and delete |
| `find . -exec command {} \;` | Run command on each result |

### chmod quick reference

| Octal | Symbolic | Meaning |
| ----- | -------- | ------- |
| `755` | `rwxr-xr-x` | Owner: full; Group/Other: read+execute |
| `644` | `rw-r--r--` | Owner: read+write; Group/Other: read |
| `600` | `rw-------` | Owner only: read+write (private files) |
| `700` | `rwx------` | Owner only: full (private directories) |
| `777` | `rwxrwxrwx` | Everyone: full ⚠️ |

---

## 🔧 Common Commands

### find

find recursively searches the filesystem based on name, type, size, time, and more.

```bash
# Find by name
find /var/log -name "*.log"
find . -name "*.go" -not -path "*/vendor/*"
find . -iname "readme.md"                       # case-insensitive

# Find by type
find . -type f          # files only
find . -type d          # directories only
find . -type l          # symlinks only

# Find by size
find . -size +10M       # larger than 10MB
find . -size -1k        # smaller than 1KB
find . -size +1M -size -100M  # between 1MB and 100MB

# Find by modification time
find . -mtime -7        # modified in last 7 days
find . -mtime +30       # modified more than 30 days ago
find . -newer reference.txt   # newer than reference.txt

# Execute commands on results
find . -name "*.tmp" -delete
find . -name "*.go" -exec gofmt -w {} \;
find /tmp -type f -mtime +7 -delete             # clean up old temp files

# Find and count
find ./src -name "*.js" | wc -l
```

**Key flags:**

| Flag | Description |
| ---- | ----------- |
| `-name` | Match filename (case-sensitive) |
| `-iname` | Match filename (case-insensitive) |
| `-type f/d/l` | File / directory / symlink |
| `-size +N[M/k]` | Larger than N megabytes/kilobytes |
| `-mtime -N` | Modified within N days |
| `-newer FILE` | Modified more recently than FILE |
| `-not` / `!` | Negate the following test |
| `-exec CMD {} \;` | Run CMD for each result |
| `-delete` | Delete matching files |

### xargs

xargs builds and executes commands using lines from stdin as arguments.

```bash
# Basic usage
find . -name "*.txt" | xargs cat
echo "one two three" | xargs -n1 echo       # one arg per line (-n1)

# Parallel execution (-P = number of processes)
find . -name "*.jpg" | xargs -P4 -I{} convert {} {}.png

# Safe handling of filenames with spaces (-print0 + -0)
find . -name "*.txt" -print0 | xargs -0 grep "pattern"

# Build command with placeholder
cat servers.txt | xargs -I{} ssh {} "uptime"
```

> ⚠️ **Always use `-print0 | xargs -0` when filenames may contain spaces.** Plain `| xargs` breaks on filenames with spaces, newlines, or special characters.

### File Operations (cp, mv, rm, mkdir, ln)

```bash
# cp — copy
cp file.txt backup.txt
cp -r ./src ./src-backup           # recursive (directory)
cp -p file.txt dest/               # preserve timestamps and permissions
cp -u src/ dest/ -r                # only copy if source is newer

# mv — move / rename
mv old-name.txt new-name.txt
mv ./src/*.log ./logs/             # move multiple files

# rm — remove
rm file.txt
rm -r ./build/                     # recursive directory removal # ⚠️
rm -rf ./dist/ ./node_modules/     # force recursive — no confirmation # ⚠️

# mkdir — make directory
mkdir logs
mkdir -p app/src/components        # create parent directories as needed

# ln — links
ln -s /opt/app/current /usr/local/bin/app   # create symlink
ls -la /usr/local/bin/app          # verify symlink target
```

### File Permissions (chmod, chown)

Every file has three permission sets: user (owner), group, and others. Each set has read (r=4), write (w=2), and execute (x=1) bits. Permissions are expressed as an octal number (e.g., `755`) or symbolic notation (e.g., `rwxr-xr-x`).

**Permission bit reference:**

| Octal | Binary | Symbolic | Meaning |
| ----- | ------ | -------- | ------- |
| `7` | `111` | `rwx` | read + write + execute |
| `6` | `110` | `rw-` | read + write |
| `5` | `101` | `r-x` | read + execute |
| `4` | `100` | `r--` | read only |
| `0` | `000` | `---` | no permissions |

```bash
# chmod — change permissions
chmod +x deploy.sh                  # add execute for all
chmod u+x,g-w deploy.sh             # user +exec, group -write
chmod go= secret.txt                # remove all perms from group and others
chmod 755 script.sh                 # rwxr-xr-x (standard for scripts)
chmod 644 config.txt                # rw-r--r-- (standard for config files)
chmod 600 ~/.ssh/id_ed25519         # rw------- (private key — required by SSH)
chmod 700 ~/.ssh                    # rwx------ (SSH dir — required by SSH)
chmod -R 755 ./public/              # recursive

# chown — change owner
chown alice file.txt
chown alice:developers file.txt     # owner:group
chown -R alice:developers ./project # recursive
sudo chown root:root /usr/local/bin/app

# View permissions
ls -la file.txt
stat file.txt                       # detailed metadata
```

### Disk Usage (df, du)

```bash
# df — disk space (filesystem level)
df -h                               # all filesystems, human-readable
df -h /home                         # specific mount point
df -i                               # inode usage (run out of inodes = no new files)

# du — disk usage (directory/file level)
du -sh ./project                    # total size of directory
du -sh *                            # size of each item in current dir
du -sh * | sort -h                  # sort by size (human-readable)
du -sh * | sort -rh | head -10      # top 10 largest items
du -ah ./project | sort -rh | head -20  # top 20 largest files/dirs recursively
ncdu .                              # interactive disk usage browser (install separately)
```

### Process Management (ps, kill)

```bash
# List processes
ps aux                              # all processes (BSD syntax)
ps aux | grep nginx
pgrep nginx                         # PIDs matching process name
pgrep -u alice                      # all PIDs owned by alice

# Send signals
kill 1234                           # SIGTERM — ask process to stop gracefully
kill -15 1234                       # same as above (SIGTERM = 15)
kill -9 1234                        # SIGKILL — force stop (use as last resort) # ⚠️
killall nginx                       # kill all processes named "nginx"
pkill -f "python worker.py"         # kill by full command match
