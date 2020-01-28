# 📘 CLI Cheat Sheets Repository — LLM Implementation Plan

> **Purpose:** This document is a complete, self-contained specification for an LLM to implement a
> professional GitHub repository of CLI cheat sheets. Follow every milestone in order. Do not skip
> sections. Each milestone is independently deployable.

---

## Table of Contents

1. [Repository Philosophy](https://claude.ai/chat/b7178a80-115a-4fdf-a8d8-8b8b49769e2c#1-repository-philosophy)
2. [Repository Structure](https://claude.ai/chat/b7178a80-115a-4fdf-a8d8-8b8b49769e2c#2-repository-structure)
3. [Global Files Specification](https://claude.ai/chat/b7178a80-115a-4fdf-a8d8-8b8b49769e2c#3-global-files-specification)
4. [Cheat Sheet Template (Master)](https://claude.ai/chat/b7178a80-115a-4fdf-a8d8-8b8b49769e2c#4-cheat-sheet-template-master)
5. [Milestone 0 — Repo Scaffolding](https://claude.ai/chat/b7178a80-115a-4fdf-a8d8-8b8b49769e2c#milestone-0--repo-scaffolding)
6. [Milestone 1 — Git](https://claude.ai/chat/b7178a80-115a-4fdf-a8d8-8b8b49769e2c#milestone-1--git)
7. [Milestone 2 — Vim (Part 1 — Core)](https://claude.ai/chat/b7178a80-115a-4fdf-a8d8-8b8b49769e2c#milestone-2--vim-part-1--core)
8. [Milestone 3 — Vim (Part 2 — Advanced)](https://claude.ai/chat/b7178a80-115a-4fdf-a8d8-8b8b49769e2c#milestone-3--vim-part-2--advanced)
9. [Milestone 4 — tmux](https://claude.ai/chat/b7178a80-115a-4fdf-a8d8-8b8b49769e2c#milestone-4--tmux)
10. [Milestone 5 — Linux Core Utils (Part 1 — Text Processing)](https://claude.ai/chat/b7178a80-115a-4fdf-a8d8-8b8b49769e2c#milestone-5--linux-core-utils-part-1--text-processing)
11. [Milestone 6 — Linux Core Utils (Part 2 — File &amp; System)](https://claude.ai/chat/b7178a80-115a-4fdf-a8d8-8b8b49769e2c#milestone-6--linux-core-utils-part-2--file--system)
12. [Milestone 7 — Docker](https://claude.ai/chat/b7178a80-115a-4fdf-a8d8-8b8b49769e2c#milestone-7--docker)
13. [Milestone 8 — Docker Compose](https://claude.ai/chat/b7178a80-115a-4fdf-a8d8-8b8b49769e2c#milestone-8--docker-compose)
14. [Milestone 9 — SSH](https://claude.ai/chat/b7178a80-115a-4fdf-a8d8-8b8b49769e2c#milestone-9--ssh)
15. [Milestone 10 — Regex](https://claude.ai/chat/b7178a80-115a-4fdf-a8d8-8b8b49769e2c#milestone-10--regex)
16. [Milestone 11 — Homebrew](https://claude.ai/chat/b7178a80-115a-4fdf-a8d8-8b8b49769e2c#milestone-11--homebrew)
17. [Milestone 12 — Terraform](https://claude.ai/chat/b7178a80-115a-4fdf-a8d8-8b8b49769e2c#milestone-12--terraform)
18. [Milestone 13 — apt](https://claude.ai/chat/b7178a80-115a-4fdf-a8d8-8b8b49769e2c#milestone-13--apt)
19. [Style &amp; Formatting Rules](https://claude.ai/chat/b7178a80-115a-4fdf-a8d8-8b8b49769e2c#style--formatting-rules)
20. [Quality Checklist](https://claude.ai/chat/b7178a80-115a-4fdf-a8d8-8b8b49769e2c#quality-checklist)

---

## 1. Repository Philosophy

This repository must feel like a **professional reference** , not a dump of man-page excerpts.
Every cheat sheet must:

- Be **scannable in under 10 seconds** — readers come with a specific question.
- Use **real-world examples** , not abstract placeholders like `<value>`.
- Include **color/emoji-aided visual hierarchy** using GitHub Markdown features.
- Be **copy-paste safe** — every code block must be runnable as-is or clearly annotated.
- Be **opinionated** — include "best practices" and warn against common mistakes.
- Stay **current** — note version-specific behaviors when relevant.

---

## 2. Repository Structure

```
cli-cheatsheets/
│
├── .github/
│   ├── ISSUE_TEMPLATE/
│   │   ├── bug_report.md
│   │   ├── new_cheatsheet.md
│   │   └── improvement.md
│   ├── PULL_REQUEST_TEMPLATE.md
│   └── workflows/
│       └── lint.yml                  # Lint markdown on PRs
│
├── git/
│   └── README.md
│
├── vim/
│   ├── README.md                     # Core Vim (milestone 2)
│   └── advanced.md                   # Advanced Vim (milestone 3)
│
├── tmux/
│   └── README.md
│
├── linux-core-utils/
│   ├── README.md                     # Index + text processing (milestone 5)
│   └── file-system.md                # File & system utils (milestone 6)
│
├── docker/
│   └── README.md
│
├── docker-compose/
│   └── README.md
│
├── ssh/
│   └── README.md
│
├── regex/
│   └── README.md
│
├── homebrew/
│   └── README.md
│
├── terraform/
│   └── README.md
│
├── apt/
│   └── README.md
│
├── _template/
│   └── README.md                     # Blank cheat sheet template for contributors
│
├── assets/
│   └── banner.png                    # Optional: repo banner image
│
├── README.md                         # Root index
├── CONTRIBUTING.md
├── CODE_OF_CONDUCT.md
├── CHANGELOG.md
├── LICENSE                           # MIT
└── .markdownlint.yml                 # Linting rules
```

---

## 3. Global Files Specification

### 3.1 Root `README.md`

The root README is the **storefront** . It must include:

```markdown
# 🖥️ CLI Cheat Sheets

> A curated, community-driven collection of clean and practical cheat sheets
> for developers, DevOps engineers, and power users.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![Contributions](https://img.shields.io/github/contributors/<org>/<repo>)]()
[![Stars](https://img.shields.io/github/stars/<org>/<repo>?style=social)]()
```

Followed by:

- A **quick-nav table** (emoji + tool name + one-line description + link)
- A **"How to use this repo"** section (browser, raw, clone)
- A **"Contributing"** one-liner with link to CONTRIBUTING.md
- A **"Tools covered"** table with version coverage and last-updated date

**Quick-nav table format:**

```markdown
| Tool                                       | Category        | Description                                   |
| ------------------------------------------ | --------------- | --------------------------------------------- |
| [🔀 Git](./git/)                           | VCS             | Version control commands, branching, rebasing |
| [📝 Vim](./vim/)                           | Editor          | Modal editing, motions, macros                |
| [🪟 tmux](./tmux/)                         | Terminal        | Session, window and pane management           |
| [🐧 Linux Core Utils](./linux-core-utils/) | Shell           | grep, awk, sed, find and more                 |
| [🐳 Docker](./docker/)                     | Container       | Build, run, inspect containers                |
| [🐙 Docker Compose](./docker-compose/)     | Container       | Multi-container orchestration                 |
| [🔐 SSH](./ssh/)                           | Network         | Remote access, tunneling, key management      |
| [🔍 Regex](./regex/)                       | Universal       | Pattern matching across all tools             |
| [🍺 Homebrew](./homebrew/)                 | Package Manager | macOS/Linux package management                |
| [🏗️ Terraform](./terraform/)               | IaC             | Infrastructure as code                        |
| [📦 apt](./apt/)                           | Package Manager | Debian/Ubuntu package management              |
```

---

### 3.2 `CONTRIBUTING.md`

Must cover:

1. **Code of Conduct** — reference CODE_OF_CONDUCT.md
2. **Getting Started** — fork, clone, branch naming (`feat/add-curl-cheatsheet`)
3. **Adding a New Cheat Sheet** — step-by-step using `_template/README.md`
4. **Improving an Existing Sheet** — what counts as a good improvement
5. **Style Guide** — enforce the cheat sheet template structure
6. **Markdown rules** — reference `.markdownlint.yml`
7. **Pull Request Process** — checklist before submitting
8. **Review Process** — what reviewers look for
9. **Recognition** — contributors are listed in root README

**PR checklist (embed in CONTRIBUTING.md):**

````markdown
- [ ] Followed the cheat sheet template structure
- [ ] All code blocks specify a language (`bash, `yaml, etc.)
- [ ] Examples are real and tested
- [ ] No placeholder values left (e.g. `YOUR_VALUE` is fine, `<value>` is not)
- [ ] Added tool to root README quick-nav table (if new sheet)
- [ ] Ran markdownlint locally
- [ ] Spell-checked
````

---

### 3.3 `LICENSE`

Use **MIT License** . Full text. Year and author placeholder:

```
MIT License
Copyright (c) 2025 <Author or Org>
...
```

---

### 3.4 `CODE_OF_CONDUCT.md`

Use the **Contributor Covenant v2.1** (standard open source CoC). Full text.

---

### 3.5 `CHANGELOG.md`

Follow [Keep a Changelog](https://keepachangelog.com/) format:

```markdown
# Changelog

All notable changes to this project are documented here.
Format: [Semantic Versioning](https://semver.org)

## [Unreleased]

## [1.0.0] - 2025-XX-XX

### Added

- Initial cheat sheets: Git, Vim, tmux, Linux Core Utils, Docker, Docker Compose, SSH, Regex, Homebrew, Terraform, apt
```

---

### 3.6 `.markdownlint.yml`

```yaml
default: true
MD013:
  line_length: 120
  code_blocks: false
  tables: false
MD033: false # Allow inline HTML (for badges, details tags)
MD041: false # First line doesn't have to be H1 (we use badges first)
MD024: false # Allow duplicate headings (e.g. "Examples" in multiple sections)
```

---

### 3.7 `.github/PULL_REQUEST_TEMPLATE.md`

```markdown
## Description

<!-- What does this PR add or fix? -->

## Type of change

- [ ] New cheat sheet
- [ ] Improvement to existing sheet
- [ ] Bug fix (broken link, wrong command)
- [ ] Repository infrastructure

## Checklist

- [ ] Followed cheat sheet template
- [ ] All code blocks have language tags
- [ ] Examples are real and tested
- [ ] markdownlint passes
```

---

### 3.8 `.github/workflows/lint.yml`

```yaml
name: Lint Markdown
on: [pull_request]
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run markdownlint
        uses: DavidAnson/markdownlint-cli2-action@v16
        with:
          globs: "**/*.md"
```

---

## 4. Cheat Sheet Template (Master)

Every `README.md` inside a tool folder **must** follow this template exactly.
Sections marked `[REQUIRED]` cannot be omitted. `[OPTIONAL]` sections are included if relevant.

````markdown
<!-- BANNER: Use a consistent emoji + tool name heading -->

# <emoji> Tool Name Cheat Sheet

<!-- One paragraph: what the tool is, why it matters, who uses it -->

> **What is it?** Brief 2-3 sentence description.
> **Why learn it?** One compelling sentence.
> **Latest stable version covered:** vX.Y (Month Year)

<!-- BADGES -->

[![Official Docs](badge)](url)
[![License](badge)](url)
[![Version](badge)](url)

---

## 📋 Table of Contents [REQUIRED]

- [Installation](#installation)
- [Core Concepts](#core-concepts)
- [Quick Reference](#quick-reference) ← the money section
- [Common Commands](#common-commands)
- [Real-World Examples](#real-world-examples)
- [Configuration](#configuration) [OPTIONAL]
- [Pro Tips](#pro-tips)
- [Troubleshooting](#troubleshooting) [OPTIONAL]
- [Resources](#resources)

---

## ⚙️ Installation [REQUIRED]

<!-- One subsection per OS/distro. Keep it minimal — just the install command. -->

| Platform         | Command                   |
| ---------------- | ------------------------- |
| macOS (Homebrew) | `brew install <tool>`     |
| Ubuntu/Debian    | `sudo apt install <tool>` |
| Fedora/RHEL      | `sudo dnf install <tool>` |
| Windows (WSL)    | _same as Ubuntu_          |
| From source      | See [official docs]()     |

**Verify installation:**

```bash
<tool> --version
```
````

---

## 🧠 Core Concepts [REQUIRED]

<!--
  This section is critical. DO NOT skip it or make it a list of buzzwords.
  Explain the mental model: what abstractions does this tool use?
  Use a short paragraph per concept, followed by a simple analogy if helpful.
  Use a diagram (ASCII or table) if it helps.
-->

### Concept One

Brief explanation. Analogy if useful.

### Concept Two

...

<!-- If the tool has a state machine or object hierarchy, show it as ASCII -->

<!--
Example for Docker:
Registry → Image → Container
             ↑
           Dockerfile
-->

---

## ⚡ Quick Reference [REQUIRED]

<!--
  This is the section users bookmark. Make it dense, scannable.
  Use a table. Group by sub-topic. One line per command.
  Format: | Command | What it does |
  Use inline code for commands. Add [!] emoji for destructive commands.
-->

### <Sub-topic 1>

| Command               | Description       |
| --------------------- | ----------------- |
| `tool command`        | Short description |
| `tool command --flag` | Short description |

### <Sub-topic 2>

...

---

## 🛠️ Common Commands [REQUIRED]

<!--
  Expand on Quick Reference with context, flags, and real values.
  One H3 per logical group. Each group:
    1. Short intro sentence
    2. Code block(s) with real examples
    3. Key flags table (flag | default | description)
  NO placeholder values like <your-container>. Use realistic names.
-->

### Group Name

Short description of what this group covers.

```bash
# Example with realistic values
tool subcommand --flag value

# Another variant
tool subcommand --flag value --other-flag
```

**Key flags:**

| Flag     | Default | Description  |
| -------- | ------- | ------------ |
| `--flag` | `none`  | What it does |

---

## 🌍 Real-World Examples [REQUIRED]

<!--
  3 to 6 complete, realistic scenarios.
  Each has:
    - A bold title
    - A one-sentence context ("You want to...")
    - A complete, copy-paste-ready code block
    - Inline comments explaining non-obvious lines
-->

### Scenario: `<Name>`

> You want to...

```bash
# Step 1: ...
command one

# Step 2: ...
command two
```

---

## 🔧 Configuration [OPTIONAL]

<!--
  Include if the tool has a config file.
  Show file location(s), annotated minimal config, and common customizations.
-->

**Config file location:** `~/.toolrc` or `~/.config/tool/config`

```ini
# Minimal working config
key = value

# Recommended settings
key = value   # explanation
```

---

## 💡 Pro Tips [REQUIRED]

<!--
  5 to 10 tips that experienced users know but beginners don't.
  Each tip is:
    - A bold heading
    - 1-2 sentences of explanation
    - A code block if applicable
  Include at least one "avoid this" anti-pattern tip.
-->

> 💡 **Tip title**
> Explanation. Example:
>
> ```bash
> command
> ```

> ⚠️ **Common Mistake: ...**
> What goes wrong and why. What to do instead.

---

## 🩺 Troubleshooting [OPTIONAL]

<!--
  Include for tools with common gotchas (SSH, Docker, Terraform...).
  Format: Problem → Cause → Fix
-->

| Problem         | Likely Cause   | Fix           |
| --------------- | -------------- | ------------- |
| `Error message` | Why it happens | `fix command` |

---

## 📚 Resources [REQUIRED]

<!--
  3 to 8 curated links. No link farms. Each link earns its place.
  Format: - [Title](url) — one sentence on why it's useful
-->

- [Official Documentation](https://claude.ai/chat/url) — The authoritative reference
- [Interactive Tutorial](https://claude.ai/chat/url) — Best for beginners
- [Cheat Sheet PDF](https://claude.ai/chat/url) — Printable reference
- [Community / Forum](https://claude.ai/chat/url) — Where to ask questions

````

---

## Milestone 0 — Repo Scaffolding

**Goal:** Create the complete empty repository skeleton with all global files.

**Deliverables:**

1. All directories created as listed in Section 2
2. All global files written as specified in Section 3
3. Each tool folder contains a stub `README.md` with just the H1 heading (e.g., `# 🐳 Docker Cheat Sheet`) and a `> 🚧 Coming soon` notice
4. Root README contains the full quick-nav table with stub links

**Acceptance criteria:**
- `markdownlint` passes on all files
- Root README renders correctly on GitHub
- All links in root README point to existing files (even stubs)

---

## Milestone 1 — Git

**Folder:** `git/README.md`
**Emoji:** 🔀
**Version covered:** Git 2.x

### Sections to implement

#### Installation
- macOS (Homebrew + Xcode tools)
- Ubuntu/Debian
- Windows (Git for Windows, WSL)
- Verify: `git --version`

#### Core Concepts
- **Repository** — working tree, index (staging), object store
- **Commit** — snapshot, not diff; SHA-1 identity
- **Branch** — pointer to a commit; HEAD
- **Remote** — named URL (`origin`, `upstream`)
- **Three trees:** Working Directory → Index → HEAD (diagram)
- **Fast-forward vs merge commit** — explain with ASCII graph
- **Rebase** — replay commits on new base; when to use vs merge

#### Quick Reference Table
Groups: Setup | Staging | Committing | Branching | Merging & Rebasing | Remotes | Undoing | Stash | Tags | Log & Diff

#### Common Commands

**Setup & Config:**
```bash
git config --global user.name "Alice"
git config --global user.email "alice@example.com"
git config --global core.editor "vim"
git config --global init.defaultBranch main
git config --list
````

**Staging & Committing:**

```bash
git init
git clone https://github.com/org/repo.git
git status
git add .
git add -p                    # interactive/patch staging
git commit -m "feat: add login page"
git commit --amend --no-edit
```

**Branching:**

```bash
git branch feature/login
git switch -c feature/login   # create + switch (modern)
git branch -d feature/login
git branch -D feature/login   # force delete
git branch -a                 # list all including remotes
```

**Merging & Rebasing:**

```bash
git merge feature/login
git merge --no-ff feature/login   # always create merge commit
git rebase main
git rebase -i HEAD~3              # interactive rebase last 3 commits
```

**Remotes:**

```bash
git remote add origin git@github.com:org/repo.git
git fetch origin
git pull origin main
git push origin feature/login
git push origin feature/login --force-with-lease   # safe force push
```

**Undoing:**

```bash
git restore file.txt              # discard working dir changes
git restore --staged file.txt     # unstage
git revert HEAD                   # safe undo (new commit)
git reset --soft HEAD~1           # undo commit, keep staged
git reset --hard HEAD~1           # ⚠️ undo commit + discard changes
```

**Stash:**

```bash
git stash
git stash push -m "WIP: login form"
git stash list
git stash pop
git stash apply stash@{2}
git stash drop stash@{0}
```

**Log & Diff:**

```bash
git log --oneline --graph --all
git log --author="Alice" --since="2 weeks ago"
git diff HEAD~1
git diff main..feature/login
git show abc1234
git blame README.md
```

#### Real-World Examples

1. **Start a feature branch and open a PR** — full flow from `switch -c` to `push`
2. **Interactive rebase to clean up messy commits** — squash, reword, fixup
3. **Resolve a merge conflict** — step by step with realistic conflict markers
4. **Recover a deleted branch** — using `reflog`
5. **Cherry-pick a commit from another branch**

#### Configuration

- `~/.gitconfig` — global config
- `.gitignore` — patterns, negation, global gitignore
- `.gitattributes` — line endings, diff drivers

Annotated `~/.gitconfig`:

```ini
[user]
  name = Alice
  email = alice@example.com
[core]
  editor = vim
  autocrlf = input       # Linux/macOS: input; Windows: true
[pull]
  rebase = true          # rebase on pull instead of merge
[push]
  autoSetupRemote = true # git push works without -u on first push
[alias]
  lg = log --oneline --graph --all --decorate
  st = status -sb
  co = switch
```

#### Pro Tips

1. Use `--force-with-lease` instead of `--force` when force-pushing
2. `git add -p` for surgical staging
3. Conventional commits (`feat:`, `fix:`, `chore:`)
4. Never rebase shared/public branches
5. `git bisect` to find which commit introduced a bug
6. `git worktree` to check out multiple branches simultaneously

#### Troubleshooting

| Problem                    | Cause                         | Fix                                    |
| -------------------------- | ----------------------------- | -------------------------------------- |
| Detached HEAD              | Checked out a commit directly | `git switch main`                      |
| Merge conflicts after pull | Diverged history              | `git mergetool`or resolve manually     |
| Pushed wrong commit        | Committed to wrong branch     | `git revert`or `git reset`+ force push |

---

## Milestone 2 — Vim (Part 1 — Core)

**Folder:** `vim/README.md`
**Emoji:** 📝
**Version covered:** Vim 8.x / Neovim 0.9+

### Sections to implement

#### Installation

- macOS: `brew install neovim` / `brew install vim`
- Ubuntu: `sudo apt install neovim`
- Windows: `winget install Neovim.Neovim`
- Verify: `vim --version | head -1`

#### Core Concepts

- **Modal editing** — the key insight: mode determines what keys do
- **The 4 main modes:**
  - Normal — navigation, operators (default on open)
  - Insert — typing text
  - Visual — selecting text
  - Command-line — `:` commands
- **Grammar of Vim:** `[count] operator [motion]`
  - Example: `3dw` = delete 3 words; `ci"` = change inside quotes
- **Buffers, Windows, Tabs** — explain the difference with an analogy
- **The dot operator** — repeat last change

ASCII mode diagram:

```
Normal ──i/a/o──► Insert
  │                  │
  │◄────Esc──────────┘
  │
  ├──v/V/Ctrl-v──► Visual
  │                  │
  │◄────Esc──────────┘
  │
  └──:──────────► Command-line
```

#### Quick Reference Table

Groups: Entering Modes | Motion | Operators | Text Objects | Search | Files/Buffers | Windows/Splits | Marks & Registers | Macros

**Motions:**

| Key               | Motion                               |
| ----------------- | ------------------------------------ |
| `h j k l`         | ← ↓ ↑ →                              |
| `w / b`           | Next/prev word start                 |
| `e / ge`          | Next/prev word end                   |
| `0 / ^`           | Line start (col 0 / first non-blank) |
| `$`               | Line end                             |
| `gg / G`          | File start / end                     |
| `{N}G`            | Go to line N                         |
| `f{c} / F{c}`     | Find char forward/backward on line   |
| `t{c} / T{c}`     | Till char forward/backward           |
| `% `              | Jump to matching bracket             |
| `Ctrl-d / Ctrl-u` | Half-page down/up                    |

**Operators:**

| Key            | Action                         |
| -------------- | ------------------------------ |
| `d`            | Delete (cut)                   |
| `c`            | Change (delete + enter Insert) |
| `y`            | Yank (copy)                    |
| `>`/`<`        | Indent / unindent              |
| `=`            | Auto-indent                    |
| `g~`/`gU`/`gu` | Toggle / upper / lower case    |

**Text Objects:**

| Key       | Object                           |
| --------- | -------------------------------- |
| `iw / aw` | Inner word / a word (with space) |
| `i" / a"` | Inner / outer double quotes      |
| `i( / a(` | Inner / outer parentheses        |
| `i{ / a{` | Inner / outer curly braces       |
| `ip / ap` | Inner / outer paragraph          |
| `it / at` | Inner / outer HTML tag           |

#### Common Commands

**Entering/exiting:**

```
i   — insert before cursor
a   — append after cursor
o   — open new line below
O   — open new line above
I   — insert at line start
A   — append at line end
Esc / Ctrl-[ — return to Normal
```

**Saving and quitting:**

```
:w             — save
:q             — quit (fails if unsaved changes)
:wq / :x / ZZ — save and quit
:q! / ZQ       — quit without saving
:wa            — save all buffers
```

**Search and replace:**

```
/pattern       — search forward
?pattern       — search backward
n / N          — next / previous match
*              — search word under cursor

:%s/old/new/g          — replace all in file
:%s/old/new/gc         — replace all, confirm each
:10,20s/old/new/g      — replace in lines 10–20
```

**Buffers and files:**

```
:e filename    — open file
:ls            — list buffers
:b2            — switch to buffer 2
:bd            — close buffer
:bn / :bp      — next / prev buffer
```

**Windows/splits:**

```
:sp filename   — horizontal split
:vsp filename  — vertical split
Ctrl-w w       — cycle windows
Ctrl-w h/j/k/l — navigate windows
Ctrl-w =       — equalize window sizes
Ctrl-w q       — close window
```

#### Real-World Examples

1. **Edit a config file efficiently** — open, navigate to section, `ci{` to replace block
2. **Multi-line search and replace with confirmation**
3. **Use marks to jump between two distant locations**
4. **Yank to/from system clipboard**

---

## Milestone 3 — Vim (Part 2 — Advanced)

**Folder:** `vim/advanced.md`
**Linked from:** `vim/README.md` with a `→ See advanced.md` callout

### Sections to implement

#### Macros

```
q{register}    — start recording macro
q              — stop recording
@{register}    — play macro
@@             — repeat last macro
10@a           — play macro 10 times
```

Real example: macro to format a CSV column

#### Registers

```
"{register}y   — yank to register
"{register}p   — paste from register
:reg           — list registers
"+y / "+p      — system clipboard (requires +clipboard)
"0p            — last yanked text (not deleted)
```

#### Marks

```
m{a-z}         — set local mark
'{mark}        — jump to mark
:marks         — list all marks
```

#### Visual Block Mode (`Ctrl-v`)

- Multi-line editing
- Append to multiple lines: `Ctrl-v`, select, `$A`, type, `Esc`
- Column insert example

#### Command-line Mode Tips

```
:g/pattern/d            — delete all lines matching pattern
:g/pattern/y A          — yank all matching lines to register a
:v/pattern/d            — delete all non-matching lines (inverse grep)
:sort                   — sort lines
:sort u                 — sort and deduplicate
:r !command             — insert shell command output
```

#### Neovim-specific

- LSP setup basics
- `init.lua` vs `init.vim`
- Plugin manager recommendation (lazy.nvim)
- Essential plugins list (with one-liner purpose)

#### `.vimrc` / `init.vim` Annotated Config

```vim
" Basics
set number relativenumber     " hybrid line numbers
set tabstop=2 shiftwidth=2 expandtab
set ignorecase smartcase       " smart search case
set clipboard=unnamedplus      " use system clipboard
set undofile                   " persistent undo
set splitright splitbelow      " sane split directions
set scrolloff=8                " keep 8 lines above/below cursor

" Keymaps
let mapleader = " "
nnoremap <leader>w :w<CR>
nnoremap <leader>q :q<CR>
nnoremap <C-d> <C-d>zz        " keep cursor centered on scroll
```

---

## Milestone 4 — tmux

**Folder:** `tmux/README.md`
**Emoji:** 🪟
**Version covered:** tmux 3.x

### Sections to implement

#### Core Concepts

- **Session** — a workspace you can detach and reattach; persists when disconnected
- **Window** — a tab within a session; full-screen
- **Pane** — a split within a window
- **Prefix key** — all tmux shortcuts start with `Ctrl-b` by default

Hierarchy diagram:

```
tmux server
└── Session: "work"
    ├── Window 0: "editor"   [vim main.go]
    ├── Window 1: "server"   [go run .]
    └── Window 2: "logs"
        ├── Pane 0 (left)   [tail -f app.log]
        └── Pane 1 (right)  [htop]
```

#### Quick Reference Table

Groups: Sessions | Windows | Panes | Copy Mode | Misc

**Sessions:**

| Shortcut              | Action              |
| --------------------- | ------------------- |
| `tmux new -s name`    | New named session   |
| `tmux ls`             | List sessions       |
| `tmux attach -t name` | Attach to session   |
| `Prefix d`            | Detach from session |
| `Prefix $`            | Rename session      |
| `Prefix (`/`)`        | Prev / next session |

**Windows:**

| Shortcut       | Action             |
| -------------- | ------------------ |
| `Prefix c`     | New window         |
| `Prefix ,`     | Rename window      |
| `Prefix n`/`p` | Next / prev window |
| `Prefix 0-9`   | Switch to window N |
| `Prefix &`     | Kill window        |

**Panes:**

| Shortcut           | Action               |
| ------------------ | -------------------- |
| `Prefix %`         | Split vertically     |
| `Prefix "`         | Split horizontally   |
| `Prefix ←→↑↓`      | Navigate panes       |
| `Prefix z`         | Toggle pane zoom     |
| `Prefix {`/`}`     | Swap pane left/right |
| `Prefix x`         | Kill pane            |
| `Prefix Ctrl-↑↓←→` | Resize pane          |

**Copy Mode:**

| Shortcut   | Action              |
| ---------- | ------------------- |
| `Prefix [` | Enter copy mode     |
| `Space`    | Start selection     |
| `Enter`    | Copy selection      |
| `Prefix ]` | Paste               |
| `/`        | Search in copy mode |

#### Real-World Examples

1. **Development environment** — session with editor, server, and logs
2. **Pair programming** — share session with `tmux attach -t shared`
3. **Long-running remote task** — ssh + tmux, detach, reconnect next day
4. **Scripted session setup** — shell script that creates a full layout

#### Configuration (`~/.tmux.conf`)

```bash
# Change prefix to Ctrl-a (screen-style)
unbind C-b
set-option -g prefix C-a
bind-key C-a send-prefix

# Enable mouse support
set -g mouse on

# 0-indexed windows start at 1 (easier keyboard reach)
set -g base-index 1
setw -g pane-base-index 1

# Resize panes with Vim-style keys
bind -r H resize-pane -L 5
bind -r J resize-pane -D 5
bind -r K resize-pane -U 5
bind -r L resize-pane -R 5

# Split panes in current directory
bind '"' split-window -v -c "#{pane_current_path}"
bind '%' split-window -h -c "#{pane_current_path}"

# Vi copy mode
setw -g mode-keys vi
bind -T copy-mode-vi v send -X begin-selection
bind -T copy-mode-vi y send -X copy-selection-and-cancel

# Status bar
set -g status-style bg=colour235,fg=colour136
set -g status-left "#[fg=green]#S "
set -g status-right "#[fg=yellow]%H:%M"

# Reload config
bind r source-file ~/.tmux.conf \; display "Config reloaded!"
```

#### Pro Tips

1. Use `tmux-resurrect` plugin to persist sessions across reboots
2. Name your sessions (`-s name`) — unnamed sessions are a mess
3. Use `tmux new-session -A -s main` in `.bashrc`/`.zshrc` to auto-attach
4. `Prefix z` to zoom a pane for focused work, `Prefix z` again to unzoom
5. `Prefix [` then `/` to search scrollback buffer

---

## Milestone 5 — Linux Core Utils (Part 1 — Text Processing)

**Folder:** `linux-core-utils/README.md`
**Emoji:** 🐧
**Covers:** `grep`, `awk`, `sed`, `sort`, `uniq`, `cut`, `tr`, `wc`, `head`, `tail`

### Core Concepts

- **Unix philosophy** — each tool does one thing, output is text, pipe them together
- **Stdin/stdout/stderr** — the three streams; redirection (`>`, `>>`, `2>`, `&>`)
- **Pipes** — `|` connects stdout of one command to stdin of next
- **Exit codes** — 0 = success, non-zero = error; `$?` to check

Pipeline diagram:

```
cat file.log | grep "ERROR" | awk '{print $5}' | sort | uniq -c | sort -rn
     │               │               │              │        │         │
  read file     filter lines    extract field    sort    dedupe    sort by count
```

### grep

```bash
# Basic search
grep "pattern" file.txt
grep -r "pattern" ./src          # recursive
grep -i "pattern" file.txt       # case-insensitive
grep -n "pattern" file.txt       # show line numbers
grep -l "pattern" *.log          # only filenames
grep -v "pattern" file.txt       # invert match (exclude)
grep -c "pattern" file.txt       # count matches
grep -A 3 -B 3 "pattern" file.txt  # 3 lines after AND before

# Extended regex
grep -E "error|warning" file.txt       # OR
grep -E "^[0-9]{4}-[0-9]{2}" log.txt  # lines starting with date

# Fixed string (no regex interpretation — fastest for literal strings)
grep -F "literal.string" file.txt

# Count occurrences across files
grep -r "TODO" ./src --include="*.go" | wc -l
```

**Key flags table:**

| Flag   | Description                   |
| ------ | ----------------------------- |
| `-r`   | Recursive through directories |
| `-i`   | Case-insensitive              |
| `-n`   | Show line numbers             |
| `-v`   | Invert match                  |
| `-l`   | Print filenames only          |
| `-c`   | Count matching lines          |
| `-E`   | Extended regex                |
| `-F`   | Fixed string (literal)        |
| `-o`   | Print only matched part       |
| `-A N` | N lines after match           |
| `-B N` | N lines before match          |
| `-C N` | N lines before and after      |

### awk

```bash
# Print specific column
awk '{print $2}' file.txt             # column 2 (space-delimited)
awk -F: '{print $1}' /etc/passwd      # column 1, colon-delimited

# Conditional print
awk '$3 > 100 {print $1, $3}' data.txt

# Sum a column
awk '{sum += $2} END {print sum}' data.txt

# Print line count
awk 'END {print NR}' file.txt

# NR (record number), NF (field count)
awk 'NR==5 {print}' file.txt          # print line 5
awk 'NF > 3 {print}' file.txt         # lines with more than 3 fields

# BEGIN/END blocks
awk 'BEGIN {print "=== Report ==="} {print $1} END {print "=== Done ==="}' file.txt

# Multiple delimiters
awk -F'[,;]' '{print $1}' file.csv

# Nginx access log: count requests per IP
awk '{print $1}' access.log | sort | uniq -c | sort -rn | head -20
```

### sed

```bash
# Substitute (most common use)
sed 's/old/new/' file.txt           # first occurrence per line
sed 's/old/new/g' file.txt          # all occurrences (global)
sed 's/old/new/gi' file.txt         # global + case-insensitive
sed -i 's/old/new/g' file.txt       # in-place edit
sed -i.bak 's/old/new/g' file.txt   # in-place with backup

# Delete lines
sed '/pattern/d' file.txt           # delete matching lines
sed '5d' file.txt                   # delete line 5
sed '3,7d' file.txt                 # delete lines 3-7

# Print specific lines
sed -n '5p' file.txt                # print line 5 only
sed -n '3,7p' file.txt              # print lines 3-7

# Insert/append
sed '3i\inserted line' file.txt     # insert before line 3
sed '3a\appended line' file.txt     # append after line 3

# Multiple expressions
sed -e 's/foo/bar/g' -e 's/baz/qux/g' file.txt

# Remove empty lines
sed '/^$/d' file.txt

# Remove trailing whitespace
sed 's/[[:space:]]*$//' file.txt
```

### sort, uniq, cut, tr, wc, head, tail

```bash
# sort
sort file.txt                       # alphabetical
sort -n file.txt                    # numeric
sort -rn file.txt                   # reverse numeric
sort -k2 file.txt                   # sort by column 2
sort -t: -k3 -n /etc/passwd         # sort passwd by UID
sort -u file.txt                    # sort + deduplicate

# uniq (input must be sorted!)
uniq file.txt                       # remove adjacent duplicates
uniq -c file.txt                    # count occurrences
uniq -d file.txt                    # only show duplicates
uniq -u file.txt                    # only show unique lines

# cut
cut -d: -f1 /etc/passwd             # first field, colon delimiter
cut -d, -f1,3 data.csv              # fields 1 and 3
cut -c1-10 file.txt                 # characters 1-10

# tr
tr 'a-z' 'A-Z' < file.txt           # uppercase
tr -d '\n' < file.txt               # remove newlines
tr -s ' ' < file.txt                # squeeze multiple spaces to one
echo "hello world" | tr ' ' '\n'    # split on space

# wc
wc -l file.txt                      # line count
wc -w file.txt                      # word count
wc -c file.txt                      # byte count
ls | wc -l                          # count files in directory

# head / tail
head -20 file.txt                   # first 20 lines
tail -20 file.txt                   # last 20 lines
tail -f /var/log/app.log            # follow (live stream)
tail -f /var/log/app.log | grep ERROR   # live stream + filter
```

#### Real-World Examples

1. **Find all TODO/FIXME in a codebase** — grep recursive
2. **Parse Nginx access logs** — awk pipeline for top IPs and status codes
3. **Bulk rename pattern in config files** — sed in-place
4. **Extract unique values from CSV column** — cut + sort + uniq
5. **Monitor live errors** — tail -f + grep

---

## Milestone 6 — Linux Core Utils (Part 2 — File & System)

**Folder:** `linux-core-utils/file-system.md`
**Covers:** `find`, `xargs`, `ls`, `cp`, `mv`, `rm`, `mkdir`, `chmod`, `chown`, `ln`, `df`, `du`, `ps`, `kill`, `top/htop`, `curl`, `wget`, `tar`, `ssh` (basic), `rsync`

### find

```bash
# Find files by name
find /var/log -name "*.log"
find . -name "*.go" -not -path "*/vendor/*"

# Find by type
find . -type f          # files only
find . -type d          # directories only
find . -type l          # symlinks only

# Find by size
find . -size +10M       # larger than 10MB
find . -size -1k        # smaller than 1KB

# Find by modification time
find . -mtime -7        # modified in last 7 days
find . -newer file.txt  # modified more recently than file.txt

# Execute commands on results
find . -name "*.tmp" -delete
find . -name "*.go" -exec gofmt -w {} \;
find . -type f -name "*.log" | xargs rm

# Find and count
find ./src -name "*.js" | wc -l
```

### xargs

```bash
# Basic usage
find . -name "*.txt" | xargs cat
echo "one two three" | xargs -n1 echo       # one arg per line

# Parallel execution
find . -name "*.jpg" | xargs -P4 -I{} convert {} {}.png

# Handle filenames with spaces
find . -name "*.txt" -print0 | xargs -0 grep "pattern"

# Build command from args
cat servers.txt | xargs -I{} ssh {} "df -h"
```

### File permissions (chmod/chown)

```bash
# chmod symbolic
chmod +x script.sh              # add execute for all
chmod u+x,g-w file              # user +exec, group -write
chmod go= file                  # remove all perms from group and others

# chmod numeric (octal)
chmod 755 script.sh             # rwxr-xr-x
chmod 644 config.txt            # rw-r--r--
chmod 600 ~/.ssh/id_rsa         # rw------- (private key!)

# chown
chown alice file.txt
chown alice:developers file.txt
chown -R alice:developers ./project

# View permissions
ls -la file.txt
stat file.txt
```

**Permission cheat table:**

```
Octal | Binary | Symbolic | Meaning
  7   |  111   |   rwx    | read + write + execute
  6   |  110   |   rw-    | read + write
  5   |  101   |   r-x    | read + execute
  4   |  100   |   r--    | read only
  0   |  000   |   ---    | none
```

### Disk Usage

```bash
df -h                           # disk space (human readable)
df -h /home                     # specific mount

du -sh ./directory              # directory total size
du -sh * | sort -h              # sizes of all items, sorted
du -sh * | sort -rh | head -10  # top 10 largest items
ncdu .                          # interactive disk usage (install separately)
```

### Process Management

```bash
ps aux                          # all processes (BSD style)
ps aux | grep nginx
pgrep nginx                     # PIDs matching name
kill 1234                       # send SIGTERM (graceful)
kill -9 1234                    # send SIGKILL (force) — last resort
killall nginx                   # kill all processes by name
pkill -f "python script.py"     # kill by full command match

# Job control
command &                       # run in background
jobs                            # list background jobs
fg %1                           # bring job 1 to foreground
bg %1                           # resume job 1 in background
nohup command &                 # survive terminal close
```

#### Real-World Examples

1. **Find and delete old log files** — find + delete
2. **Sync a directory to a remote server** — rsync
3. **Archive and compress a project** — tar

---

## Milestone 7 — Docker

**Folder:** `docker/README.md`
**Emoji:** 🐳
**Version covered:** Docker Engine 24+, Docker Desktop 4.x

### Core Concepts

- **Image** — read-only blueprint (layers); built from a Dockerfile
- **Container** — running instance of an image; has its own filesystem, network, process space
- **Registry** — stores images; Docker Hub is the default (`docker.io`)
- **Volume** — persistent storage outside the container lifecycle
- **Network** — virtual network for containers to communicate

Object hierarchy:

```
Dockerfile ──build──► Image ──run──► Container
                          │
                     Registry (push/pull)

Container ──► Volumes (data persistence)
          └─► Networks (inter-container comms)
```

### Quick Reference

**Images:**

| Command                                   | Description                 |
| ----------------------------------------- | --------------------------- |
| `docker pull nginx:1.25`                  | Pull image from registry    |
| `docker images`                           | List local images           |
| `docker rmi nginx:1.25`                   | Remove image                |
| `docker build -t myapp:1.0 .`             | Build image from Dockerfile |
| `docker tag myapp:1.0 registry/myapp:1.0` | Tag image                   |
| `docker push registry/myapp:1.0`          | Push to registry            |
| `docker image prune`                      | Remove dangling images      |

**Containers:**

| Command                      | Description                    |
| ---------------------------- | ------------------------------ |
| `docker run nginx`           | Run container (foreground)     |
| `docker run -d nginx`        | Run detached                   |
| `docker run -it ubuntu bash` | Interactive shell              |
| `docker ps`                  | List running containers        |
| `docker ps -a`               | List all containers            |
| `docker stop myapp`          | Graceful stop (SIGTERM)        |
| `docker kill myapp`          | Force stop (SIGKILL)           |
| `docker rm myapp`            | Remove stopped container       |
| `docker rm -f myapp`         | Force remove running container |

### Common Commands

**Running containers:**

```bash
# Basic run
docker run nginx

# Detached, named, port-mapped
docker run -d --name webserver -p 8080:80 nginx:1.25

# With environment variables
docker run -d \
  --name postgres \
  -e POSTGRES_PASSWORD=secret \
  -e POSTGRES_DB=myapp \
  -p 5432:5432 \
  postgres:16

# With volume mount
docker run -d \
  --name postgres \
  -v pgdata:/var/lib/postgresql/data \
  -e POSTGRES_PASSWORD=secret \
  postgres:16

# With bind mount (local dir → container)
docker run -d \
  -v $(pwd)/html:/usr/share/nginx/html:ro \
  -p 8080:80 \
  nginx

# Resource limits
docker run -d \
  --memory="512m" \
  --cpus="0.5" \
  myapp:1.0
```

**Inspecting and debugging:**

```bash
docker logs myapp                    # view logs
docker logs -f myapp                 # follow logs live
docker logs --tail 100 myapp         # last 100 lines

docker exec -it myapp bash           # shell into running container
docker exec myapp cat /etc/nginx/nginx.conf   # run single command

docker inspect myapp                 # full JSON metadata
docker inspect myapp | jq '.[0].NetworkSettings.IPAddress'

docker stats                         # live resource usage
docker top myapp                     # processes inside container
```

**Volumes:**

```bash
docker volume create pgdata
docker volume ls
docker volume inspect pgdata
docker volume rm pgdata
docker volume prune             # remove all unused volumes
```

**Networks:**

```bash
docker network create mynet
docker network ls
docker run -d --name app --network mynet myapp:1.0
docker run -d --name db  --network mynet postgres:16
# 'app' can reach 'db' by hostname 'db'
```

**Cleanup:**

```bash
docker system prune              # remove stopped containers + dangling images
docker system prune -a           # ⚠️ also remove unused images
docker system df                 # show disk usage
```

### Dockerfile Best Practices

```dockerfile
# ✅ Good Dockerfile
FROM node:20-alpine AS base

# Set working directory
WORKDIR /app

# Copy dependency manifests first (layer cache optimization)
COPY package*.json ./
RUN npm ci --only=production

# Copy application code
COPY . .

# Non-root user for security
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

# Document the port (informational only)
EXPOSE 3000

# Use exec form (not shell form) for proper signal handling
CMD ["node", "server.js"]
```

**Multi-stage build (keep images small):**

```dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine AS production
COPY --from=builder /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Real-World Examples

1. **Containerize a Node.js app** — Dockerfile + build + run
2. **Set up a local Postgres database** — run with volume for persistence
3. **Debug a container that won't start** — docker logs + docker run interactively
4. **Clean up disk space** — system prune workflow

---

## Milestone 8 — Docker Compose

**Folder:** `docker-compose/README.md`
**Emoji:** 🐙
**Version covered:** Compose v2 (`docker compose`, not `docker-compose`)

### Core Concepts

- **Service** — a container definition; maps to one container (or multiple with replicas)
- **`docker-compose.yml`** — declarative manifest for a multi-container app
- **Compose v2** — now built into Docker CLI as `docker compose` (no hyphen)

### Anatomy of `compose.yml`

```yaml
# compose.yml (or docker-compose.yml)
name: myapp

services:
  web: # service name
    build:
      context: . # build from local Dockerfile
      dockerfile: Dockerfile
    image: myapp:dev # OR use a pre-built image
    ports:
      - "8080:3000" # host:container
    environment:
      - NODE_ENV=development
      - DATABASE_URL=postgres://postgres:secret@db:5432/myapp
    env_file:
      - .env # load from .env file
    depends_on:
      db:
        condition: service_healthy # wait for healthcheck
    volumes:
      - .:/app # bind mount for hot-reload
      - /app/node_modules # anonymous volume to exclude node_modules
    restart: unless-stopped

  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: myapp
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: secret
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    volumes:
      - redisdata:/data

volumes:
  pgdata:
  redisdata:

networks:
  default:
    name: myapp-network
```

### Common Commands

```bash
# Start services
docker compose up                    # foreground, build if needed
docker compose up -d                 # detached
docker compose up --build            # force rebuild
docker compose up web                # start specific service only

# Stop services
docker compose down                  # stop + remove containers/networks
docker compose down -v               # also remove volumes ⚠️
docker compose stop                  # stop without removing

# Logs
docker compose logs                  # all services
docker compose logs -f web           # follow web service
docker compose logs --tail=50

# Run commands
docker compose exec web sh           # shell into running service
docker compose run --rm web npm test # one-off command, auto-remove container

# Scale
docker compose up -d --scale web=3  # run 3 web instances

# Status
docker compose ps
docker compose top

# Config validation
docker compose config                # print merged/resolved config
```

### Real-World Examples

1. **Full-stack web app** — Node.js + Postgres + Redis
2. **Override files** — `compose.yml` + `compose.override.yml` for dev vs prod
3. **Multi-environment setup** — using `--env-file` and `--file` flags

---

## Milestone 9 — SSH

**Folder:** `ssh/README.md`
**Emoji:** 🔐
**Version covered:** OpenSSH 9.x

### Core Concepts

- **Public/private key pair** — private stays on your machine; public goes to the server
- **`~/.ssh/config`** — client config: define aliases, per-host settings
- **`authorized_keys`** — server-side list of allowed public keys
- **Agent forwarding** — use local keys on remote hosts (useful for git over SSH)
- **Tunneling** — route traffic through an SSH connection

### Quick Reference

**Connecting:**

| Command                              | Description                  |
| ------------------------------------ | ---------------------------- |
| `ssh user@host`                      | Basic connection             |
| `ssh user@host -p 2222`              | Custom port                  |
| `ssh -i ~/.ssh/id_ed25519 user@host` | Specific key                 |
| `ssh -v user@host`                   | Verbose (debug)              |
| `ssh host`                           | Use alias from ~/.ssh/config |

**Key Management:**

| Command                                     | Description                        |
| ------------------------------------------- | ---------------------------------- |
| `ssh-keygen -t ed25519 -C "me@example.com"` | Generate key (ed25519 recommended) |
| `ssh-copy-id user@host`                     | Install public key on server       |
| `ssh-add ~/.ssh/id_ed25519`                 | Add key to agent                   |
| `ssh-add -l`                                | List keys in agent                 |
| `cat ~/.ssh/id_ed25519.pub`                 | Print public key                   |

### Common Commands

**Generating keys:**

```bash
# Modern: ed25519 (faster, smaller, more secure than RSA)
ssh-keygen -t ed25519 -C "alice@company.com" -f ~/.ssh/id_ed25519

# Legacy RSA (when ed25519 unsupported)
ssh-keygen -t rsa -b 4096 -C "alice@company.com"
```

**Copying files (scp / rsync):**

```bash
# scp: simple copy
scp file.txt alice@server:/home/alice/
scp alice@server:/var/log/app.log ./
scp -r ./project alice@server:/opt/apps/

# rsync: efficient sync (only transfers diffs)
rsync -avz ./project/ alice@server:/opt/apps/project/
rsync -avz --delete ./project/ alice@server:/opt/apps/project/   # mirror
rsync -avzn ./project/ alice@server:/opt/apps/project/           # dry run
```

**Tunneling:**

```bash
# Local port forwarding: forward localhost:8080 → remote_server:80
ssh -L 8080:localhost:80 alice@server
# Now: curl http://localhost:8080 → hits server:80

# Remote port forwarding: expose local port on remote server
ssh -R 9090:localhost:3000 alice@server
# Now: remote:9090 → your machine:3000

# SOCKS proxy
ssh -D 1080 alice@server
# Set browser proxy to localhost:1080

# Jump host (bastion)
ssh -J alice@bastion alice@internal-server
```

### `~/.ssh/config` — Full Annotated Example

```
# ~/.ssh/config

# Default settings for all hosts
Host *
  AddKeysToAgent yes
  IdentityFile ~/.ssh/id_ed25519
  ServerAliveInterval 60
  ServerAliveCountMax 3

# Work server alias
Host work
  HostName 203.0.113.50
  User alice
  Port 22
  IdentityFile ~/.ssh/work_ed25519

# Jump through a bastion host
Host internal
  HostName 10.0.1.50
  User alice
  ProxyJump alice@bastion.company.com

# GitHub
Host github.com
  User git
  IdentityFile ~/.ssh/id_ed25519
  AddKeysToAgent yes
```

### Security Best Practices

- ✅ Use **ed25519** keys, not RSA 2048
- ✅ **Always** set a passphrase on your private key
- ✅ Use `~/.ssh/config` — don't type IPs and flags each time
- ✅ Disable password auth on servers (`PasswordAuthentication no` in `sshd_config`)
- ✅ Use `fail2ban` or similar on servers
- ⚠️ **Never** share your private key (`id_ed25519`) — only share `id_ed25519.pub`
- ⚠️ `chmod 700 ~/.ssh` and `chmod 600 ~/.ssh/*` — SSH ignores insecure permissions

---

## Milestone 10 — Regex

**Folder:** `regex/README.md`
**Emoji:** 🔍
**Note:** This is a **universal reference** — cover PCRE/ERE/BRE differences

### Core Concepts

- **Literal match** — most characters match themselves
- **Metacharacter** — special meaning: `. * + ? ^ $ [] {} | () \`
- **Quantifier** — how many times to match: `*`, `+`, `?`, `{n,m}`
- **Anchor** — position in string: `^` (start), `$` (end), `\b` (word boundary)
- **Character class** — `[abc]`, `[a-z]`, `[^abc]` (negated)
- **Group** — `(abc)` capture group; `(?:abc)` non-capturing
- **Alternation** — `cat|dog` matches either

### Metacharacter Reference

| Pattern | Matches                      |
| ------- | ---------------------------- |
| `.`     | Any character except newline |
| `\d`    | Digit `[0-9]`                |
| `\D`    | Non-digit                    |
| `\w`    | Word char `[a-zA-Z0-9_]`     |
| `\W`    | Non-word char                |
| `\s`    | Whitespace `[ \t\n\r\f\v]`   |
| `\S`    | Non-whitespace               |
| `\b`    | Word boundary                |
| `^`     | Start of line                |
| `$`     | End of line                  |
| `\n`    | Newline                      |
| `\t`    | Tab                          |

### Quantifiers

| Quantifier | Meaning            |
| ---------- | ------------------ |
| `*`        | 0 or more (greedy) |
| `+`        | 1 or more (greedy) |
| `?`        | 0 or 1 (optional)  |
| `{n}`      | Exactly n          |
| `{n,}`     | n or more          |
| `{n,m}`    | Between n and m    |
| `*?`       | 0 or more (lazy)   |
| `+?`       | 1 or more (lazy)   |

### Common Patterns (Copy-Paste Ready)

```
Email:          [a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}
URL:            https?://[^\s/$.?#].[^\s]*
IPv4:           \b(?:\d{1,3}\.){3}\d{1,3}\b
UUID:           [0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}
Date YYYY-MM-DD: \d{4}-(?:0[1-9]|1[0-2])-(?:0[1-9]|[12]\d|3[01])
Time HH:MM:SS:  (?:[01]\d|2[0-3]):[0-5]\d:[0-5]\d
Semver:         v?\d+\.\d+\.\d+(?:-[a-zA-Z0-9.]+)?
Hex color:      #[0-9a-fA-F]{3,6}
```

### Flavor Differences (BRE vs ERE vs PCRE)

| Feature       | BRE (grep) | ERE (grep -E) | PCRE (grep -P, JS, Python) |
| ------------- | ---------- | ------------- | -------------------------- |
| Groups        | `\(...\)`  | `(...)`       | `(...)`                    |
| Alternation   | `\|`       | `\|`          | `\|`                       |
| `+`quantifier | `\+`       | `+`           | `+`                        |
| `?`quantifier | `\?`       | `?`           | `?`                        |
| Lookahead     | ✗          | ✗             | `(?=...)`                  |
| Lookbehind    | ✗          | ✗             | `(?<=...)`                 |
| Named groups  | ✗          | ✗             | `(?P<name>...)`            |

### Real-World Examples

1. **Validate an email in bash with grep**
2. **Extract all IP addresses from a log file**
3. **Regex in Python** — `re.match`, `re.findall`, `re.sub`
4. **Regex in JavaScript** — `/pattern/flags`, named groups
5. **Lookahead/lookbehind** — match word only if followed by something

---

## Milestone 11 — Homebrew

**Folder:** `homebrew/README.md`
**Emoji:** 🍺
**Platform:** macOS and Linux

### Core Concepts

- **Formula** — a Ruby script defining how to install a CLI tool
- **Cask** — a formula for macOS GUI applications
- **Tap** — a third-party formula repository
- **Cellar** — where Homebrew installs packages (`/opt/homebrew/Cellar` on Apple Silicon)

### Quick Reference

```bash
# Install / uninstall
brew install git
brew uninstall git
brew install --cask firefox        # GUI app

# Update
brew update                        # update Homebrew itself
brew upgrade                       # upgrade all outdated packages
brew upgrade git                   # upgrade specific package

# Search
brew search node
brew info node                     # details, deps, version

# Maintenance
brew list                          # installed formulae
brew list --cask                   # installed casks
brew outdated                      # list outdated packages
brew cleanup                       # remove old versions
brew doctor                        # diagnose issues
brew autoremove                    # remove unused dependencies

# Taps
brew tap homebrew/cask-fonts       # add a tap
brew tap-info hashicorp/tap
brew install hashicorp/tap/terraform
```

### Brewfile (reproducible setup)

```ruby
# Brewfile — commit this to your dotfiles repo

tap "homebrew/cask-fonts"
tap "homebrew/cask-versions"

# Development tools
brew "git"
brew "neovim"
brew "tmux"
brew "fzf"
brew "ripgrep"
brew "jq"
brew "httpie"
brew "gh"                          # GitHub CLI

# Languages & runtimes
brew "node"
brew "python"
brew "go"

# DevOps
brew "terraform"
brew "kubectl"
brew "helm"
brew "awscli"

# GUI Apps (casks)
cask "iterm2"
cask "visual-studio-code"
cask "docker"
cask "postman"

# Fonts
cask "font-jetbrains-mono-nerd-font"
```

```bash
# Install everything from Brewfile
brew bundle install

# Check what's missing
brew bundle check

# Generate Brewfile from current installs
brew bundle dump
```

---

## Milestone 12 — Terraform

**Folder:** `terraform/README.md`
**Emoji:** 🏗️
**Version covered:** Terraform / OpenTofu 1.6+

### Core Concepts

- **Provider** — plugin to manage a cloud/service API (AWS, GCP, GitHub...)
- **Resource** — a piece of infrastructure to create/manage
- **Data source** — read existing infrastructure (without managing it)
- **State** — JSON file tracking what Terraform manages; can be local or remote (S3, Terraform Cloud)
- **Plan** — diff between desired state (config) and current state
- **Workspace** — isolated state per environment (dev/staging/prod)
- **Module** — reusable group of resources

Workflow diagram:

```
Write .tf files ──► terraform init ──► terraform plan ──► terraform apply
                                                              │
                                                         terraform.tfstate
                                                              │
                                                       terraform destroy
```

### Quick Reference

```bash
# Core workflow
terraform init             # download providers, set up backend
terraform validate         # check syntax and config validity
terraform fmt              # format .tf files
terraform plan             # preview changes
terraform plan -out=tfplan # save plan to file
terraform apply            # apply changes (asks for confirmation)
terraform apply tfplan     # apply saved plan (no confirmation prompt)
terraform destroy          # destroy all managed resources ⚠️

# State
terraform show             # human-readable state
terraform state list       # list resources in state
terraform state show aws_instance.web   # show one resource
terraform state rm aws_instance.web     # remove from state (without destroying)
terraform import aws_instance.web i-1234abcd  # import existing resource

# Workspaces
terraform workspace list
terraform workspace new staging
terraform workspace select staging
terraform workspace show

# Other
terraform output                        # show output values
terraform graph | dot -Tsvg > graph.svg # dependency graph
terraform providers                     # list providers in config
```

### HCL Syntax Cheat Sheet

```hcl
# Variables
variable "region" {
  description = "AWS region"
  type        = string
  default     = "us-east-1"
}

# Locals
locals {
  env    = "production"
  prefix = "myapp-${local.env}"
}

# Resource
resource "aws_s3_bucket" "assets" {
  bucket = "${local.prefix}-assets"
  tags   = { Environment = local.env }
}

# Data source
data "aws_ami" "ubuntu" {
  most_recent = true
  owners      = ["099720109477"]   # Canonical
  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-22.04-amd64-server-*"]
  }
}

# Output
output "bucket_arn" {
  description = "S3 bucket ARN"
  value       = aws_s3_bucket.assets.arn
}

# Conditionals
resource "aws_instance" "bastion" {
  count = var.create_bastion ? 1 : 0
  ami   = data.aws_ami.ubuntu.id
}

# for_each
resource "aws_iam_user" "team" {
  for_each = toset(["alice", "bob", "carol"])
  name     = each.key
}

# Dynamic blocks
resource "aws_security_group" "web" {
  dynamic "ingress" {
    for_each = var.allowed_ports
    content {
      from_port   = ingress.value
      to_port     = ingress.value
      protocol    = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
    }
  }
}
```

### Real-World Examples

1. **Provision an EC2 instance with security group**
2. **Remote state in S3 with DynamoDB locking**
3. **Module usage** — call a module, pass variables
4. **Workspace-based environment separation**

### Pro Tips

1. Always use `-out=tfplan` then `apply tfplan` — no surprises in CI
2. Never edit `.tfstate` by hand — use `terraform state` commands
3. Use `terraform validate` in CI before plan
4. Lock provider versions with `required_providers`
5. Remote state is mandatory for teams — never local state in production

---

## Milestone 13 — apt

**Folder:** `apt/README.md`
**Emoji:** 📦
**Covers:** apt, apt-get, dpkg
**Platform:** Debian, Ubuntu, and derivatives

### Core Concepts

- **Package** — a `.deb` file containing binaries, configs, metadata
- **Repository** — a server hosting packages; configured in `/etc/apt/sources.list.d/`
- **`apt`** — high-level frontend (human-friendly, use this in terminals)
- **`apt-get`** — lower-level, scripting-stable (use in scripts)
- **`dpkg`** — lowest-level; directly installs `.deb` files

### Quick Reference

```bash
# Package management
sudo apt update                     # refresh package index
sudo apt upgrade                    # upgrade all upgradable packages
sudo apt full-upgrade               # upgrade + handle dependency changes
sudo apt install nginx              # install package
sudo apt install nginx=1.24.0-*     # install specific version
sudo apt remove nginx               # remove (keep config files)
sudo apt purge nginx                # remove + delete config files
sudo apt autoremove                 # remove unused dependencies

# Search and info
apt search nginx                    # search packages
apt show nginx                      # package details
apt list --installed                # all installed packages
apt list --upgradable               # packages with available updates

# dpkg (low-level)
dpkg -i package.deb                 # install local .deb file
dpkg -l                             # list all installed
dpkg -l | grep nginx
dpkg -L nginx                       # list files installed by package
dpkg -S /usr/bin/nginx              # which package owns this file

# Repositories
cat /etc/apt/sources.list
ls /etc/apt/sources.list.d/
sudo add-apt-repository ppa:deadsnakes/ppa   # add PPA (Ubuntu)
sudo apt-key add pubkey.gpg                  # add signing key (legacy)

# apt-get (use in scripts)
apt-get update -qq
apt-get install -y nginx            # -y = non-interactive
apt-get install -y --no-install-recommends nginx   # minimal install
```

### Adding a Third-Party Repository (Modern Way)

```bash
# Example: Add Docker's official repo
# 1. Download signing key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# 2. Add repository
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" \
  | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 3. Install
sudo apt update
sudo apt install docker-ce
```

### Real-World Examples

1. **Set up a fresh Ubuntu server** — update + essential packages + unattended-upgrades
2. **Pin a package version** — prevent specific packages from upgrading
3. **Install a `.deb` file and fix dependencies** — dpkg -i + apt install -f

---

## Style & Formatting Rules

These rules apply to **every** file in the repository.

### Markdown Rules

1. **Language tags on every code block** — always specify `bash`, `yaml`, `hcl`, `vim`, `conf`, etc.
2. **Table headers** — always present; always bold-free (markdown renders them bold)
3. **No trailing whitespace**
4. **One blank line** before and after headings, code blocks, and tables
5. **Heading hierarchy** — never skip levels (no H1 → H3)
6. **Emoji in headings** — use one emoji per H2 section heading for visual scanning
7. **Bold** for UI/key terms on first introduction; `code` for all commands, flags, paths, and values
8. **Callout boxes using blockquotes:**
   - `> 💡` — tip
   - `> ⚠️` — warning
   - `> ❌` — anti-pattern / common mistake
   - `> ✅` — recommended practice

### Naming Conventions

| Type                     | Convention                  | Example                      |
| ------------------------ | --------------------------- | ---------------------------- |
| Folder names             | lowercase, hyphen-separated | `docker-compose/`            |
| File names               | lowercase                   | `README.md`,`advanced.md`    |
| Placeholder values       | SCREAMING_SNAKE             | `YOUR_BUCKET_NAME`           |
| Realistic example values | lowercase                   | `my-app`,`alice`,`webserver` |

### Command Example Rules

- Use **realistic** names (not `mycontainer`, `yourserver`, `example.com`)
- Use `alice` for usernames, `company.com` for domains, `webserver` for container names
- Every destructive command gets a `# ⚠️` or `[!]` annotation
- Multi-line commands use `\` with consistent 2-space indent for flags

---

## Quality Checklist

Run this checklist before marking any milestone as done.

### Content

- [ ] All `[REQUIRED]` template sections are present
- [ ] Core Concepts explains the mental model, not just definitions
- [ ] Quick Reference table covers all major sub-commands
- [ ] Each Common Commands section has real, runnable examples
- [ ] At least 3 Real-World Examples are provided
- [ ] Pro Tips section has at least 5 tips including one anti-pattern
- [ ] Resources section has at least 3 curated links

### Formatting

- [ ] All code blocks have a language tag
- [ ] No placeholder values like `<your-value>` left
- [ ] Headings follow correct hierarchy (no skipped levels)
- [ ] Tables are used for reference data; prose for explanations
- [ ] Emoji used in H2 headings for visual hierarchy
- [ ] Callout blockquotes used for tips/warnings
- [ ] Destructive commands annotated with ⚠️

### Technical Accuracy

- [ ] All commands tested or verified against official docs
- [ ] Version-specific notes added where behavior differs
- [ ] Flags and options described accurately
- [ ] No commands that require unspecified setup to run

### Repository

- [ ] File lives in the correct folder
- [ ] Root README quick-nav table updated
- [ ] `markdownlint` passes with zero errors
- [ ] Links to official docs are valid

---

_End of Implementation Plan_
_Version: 1.0 | Tools: 11 | Milestones: 14 (0-13)_
