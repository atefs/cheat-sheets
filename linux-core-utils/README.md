# 🐧 Linux Core Utils — Text Processing Cheat Sheet

The Linux command-line toolbox is built around small, composable tools that each do one thing well. This sheet covers text-processing utilities: `grep`, `awk`, `sed`, `sort`, `uniq`, `cut`, `tr`, `wc`, `head`, and `tail`. For file and system utilities (`find`, `chmod`, `tar`, `rsync`, etc.), see [file-system.md](./file-system.md).

[![GNU Coreutils](https://img.shields.io/badge/GNU-coreutils%208.x-blue)](https://www.gnu.org/software/coreutils/)
[![License](https://img.shields.io/badge/license-MIT-green)](../LICENSE)

> 💡 **Two-part reference:** This file covers text processing. File operations, permissions, processes, `tar`, and `rsync` are in [file-system.md](./file-system.md).

---

## Table of Contents

- [Installation](#-installation)
- [Core Concepts](#-core-concepts)
- [Quick Reference](#-quick-reference)
  - [grep flags](#grep-flags)
  - [awk expressions](#awk-expressions)
  - [sed commands](#sed-commands)
- [Common Commands](#-common-commands)
  - [grep](#grep)
  - [awk](#awk)
  - [sed](#sed)
  - [sort](#sort)
  - [uniq](#uniq)
  - [cut](#cut)
  - [tr](#tr)
  - [wc](#wc)
  - [head / tail](#head--tail)
- [Real-World Examples](#-real-world-examples)
- [Combining Tools](#-combining-tools)
- [Pro Tips](#-pro-tips)
- [Resources](#-resources)

---

## 📦 Installation

These tools are preinstalled on all Linux distributions. macOS ships with BSD variants; install GNU versions for more features and consistent flags. Windows users should use WSL2 or Git Bash for a compatible environment.

```bash
# macOS: install GNU versions (more features, consistent flags)
brew install grep gawk gnu-sed coreutils findutils

# Add to your ~/.zshrc or ~/.bashrc to use GNU versions by default:
export PATH="$(brew --prefix)/opt/coreutils/libexec/gnubin:$PATH"
export PATH="$(brew --prefix)/opt/grep/libexec/gnubin:$PATH"
export PATH="$(brew --prefix)/opt/gnu-sed/libexec/gnubin:$PATH"
export PATH="$(brew --prefix)/opt/findutils/libexec/gnubin:$PATH"
```

```powershell
# Windows: use WSL2 (recommended) for a full Linux environment
wsl --install                              # enables WSL2 with Ubuntu

# Or use Git Bash (ships with Git for Windows) for basic Unix tools
# Or install GnuWin32: https://gnuwin32.sourceforge.net/
```

**Key BSD vs GNU differences** — if you skip the GNU install on macOS, watch for these:

| Command | GNU (Linux / macOS after brew) | BSD (macOS default) |
| ------- | ------------------------------ | ------------------- |
| `sed -i 's/a/b/' file` | Works as-is | `sed -i '' 's/a/b/' file` (empty backup arg required) |
| `grep -P 'pattern'` | PCRE patterns supported | Not supported (`-P` is invalid) |
| `ls --color=auto` | GNU color flag | `ls -G` (BSD color flag) |
| `xargs -d '\n'` | Custom delimiter | Not supported |
| `date -d "2 days ago"` | GNU relative date | `date -v-2d` (BSD syntax) |
| `sort --parallel=4` | Multi-threaded sort | Not supported |

---

## 🧠 Core Concepts

> The Unix philosophy: write programs that do one thing and do it well; write programs that work together; write programs that handle text streams. Each tool in this sheet is a filter — it reads from stdin (or a file), transforms or selects data, and writes to stdout. Chaining them with pipes (`|`) builds powerful, ad-hoc data pipelines without writing a single script.

**The three standard streams:** stdin (0), stdout (1), stderr (2). Redirection operators:

```bash
command > out.txt        # redirect stdout (overwrite)
command >> out.txt       # redirect stdout (append)
command 2> err.txt       # redirect stderr
command &> all.txt       # redirect both stdout and stderr
command < input.txt      # feed file as stdin
command 2>/dev/null      # discard stderr
```

**Exit codes:**

```bash
command; echo $?         # 0 = success, non-zero = error
grep "pattern" file.txt; echo $?  # 0 = found, 1 = not found, 2 = error
```

**Pipeline flow — how tools chain together:**

```
cat file.log | grep "ERROR" | awk '{print $5}' | sort | uniq -c | sort -rn
     │               │               │              │        │         │
  read file     filter lines    extract field    sort    dedupe    sort by count
```

---

## ⚡ Quick Reference

### grep flags

| Flag | Description |
| ---- | ----------- |
| `-r` | Recursive through directories |
| `-i` | Case-insensitive |
| `-n` | Show line numbers |
| `-v` | Invert match (exclude) |
| `-l` | Print filenames only |
| `-c` | Count matching lines |
| `-E` | Extended regex (same as `egrep`) |
| `-F` | Fixed string (literal, fastest) |
| `-o` | Print only matched part |
| `-A N` | N lines after match |
| `-B N` | N lines before match |
| `-C N` | N lines before and after |
| `--include="*.go"` | Limit to file pattern |

### awk expressions

| Expression | Description |
| ---------- | ----------- |
| `$0` | Entire line |
| `$1`, `$2` | Field 1, 2 (space-delimited) |
| `NR` | Current record (line) number |
| `NF` | Number of fields on current line |
| `-F:` | Set field separator to `:` |
| `BEGIN{}` | Run before any input |
| `END{}` | Run after all input |

### sed commands

| Command | Description |
| ------- | ----------- |
| `s/old/new/` | Substitute first occurrence per line |
| `s/old/new/g` | Substitute all occurrences |
| `s/old/new/gi` | Global + case-insensitive |
| `-i` | Edit file in-place |
| `-i.bak` | In-place with backup |
| `/pattern/d` | Delete matching lines |
| `-n '5p'` | Print line 5 only |
| `-n '3,7p'` | Print lines 3–7 |

---

## 🔧 Common Commands

### grep

grep filters lines from files or stdin that match a pattern.

```bash
# Basic search
grep "ERROR" app.log
grep -r "TODO" ./src                    # recursive
grep -i "error" app.log                 # case-insensitive
grep -n "panic" main.go                 # show line numbers
grep -l "config" *.go                   # only filenames
grep -v "DEBUG" app.log                 # exclude DEBUG lines
grep -c "ERROR" app.log                 # count matching lines
grep -A 3 -B 3 "FATAL" app.log          # 3 lines before AND after each match

# Extended regex
grep -E "error|warning|fatal" app.log   # OR
grep -E "^[0-9]{4}-[0-9]{2}" app.log   # lines starting with YYYY-MM

# Fixed string (no regex — fastest for literal searches)
grep -F "user.email[0]" template.html

# Recursive with file filter
grep -r "TODO" ./src --include="*.go" | wc -l
```

### awk

awk processes text line by line, splitting each into fields. It has a built-in C-like language for conditions and actions.

```bash
# Print specific column
awk '{print $2}' access.log             # column 2 (space-delimited)
awk -F: '{print $1}' /etc/passwd        # column 1, colon-delimited

# Conditional print
awk '$3 > 100 {print $1, $3}' metrics.txt

# Sum a column
awk '{sum += $4} END {print "Total:", sum}' sales.csv

# Print line count
awk 'END {print NR}' file.txt

# Print specific line
awk 'NR==5 {print}' file.txt

# Lines with more than 3 fields
awk 'NF > 3 {print}' data.txt

# BEGIN/END blocks
awk 'BEGIN {print "=== Report ==="} {print $1} END {print "=== Done ==="}' data.txt

# Multiple delimiters
awk -F'[,;]' '{print $1}' data.csv

# Count requests per IP from nginx access log
awk '{print $1}' access.log | sort | uniq -c | sort -rn | head -20
```

### sed

sed (stream editor) applies editing commands to text line by line.

```bash
# Substitute
sed 's/localhost/db.company.com/' config.yml    # first occurrence per line
sed 's/http:/https:/g' urls.txt                 # all occurrences (global)
sed 's/foo/bar/gi' file.txt                     # global + case-insensitive
sed -i 's/v1/v2/g' api-client.go                # in-place edit
sed -i.bak 's/v1/v2/g' api-client.go            # in-place with .bak backup

# Delete lines
sed '/^#/d' config.txt                          # delete comment lines
sed '5d' file.txt                               # delete line 5
sed '3,7d' file.txt                             # delete lines 3-7

# Print specific lines
sed -n '5p' file.txt                            # print line 5 only
sed -n '3,7p' file.txt                          # print lines 3-7

# Insert / append
sed '3i\# inserted comment' config.txt          # insert before line 3
sed '3a\# appended comment' config.txt          # append after line 3

# Multiple expressions
sed -e 's/foo/bar/g' -e 's/baz/qux/g' file.txt

# Remove empty lines
sed '/^$/d' file.txt

# Remove trailing whitespace
sed 's/[[:space:]]*$//' file.txt
```

### sort

```bash
sort file.txt                           # alphabetical
sort -n numbers.txt                     # numeric sort
sort -rn numbers.txt                    # reverse numeric
sort -k2 data.txt                       # sort by column 2
sort -t: -k3 -n /etc/passwd             # sort by UID (field 3, colon-separated)
sort -u file.txt                        # sort + remove duplicates
sort -f file.txt                        # case-insensitive sort
sort -h sizes.txt                       # human-readable sizes (1K, 2M, 3G)
```

**Key flags:**

| Flag | Description |
| ---- | ----------- |
| `-n` | Numeric sort |
| `-r` | Reverse order |
| `-k N` | Sort by field N |
| `-t SEP` | Field separator |
| `-u` | Remove duplicates |
| `-f` | Case-insensitive |
| `-h` | Human-readable sizes |

### uniq

uniq collapses adjacent duplicate lines. Input must be sorted first.

```bash
sort file.txt | uniq                    # remove duplicates
sort file.txt | uniq -c                 # count occurrences (prepends count)
sort file.txt | uniq -d                 # show only duplicated lines
sort file.txt | uniq -u                 # show only lines that appear once
sort file.txt | uniq -c | sort -rn      # frequency table (most common first)
```

> ⚠️ `uniq` only removes **adjacent** duplicates. Always `sort` first unless your data is already sorted.

### cut

cut extracts specific fields or character ranges from each line.

```bash
cut -d: -f1 /etc/passwd                 # first field, colon delimiter (usernames)
cut -d, -f1,3 data.csv                  # fields 1 and 3, comma delimiter
cut -d: -f1,3 /etc/passwd               # fields 1 and 3
cut -c1-10 file.txt                     # characters 1-10 of each line
cut -c5- file.txt                       # from character 5 to end of line
```

### tr

tr translates or deletes characters.

```bash
tr 'a-z' 'A-Z' < file.txt              # convert to uppercase
tr 'A-Z' 'a-z' < file.txt              # convert to lowercase
tr -d '\n' < file.txt                   # remove all newlines (join lines)
tr -d ' ' < file.txt                    # remove all spaces
tr -s ' ' < file.txt                    # squeeze multiple spaces to one
echo "hello world" | tr ' ' '\n'        # split on space (one word per line)
echo "a,b,c" | tr ',' '\t'             # replace commas with tabs
tr -dc '[:alnum:]' < /dev/urandom | head -c 16  # generate 16-char random string
```

### wc

```bash
wc -l file.txt                          # line count
wc -w file.txt                          # word count
wc -c file.txt                          # byte count
wc -l *.log                             # line counts for multiple files
ls ./src | wc -l                        # count files in directory
```

### head / tail

```bash
head -20 file.txt                       # first 20 lines (default: 10)
head -1 file.txt                        # first line only
tail -20 file.txt                       # last 20 lines
tail -1 file.txt                        # last line only
tail -f /var/log/app.log                # follow: stream new lines as they appear
tail -f /var/log/app.log | grep ERROR   # follow + filter live
tail -n +5 file.txt                     # print from line 5 onwards (skip first 4)
```

---

## 🌍 Real-World Examples

### 1. Find all TODO/FIXME comments in a codebase

> You want a list of every TODO or FIXME across all source files, with filenames and line numbers.

```bash
# Recursive search with line numbers
grep -rn "TODO\|FIXME" ./src --include="*.go"

# Count per file
grep -rl "TODO" ./src --include="*.go" | while read f; do
  count=$(grep -c "TODO" "$f")
  echo "$count $f"
done | sort -rn
```

### 2. Parse nginx access logs: top IPs and status codes

> You want to find the top 10 client IPs and see a breakdown of HTTP status codes.

```bash
# Top 10 IPs by request count
awk '{print $1}' /var/log/nginx/access.log \
  | sort | uniq -c | sort -rn | head -10

# Status code distribution
awk '{print $9}' /var/log/nginx/access.log \
  | sort | uniq -c | sort -rn

# Requests in the last hour (assuming ISO timestamp in field 4)
awk -v d="$(date '+%d/%b/%Y:%H')" '$4 ~ d {print $1}' /var/log/nginx/access.log \
  | sort | uniq -c | sort -rn | head -10
```

### 3. Bulk find-and-replace in config files

> You need to rename a database host from `db-old.company.com` to `db-prod.company.com` across all config files.

```bash
# Preview changes first (dry run — no -i)
grep -rl "db-old.company.com" ./config/

# Apply in-place with backup
find ./config -name "*.yml" -o -name "*.env" \
  | xargs sed -i.bak 's/db-old\.company\.com/db-prod.company.com/g'

# Verify
grep -r "db-old" ./config/          # should return nothing
grep -r "db-prod" ./config/         # should show replacements

# Remove backups when satisfied
find ./config -name "*.bak" -delete
```

### 4. Extract unique values from a CSV column

> You have a CSV export and want all unique values from the "status" column (column 3).

```bash
# Skip header line, extract column 3, sort and deduplicate
tail -n +2 orders.csv | cut -d, -f3 | sort | uniq

# With counts
tail -n +2 orders.csv | cut -d, -f3 | sort | uniq -c | sort -rn
```

### 5. Monitor live errors in a log file

> You want to watch a log file in real-time and alert only on ERROR or FATAL entries.

```bash
# Follow log, filter for errors
tail -f /var/log/app.log | grep --line-buffered -E "ERROR|FATAL"

# Include 2 lines of context around each error
tail -f /var/log/app.log | grep --line-buffered -A 2 "FATAL"

# Count errors per minute (using awk for timestamps)
tail -f /var/log/app.log | awk '/ERROR/ {count++} /:[0-9]{2} / {
  if (count > 0) print strftime("%H:%M", systime()), count, "errors"
  count=0
}'
```

---

## 🔗 Combining Tools

Three advanced pipelines that demonstrate the power of chaining text tools.

**Pipeline 1: Build a frequency table from any text**

```bash
# Most common words in a file
cat README.md \
  | tr '[:upper:]' '[:lower:]' \
  | tr -cs '[:alpha:]' '\n' \
  | sort \
  | uniq -c \
  | sort -rn \
  | head -20
```

**Pipeline 2: Extract structured data from mixed-format logs**

```bash
# From: 2024-01-15 14:23:01 [ERROR] user_id=1042 action=login ip=203.0.113.5
# Extract user_id and ip for all ERROR lines in the last 100 lines
tail -100 /var/log/app.log \
  | grep "ERROR" \
  | sed 's/.*user_id=\([0-9]*\).*ip=\([^ ]*\).*/\1 \2/' \
  | sort -k2 | uniq
```

**Pipeline 3: Compare installed packages between two servers**

```bash
# On server A:
dpkg -l | awk '/^ii/{print $2}' | sort > /tmp/pkgs-a.txt

# On server B:
dpkg -l | awk '/^ii/{print $2}' | sort > /tmp/pkgs-b.txt

# Show packages on A but not B:
comm -23 /tmp/pkgs-a.txt /tmp/pkgs-b.txt

# Show packages on B but not A:
comm -13 /tmp/pkgs-a.txt /tmp/pkgs-b.txt
```

---

## 💡 Pro Tips

> 💡 **`grep -F` is dramatically faster for literal strings**
> When you don't need regex, `-F` skips pattern compilation. On large log files, this can be 5-10x faster.

> 💡 **`grep --line-buffered` when following a pipe**
> By default, `grep` buffers output. When piped from `tail -f`, add `--line-buffered` so each matching line appears immediately.

> 💡 **`awk` for anything `cut` can't do**
> `cut` only works with a single delimiter and fixed fields. When your data is whitespace-delimited or has variable field counts, `awk` handles it cleanly. When both work, `cut` is simpler.

> 💡 **`sed -i.bak` — always make a backup on first use**
> When using `sed -i` on production files, use `-i.bak` to create a backup. Once you're confident the pattern is correct, delete the backups with `find . -name "*.bak" -delete`.

> 💡 **Exit codes make pipelines scriptable**
> `grep` returns 0 if it finds a match, 1 if not. Use this in conditionals:
> ```bash
> if grep -q "ERROR" app.log; then
>   echo "Errors found in log"
>   exit 1
> fi
> ```

> ❌ **Don't parse `/etc/passwd` with `cut` when `awk` is cleaner**
> `cut -d: -f1 /etc/passwd` works, but `awk -F: '{print $1}' /etc/passwd` is more readable when you need multiple fields. Choose `cut` for a single field, `awk` when you need conditions or multiple fields.

---

> 📁 **Next:** [file-system.md](./file-system.md) — `find`, `chmod`, `tar`, `rsync`, disk usage, and process management.

---

> 💡 **Use `-print0` + `xargs -0` for filenames with spaces**
> `find . -name '*.log' -print0 | xargs -0 rm` handles filenames containing spaces, newlines, or special characters safely. The `-0` flag reads null-delimited input instead of whitespace-delimited. Always prefer this over bare `xargs` when processing file paths.

> 💡 **`tee` — write to a file and stdout simultaneously**
> `command | tee output.log` lets you see the output in real time AND save it to a file. Use `tee -a` to append instead of overwrite. Handy for long builds or migrations where you want a log without losing the live view.

## 📚 Resources

- [GNU Coreutils Manual](https://www.gnu.org/software/coreutils/manual/) — Definitive reference for all tools
- [The AWK Programming Language](https://awk.readthedocs.io/) — Thorough awk documentation
- [sed manual (GNU)](https://www.gnu.org/software/sed/manual/sed.html) — Complete sed reference
- [Bash Hackers Wiki: Pipelines](https://wiki.bash-hackers.org/syntax/basicgrammar#pipelines) — Deep dive into pipe behavior
- [commandlinefu.com](https://www.commandlinefu.com/) — Community-contributed one-liners
