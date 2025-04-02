# Contributing to CLI Cheat Sheets

Thank you for taking the time to contribute! This guide covers everything you need to know to add or improve a cheat sheet.

---

## Table of Contents

1. [Code of Conduct](#1-code-of-conduct)
2. [Getting Started](#2-getting-started)
3. [Adding a New Cheat Sheet](#3-adding-a-new-cheat-sheet)
4. [Improving an Existing Sheet](#4-improving-an-existing-sheet)
5. [Style Guide](#5-style-guide)
6. [Markdown Rules](#6-markdown-rules)
7. [Pull Request Process](#7-pull-request-process)
8. [Review Process](#8-review-process)
9. [Recognition](#9-recognition)

---

## 1. Code of Conduct

This project follows the [Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md).
By participating, you agree to uphold its standards. Please report unacceptable behaviour to the maintainers.

---

## 2. Getting Started

### Prerequisites

- Git installed and configured
- A GitHub account
- A markdown editor (VS Code with the markdownlint extension is recommended)

### Fork and clone

```bash
# Fork the repo on GitHub, then:
git clone https://github.com/YOUR_USERNAME/cheat-sheets.git
cd cheat-sheets

# Add the upstream remote so you can sync later
git remote add upstream https://github.com/atefs/cheat-sheets.git
```

### Branch naming

Use descriptive, kebab-case branch names that start with one of these prefixes:

| Prefix    | Use for                                |
| --------- | -------------------------------------- |
| `feat/`   | New cheat sheet or major new section   |
| `fix/`    | Correcting a wrong command or example  |
| `docs/`   | Typos, wording, formatting only        |
| `chore/`  | CI, tooling, repo infrastructure       |

**Examples:**

```bash
git switch -c feat/add-curl-cheatsheet
git switch -c fix/git-rebase-example
git switch -c docs/improve-docker-pro-tips
```

---

## 3. Adding a New Cheat Sheet

New cheat sheets go in their own top-level folder and must follow the master template.

### Step-by-step

1. **Open a proposal issue** using the *New cheat sheet* issue template before writing anything.
   This avoids duplicated effort and lets maintainers give early feedback.

2. **Create the folder and file:**

   ```bash
   mkdir curl
   cp _template/README.md curl/README.md
   ```

3. **Fill in every `[REQUIRED]` section** in the template.
   Do not leave placeholder text like `<tool>` or `YOUR_VALUE` in the final file.

4. **Add a row to the root `README.md`** quick-nav table.

5. **Test all commands** before submitting. If a command requires a running service or specific environment, add a note.

6. **Open a pull request** and complete the PR checklist below.

---

## 4. Improving an Existing Sheet

Good improvements:

- Fix a command that is wrong, outdated, or uses a deprecated flag
- Add a missing real-world scenario that covers a common task
- Clarify an explanation that is ambiguous
- Add version-specific notes (e.g., "behaviour changed in Git 2.28")

Not what we're looking for:

- Rewrites that change working content just to match your style preference
- Adding obscure flags that less than 5% of users will ever need
- Removing content because you personally don't use it

If you're unsure, open a *Improvement suggestion* issue first.

---

## 5. Style Guide

All cheat sheets **must** follow the master template structure (`_template/README.md`):

| Section               | Required? | Notes                                              |
| --------------------- | --------- | -------------------------------------------------- |
| H1 heading + badges   | Yes       | Emoji + tool name, official docs badge             |
| Table of Contents     | Yes       | Anchored links to all sections                     |
| Installation          | Yes       | One table row per platform                         |
| Core Concepts         | Yes       | Mental model, diagrams where helpful               |
| Quick Reference       | Yes       | Dense tables, grouped by sub-topic                 |
| Common Commands       | Yes       | Flags table for each group                         |
| Real-World Examples   | Yes       | 3–6 complete, copy-paste-ready scenarios           |
| Configuration         | Optional  | Include if the tool has a meaningful config file   |
| Pro Tips              | Yes       | 5–10 tips, at least one anti-pattern warning       |
| Troubleshooting       | Optional  | Required for tools with common gotchas             |
| Resources             | Yes       | Official docs, books, interactive tools            |

**Writing style:**

- Present tense, second person ("Run `git status` to see...")
- One idea per sentence
- Use inline code for all commands, flags, paths, and filenames
- Use realistic values in examples — not `<my-container>` but `web-api`
- Use `> ⚠️` blockquotes for warnings and `> 💡` for tips

---

## 6. Markdown Rules

This repo uses [markdownlint](https://github.com/DavidAnson/markdownlint) with the config in [`.markdownlint.yml`](.markdownlint.yml).

Key rules:

- All code blocks **must** have a language tag: ` ```bash `, ` ```yaml `, ` ```vim `, etc.
- Line length limit: **120 characters** (not enforced inside code blocks or tables)
- No bare URLs — always use `[link text](url)` format
- Headings must be surrounded by blank lines
- No trailing spaces

### Run markdownlint locally

```bash
# Install (requires Node.js)
npm install -g markdownlint-cli2

# Check a single file
markdownlint-cli2 git/README.md

# Check everything
markdownlint-cli2 "**/*.md"
```

---

## 7. Pull Request Process

Before opening a PR, work through this checklist:

- [ ] Followed the cheat sheet template structure
- [ ] All code blocks specify a language (`` `bash ``, `` `yaml ``, etc.)
- [ ] Examples are real and tested
- [ ] No placeholder values left (e.g. `YOUR_VALUE` is fine, `<value>` is not)
- [ ] Added tool to root README quick-nav table (if new sheet)
- [ ] Ran markdownlint locally and resolved all warnings
- [ ] Spell-checked

### Commit messages

Use the [Conventional Commits](https://www.conventionalcommits.org/) format:

```
feat(curl): add initial cheat sheet
fix(git): correct rebase --onto example
docs(docker): clarify multi-stage build section
```

### PR description

Fill in the pull request template completely. Link any related issues with `Fixes #123` or `Related to #123`.

---

## 8. Review Process

Maintainers look for:

1. **Technical accuracy** — commands must work as written on the documented version
2. **Template compliance** — all required sections are present and follow the structure
3. **Code block quality** — language tags, realistic values, comments on non-obvious lines
4. **Conciseness** — no redundant content; every line earns its place
5. **markdownlint clean** — CI must pass before merging

Expect at least one round of review comments. Be responsive — PRs that go 14+ days without a response from the author may be closed.

---

## 9. Recognition

All contributors are acknowledged in the root `README.md` under the **Contributors** section (auto-generated via the [all-contributors](https://allcontributors.org/) spec). When your first PR is merged, a maintainer will add you to the list.

Thank you for making this resource better for everyone!
