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

---

_Content being added — work in progress._
