# 🍺 Homebrew Cheat Sheet

Homebrew is the missing package manager for macOS (and Linux). It installs command-line tools, languages, and GUI applications with a single command and keeps them up to date. Version: Homebrew 4.x (2024)

[![Official Docs](https://img.shields.io/badge/docs-brew.sh-orange)](https://brew.sh)
[![License](https://img.shields.io/badge/license-MIT-green)](../LICENSE)

---

## Table of Contents

- [Installation](#installation)
- [Core Concepts](#-core-concepts)
- [Quick Reference](#-quick-reference)
- [Common Commands](#-common-commands)
- [Brewfile — Reproducible Setup](#-brewfile--reproducible-setup)
- [Pro Tips](#-pro-tips)
- [Resources](#-resources)

---

## Installation

```bash
# Install Homebrew (macOS and Linux)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# macOS Apple Silicon — add to shell after install:
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"

# macOS Intel — Homebrew installs to /usr/local (already in PATH)

# Linux — add to shell:
echo 'eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"' >> ~/.bashrc
```

Verify: `brew --version`

---

## 🧠 Core Concepts

**Formula** — a Ruby script that defines how to download, build, and install a single command-line tool or library. Stored in the `homebrew/core` tap. Run `brew info git` to see a formula's description, dependencies, and options.

**Cask** — a formula variant for macOS GUI applications (`.app` bundles, `.pkg` installers). Casks live in `homebrew/cask`. Examples: Firefox, iTerm2, VS Code.

**Tap** — a third-party formula repository. Running `brew tap org/repo` clones it and makes its formulae available. Example: `brew tap hashicorp/tap` gives you Terraform, Vault, and Consul via Homebrew.

**Cellar** — the directory where Homebrew installs packages.

- Apple Silicon: `/opt/homebrew/Cellar`
- Intel Mac: `/usr/local/Cellar`
- Linux: `/home/linuxbrew/.linuxbrew/Cellar`

When you install a package, the binary is symlinked from the Cellar into the prefix (`/opt/homebrew/bin`) so it appears in your PATH.

---

## 📋 Quick Reference

### Install and Uninstall

| Command | Description |
| ------- | ----------- |
| `brew install git` | Install a formula |
| `brew uninstall git` | Uninstall a formula |
| `brew install --cask firefox` | Install a macOS GUI app (cask) |
| `brew uninstall --cask firefox` | Uninstall a cask |
| `brew reinstall git` | Reinstall (useful after corruption) |

---

_Content being added — work in progress._
