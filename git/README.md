# 🔀 Git Cheat Sheet

Git is a distributed version control system where every clone is a full repository with complete history. Git is the universal language of software collaboration — understanding it deeply makes you a more effective developer and teammate. Version: 2.x (2024)

[![Official Docs](https://img.shields.io/badge/docs-git--scm.com-orange)](https://git-scm.com/doc)
[![License](https://img.shields.io/badge/license-MIT-green)](../LICENSE)

---

## Table of Contents

- [Installation](#-installation)
- [Core Concepts](#-core-concepts)
- [Quick Reference](#-quick-reference)
- [Common Commands](#-common-commands)
- [Real-World Examples](#-real-world-examples)
- [Configuration](#-configuration)
- [Pro Tips](#-pro-tips)
- [Troubleshooting](#-troubleshooting)
- [Resources](#-resources)

---

## 📦 Installation

| Platform | Command |
| -------- | ------- |
| macOS (Homebrew) | `brew install git` |
| macOS (Xcode tools) | `xcode-select --install` |
| Ubuntu/Debian | `sudo apt install git` |
| Windows | Git for Windows: https://git-scm.com or `winget install Git.Git` |
| Windows (WSL) | same as Ubuntu |

Verify your installation:

```bash
git --version
```

---

## 🧠 Core Concepts

**Repository** — A Git repository consists of three areas: the working tree (files on disk), the index (staging area for the next commit), and the object store (all snapshots ever recorded).

**Commit** — A commit is a snapshot of the entire project at a point in time, not a diff. Each commit is identified by a SHA-1 hash (e.g., `abc1234`). This makes history immutable and verifiable.

**Branch** — A branch is a lightweight, movable pointer to a commit. `HEAD` is a special pointer that indicates which branch (or commit) is currently checked out.

**Remote** — A named URL pointing to another copy of the repository. `origin` is the conventional name for the primary remote; `upstream` is often used for the original repo when working from a fork.

### Three Trees

```
Working Directory  ──git add──►  Index (Staging)  ──git commit──►  HEAD
     (files on disk)               (next commit)                (last commit)
```

### Fast-Forward vs Merge Commit vs Rebase

```
Before:                After fast-forward:      After --no-ff merge:
main─A─B                main─A─B─C─D             main─A─B───M
         \                                                \ /
feat──C─D               (no merge commit)          feat──C─D
```

**Fast-forward** happens when the target branch has no new commits since the feature branch diverged — Git simply moves the pointer forward. `--no-ff` forces a merge commit even when a fast-forward is possible, preserving the history of the feature branch.

**Rebase** replays your commits on top of a new base commit, rewriting their SHAs. This produces a clean, linear history but should never be done on branches that others have based work on.

---

_Content being added — work in progress._
