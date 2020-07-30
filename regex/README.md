# 🔍 Regex Cheat Sheet

Regular expressions are a pattern-matching language supported by virtually every programming language and CLI tool. Once learned, the same mental model applies in grep, sed, awk, Python, JavaScript, and your editor's search. Version: PCRE2 / ERE / BRE (2024)

[![regex101](https://img.shields.io/badge/test-regex101.com-orange)](https://regex101.com)
[![License](https://img.shields.io/badge/license-MIT-green)](../LICENSE)

---

## Table of Contents

- [Installation](#installation)
- [Core Concepts](#-core-concepts)
- [Quick Reference](#-quick-reference)
  - [Metacharacters](#metacharacters)
  - [Quantifiers](#quantifiers)
  - [Anchors](#anchors)
  - [Character Classes](#character-classes)
  - [Groups and Lookarounds](#groups-and-lookarounds)
  - [Dialect Comparison: BRE vs ERE vs PCRE](#dialect-comparison-bre-vs-ere-vs-pcre)
- [Common Commands](#-common-commands)
- [Common Patterns](#-common-patterns)
- [Real-World Examples](#-real-world-examples)
- [Testing Tools](#-testing-tools)
- [Pro Tips](#-pro-tips)
- [Resources](#-resources)

---

## Installation

Regex is built into tools — no installation needed. For testing, see the [Testing Tools](#-testing-tools) section.

---

## 🧠 Core Concepts

**Literal match** — most characters match themselves. The pattern `cat` matches the string "cat" exactly. No special meaning is attached to letters, digits, or most punctuation.

**Metacharacter** — characters with special meaning in a regex: `. * + ? ^ $ [] {} | () \`. To match them literally, escape with a backslash: `\.` matches a period, `\*` matches an asterisk.

**Quantifier** — specifies how many times the preceding element must match. `*` means zero or more, `+` means one or more, `?` means zero or one, and `{n,m}` means between n and m times (inclusive).

**Anchor** — matches a position in the string rather than a character. `^` asserts the start of a line, `$` asserts the end of a line, and `\b` asserts a word boundary (the transition between a word character and a non-word character).

**Character class** — `[abc]` matches any one of: a, b, or c. `[a-z]` matches any lowercase letter (range). `[^abc]` is a negated class — it matches any character except a, b, or c.

**Group** — parentheses `(abc)` create a capture group: the matched text is stored and can be referenced later with a backreference (`\1`, `\2`, etc.). `(?:abc)` is a non-capturing group — it groups without storing.

**Alternation** — the pipe `|` acts like a logical OR. `cat|dog` matches either "cat" or "dog". Use a group to scope it: `(cat|dog)s` matches "cats" or "dogs".

**Greedy vs lazy** — quantifiers are greedy by default: they match as many characters as possible while still allowing the overall pattern to match. Append `?` to make any quantifier lazy (match as few as possible): `.*` is greedy, `.*?` is lazy.

---

## 📋 Quick Reference

### Metacharacters

| Pattern | Matches |
| ------- | ------- |
| `.` | Any character except newline |
| `\d` | Digit `[0-9]` |
| `\D` | Non-digit `[^0-9]` |
| `\w` | Word character `[a-zA-Z0-9_]` |
| `\W` | Non-word character |
| `\s` | Whitespace `[ \t\n\r\f\v]` |
| `\S` | Non-whitespace |
| `\b` | Word boundary |
| `\B` | Non-word boundary |
| `^` | Start of line/string |
| `$` | End of line/string |
| `\n` | Newline |
| `\t` | Tab |

### Quantifiers

| Quantifier | Meaning |
| ---------- | ------- |
| `*` | 0 or more (greedy) |
| `+` | 1 or more (greedy) |
| `?` | 0 or 1 (optional) |
| `{n}` | Exactly n times |
| `{n,}` | n or more times |
| `{n,m}` | Between n and m times |
| `*?` | 0 or more (lazy — match as few as possible) |
| `+?` | 1 or more (lazy) |
| `??` | 0 or 1 (lazy) |

### Anchors

| Pattern | Matches |
| ------- | ------- |
| `^` | Start of line |
| `$` | End of line |
| `\A` | Start of string (PCRE only — not multiline) |
| `\Z` | End of string (PCRE only) |
| `\b` | Word boundary (between `\w` and `\W`) |
| `\B` | Non-word boundary |

### Character Classes

| Class | Meaning |
| ----- | ------- |
| `[abc]` | Any of: a, b, or c |
| `[^abc]` | None of: a, b, or c (negated) |
| `[a-z]` | Any lowercase letter |
| `[A-Z]` | Any uppercase letter |
| `[0-9]` | Any digit (same as `\d`) |
| `[a-zA-Z0-9_]` | Any word character (same as `\w`) |
| `[:alpha:]` | Letters (POSIX class, used in `[[:alpha:]]`) |
| `[:digit:]` | Digits (POSIX, used in `[[:digit:]]`) |
| `[:space:]` | Whitespace (POSIX, used in `[[:space:]]`) |
| `[:alnum:]` | Letters and digits |

### Groups and Lookarounds

| Pattern | Meaning |
| ------- | ------- |
| `(abc)` | Capture group — stores match for backreference |
| `(?:abc)` | Non-capturing group — groups without storing |
| `(?P<name>abc)` | Named capture group (PCRE/Python) |
| `\1`, `\2` | Backreference to capture group 1, 2 |
| `(?=abc)` | Positive lookahead — match if followed by abc |
| `(?!abc)` | Negative lookahead — match if NOT followed by abc |
| `(?<=abc)` | Positive lookbehind — match if preceded by abc |
| `(?<!abc)` | Negative lookbehind — match if NOT preceded by abc |

### Dialect Comparison: BRE vs ERE vs PCRE

| Feature | BRE (`grep`) | ERE (`grep -E`) | PCRE (`grep -P`, Python, JS) |
| ------- | ------------ | --------------- | ---------------------------- |
| Groups | `\(...\)` | `(...)` | `(...)` |
| Alternation | `\|` | `\|` | `\|` |
| `+` quantifier | `\+` | `+` | `+` |
| `?` quantifier | `\?` | `?` | `?` |
| `{n,m}` | `\{n,m\}` | `{n,m}` | `{n,m}` |
| `\d`, `\w`, `\s` | ✗ | ✗ | ✓ |
| Lookahead | ✗ | ✗ | `(?=...)` |
| Lookbehind | ✗ | ✗ | `(?<=...)` |
| Named groups | ✗ | ✗ | `(?P<name>...)` |
| Non-capturing group | ✗ | ✗ | `(?:...)` |

---

## ⚙️ Common Commands

```bash
# BRE (default grep)
grep "^ERROR" app.log                         # lines starting with ERROR
grep "\.log$" filelist.txt                    # lines ending with .log
grep "[0-9]\{4\}-[0-9]\{2\}-[0-9]\{2\}" log.txt  # BRE date pattern

# ERE (grep -E or egrep)
grep -E "error|warning|fatal" app.log         # alternation
grep -E "^[0-9]{4}-[0-9]{2}-[0-9]{2}" app.log  # ERE date pattern
grep -E "\b[A-Z]{2,}\b" text.txt              # all-caps words

# PCRE (grep -P — Linux only, not macOS default grep)
grep -P "\d{4}-\d{2}-\d{2}" log.txt           # PCRE digits
grep -P "(?<=user_id=)\d+" app.log             # lookbehind: extract user IDs
grep -oP "(?<=ip=)\S+" access.log              # -o print only match + lookbehind

# sed
sed 's/[0-9]\+/NUM/g' file.txt                # BRE: replace all numbers
sed -E 's/([0-9]{4})-([0-9]{2})-([0-9]{2})/\3\/\2\/\1/' dates.txt  # ERE: reformat date

# awk
awk '/ERROR|FATAL/' app.log                   # ERE by default
awk '!/^#/' config.txt                        # exclude comment lines
```

---

## 🗂️ Common Patterns

| Pattern | Example Match |
| ------- | ------------- |
| `[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}` | `alice@company.com` |
| `https?://[^\s/$.?#].[^\s]*` | `https://example.com/path?q=1` |
| `\b(?:\d{1,3}\.){3}\d{1,3}\b` | `203.0.113.5` |
| `[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}` | `550e8400-e29b-41d4-a716-446655440000` |
| `\d{4}-(?:0[1-9]\|1[0-2])-(?:0[1-9]\|[12]\d\|3[01])` | `2024-01-31` |
| `(?:[01]\d\|2[0-3]):[0-5]\d:[0-5]\d` | `14:35:09` |
| `v?\d+\.\d+\.\d+(?:-[a-zA-Z0-9.]+)?` | `v1.23.0-beta.1` |
| `#[0-9a-fA-F]{3,6}` | `#ff5733` or `#f57` |
| `\+?[1-9]\d{1,14}` | `+14155552671` (E.164 phone) |
| `(?:(?:25[0-5]\|2[0-4]\d\|[01]?\d\d?)\.){3}(?:25[0-5]\|2[0-4]\d\|[01]?\d\d?)` | `192.168.1.255` (strict IPv4) |

---

## 💡 Real-World Examples

### 1. Validate an email address in bash

```bash
email="alice@company.com"
if echo "$email" | grep -qE "^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$"; then
  echo "Valid email"
else
  echo "Invalid email"
fi
```

### 2. Extract all IP addresses from a log file

```bash
# Extract all IPv4 addresses
grep -oE "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b" /var/log/nginx/access.log | sort | uniq -c | sort -rn

# Using PCRE for stricter match (0-255 per octet)
grep -oP "(?:(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)" access.log
```

### 3. Regex in Python

```python
import re

text = "Order #1042 placed by alice@company.com on 2024-01-15"

# re.search — find first match anywhere in string
match = re.search(r"\d{4}-\d{2}-\d{2}", text)
if match:
    print(match.group())           # 2024-01-15

# re.findall — return all matches as a list
emails = re.findall(r"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}", text)
print(emails)                      # ['alice@company.com']

# re.sub — substitute matches
clean = re.sub(r"\s+", " ", "too   many    spaces")
print(clean)                       # 'too many spaces'

# Named capture groups
pattern = r"Order #(?P<order_id>\d+).*by (?P<email>\S+@\S+)"
m = re.match(pattern, text)
if m:
    print(m.group("order_id"))     # 1042
    print(m.group("email"))        # alice@company.com

# Compiled pattern (reuse for performance)
date_re = re.compile(r"\d{4}-\d{2}-\d{2}")
dates = date_re.findall("Events on 2024-01-15 and 2024-02-20")
```

### 4. Regex in JavaScript

```javascript
const text = "Order placed by alice@company.com on 2024-01-15";

// Test if pattern matches
const hasDate = /\d{4}-\d{2}-\d{2}/.test(text);
console.log(hasDate);              // true

// Extract first match
const dateMatch = text.match(/\d{4}-\d{2}-\d{2}/);
console.log(dateMatch[0]);         // "2024-01-15"

// Extract all matches (g flag)
const allDates = "2024-01-15 and 2024-02-20".match(/\d{4}-\d{2}-\d{2}/g);
console.log(allDates);             // ["2024-01-15", "2024-02-20"]

// Replace
const slug = "Hello World! How Are You?".replace(/[^a-z0-9]+/gi, "-").toLowerCase();
console.log(slug);                 // "hello-world-how-are-you"

// Named capture groups (ES2018+)
const re = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/;
const m = "2024-01-15".match(re);
console.log(m.groups.year);        // "2024"

// matchAll — all matches with capture groups
const str = "cat=5, dog=12, bird=3";
for (const m of str.matchAll(/(\w+)=(\d+)/g)) {
    console.log(`${m[1]}: ${m[2]}`);
}
// cat: 5 / dog: 12 / bird: 3
```

### 5. Lookahead and lookbehind in practice

