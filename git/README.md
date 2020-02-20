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

> ⚠️ Never rebase shared/public branches. Rebasing rewrites commit SHAs, which causes diverging histories for anyone who has pulled those commits.

---

## ⚡ Quick Reference

### Setup

| Command | Description |
| ------- | ----------- |
| `git config --global user.name "Alice"` | Set global username |
| `git config --global user.email "alice@example.com"` | Set global email |
| `git config --global core.editor "vim"` | Set default editor |
| `git config --global init.defaultBranch main` | Default branch name |
| `git config --list` | Show all config |
| `git config --global pull.rebase true` | Rebase on pull |

### Staging

| Command | Description |
| ------- | ----------- |
| `git init` | Init new repo |
| `git clone https://github.com/org/repo.git` | Clone repo |
| `git status` | Show working tree status |
| `git add file.txt` | Stage a file |
| `git add .` | Stage all changes |
| `git add -p` | Interactive/patch staging |
| `git diff` | Unstaged changes |
| `git diff --staged` | Staged changes |

### Committing

| Command | Description |
| ------- | ----------- |
| `git commit -m "feat: add login page"` | Commit with message |
| `git commit --amend --no-edit` | Amend last commit (keep message) |
| `git commit --amend -m "new message"` | Amend last commit message |

### Branching

| Command | Description |
| ------- | ----------- |
| `git branch` | List local branches |
| `git branch -a` | List all including remotes |
| `git branch feature/login` | Create branch |
| `git switch feature/login` | Switch to branch |
| `git switch -c feature/login` | Create + switch (modern) |
| `git branch -d feature/login` | Delete merged branch |
| `git branch -D feature/login` | Force delete branch # ⚠️ |
| `git branch -m old-name new-name` | Rename branch |

> 💡 `git switch` is the modern replacement for `git checkout` for branch operations. The legacy equivalent is `git checkout -b feature/login` to create and switch.

### Merging & Rebasing

| Command | Description |
| ------- | ----------- |
| `git merge feature/login` | Merge branch |
| `git merge --no-ff feature/login` | Always create merge commit |
| `git merge --squash feature/login` | Squash all commits into one |
| `git rebase main` | Rebase current branch onto main |
| `git rebase -i HEAD~3` | Interactive rebase last 3 commits |
| `git cherry-pick abc1234` | Apply specific commit |

### Remotes

| Command | Description |
| ------- | ----------- |
| `git remote add origin git@github.com:org/repo.git` | Add remote |
| `git remote -v` | List remotes |
| `git fetch origin` | Fetch without merging |
| `git pull origin main` | Fetch + merge |
| `git push origin feature/login` | Push branch |
| `git push -u origin feature/login` | Push + set upstream |
| `git push origin feature/login --force-with-lease` | Safe force push # ⚠️ |

### Undoing

| Command | Description |
| ------- | ----------- |
| `git restore file.txt` | Discard working dir changes |
| `git restore --staged file.txt` | Unstage file |
| `git revert HEAD` | Safe undo (creates new commit) |
| `git reset --soft HEAD~1` | Undo commit, keep staged |
| `git reset --mixed HEAD~1` | Undo commit, keep unstaged |
| `git reset --hard HEAD~1` | Undo commit + discard changes # ⚠️ |

### Stash

| Command | Description |
| ------- | ----------- |
| `git stash` | Stash current changes |
| `git stash push -m "WIP: login form"` | Stash with name |
| `git stash list` | List stashes |
| `git stash pop` | Apply latest + remove from stash |
| `git stash apply stash@{2}` | Apply specific stash |
| `git stash drop stash@{0}` | Remove specific stash |
| `git stash clear` | Remove all stashes # ⚠️ |

### Tags

| Command | Description |
| ------- | ----------- |
| `git tag v1.0.0` | Lightweight tag |
| `git tag -a v1.0.0 -m "Release 1.0"` | Annotated tag |
| `git push origin v1.0.0` | Push tag |
| `git push origin --tags` | Push all tags |
| `git tag -d v1.0.0` | Delete local tag |

### Log & Diff

| Command | Description |
| ------- | ----------- |
| `git log --oneline --graph --all` | Compact graph view |
| `git log --author="Alice" --since="2 weeks ago"` | Filter by author/date |
| `git diff HEAD~1` | Diff against previous commit |
| `git diff main..feature/login` | Diff between branches |
| `git show abc1234` | Show commit details |
| `git blame README.md` | Show who changed each line |
| `git reflog` | Show all HEAD movements |

---

## 🔧 Common Commands

### Initial Setup

```bash
git config --global user.name "Alice"
git config --global user.email "alice@example.com"
git config --global core.editor "vim"
git config --global init.defaultBranch main
git config --list
```

### Starting a Repository

```bash
# Initialize a new repo in the current directory
git init

# Clone an existing remote repo
git clone https://github.com/company/webserver.git

# Clone into a specific directory name
git clone https://github.com/company/webserver.git my-webserver
```

### Staging Changes

```bash
# Stage a specific file
git add src/auth/login.js

# Stage all changes in the current directory
git add .

# Stage changes interactively — review each hunk
git add -p                    # opens interactive hunk selector
                              # y=stage  n=skip  s=split  e=edit  q=quit
```

> 💡 Use `git add -p` to create focused, atomic commits. Staging hunks rather than entire files helps you avoid accidentally committing debug code or unrelated changes.

### Committing

```bash
# Commit with a message (use Conventional Commits format)
git commit -m "feat(auth): add JWT token validation"

# Amend the last commit without changing the message
git commit --amend --no-edit

# Amend the last commit with a new message
git commit --amend -m "feat(auth): add JWT token validation and refresh logic"
```

> ⚠️ Only amend commits that have not been pushed to a shared remote. Amending rewrites the commit SHA and will require a force push.

### Branching

```bash
# List all local branches
git branch

# List all branches including remote-tracking
git branch -a

# Create a new branch
git branch feature/login

# Switch to a branch (modern syntax)
git switch feature/login

# Create and switch in one command
git switch -c feature/payment-gateway

# Rename current branch
git branch -m feature/old-name feature/new-name

# Delete a fully merged branch
git branch -d feature/login

# Force delete a branch (even if unmerged) # ⚠️
git branch -D feature/login
```

### Merging

```bash
# Merge a feature branch into current branch
git merge feature/login

# Merge and always create a merge commit (preserves branch history)
git merge --no-ff feature/login

# Squash all commits from branch into a single staged change
git merge --squash feature/login
git commit -m "feat(login): implement login page"
```

### Rebasing

```bash
# Rebase current branch onto main
git rebase main

# Interactive rebase — rewrite last 4 commits
git rebase -i HEAD~4

# Continue after resolving conflicts
git rebase --continue

# Abort a rebase in progress
git rebase --abort
```

### Pushing and Pulling

```bash
# Fetch remote changes without merging
git fetch origin

# Fetch and merge (or rebase, depending on pull.rebase config)
git pull origin main

# Push branch and set upstream tracking
git push -u origin feature/login

# Safe force push — fails if remote has new commits since last fetch # ⚠️
git push origin feature/login --force-with-lease
```

### Undoing Changes

```bash
# Discard unstaged changes to a file
git restore file.txt

# Unstage a file (keep changes in working dir)
git restore --staged file.txt

# Safely undo a commit by creating a new revert commit
git revert HEAD

# Undo last commit, keep changes staged
git reset --soft HEAD~1

# Undo last commit, keep changes unstaged
git reset --mixed HEAD~1

# Undo last commit AND discard all changes # ⚠️
git reset --hard HEAD~1
```

### Stashing

```bash
