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

### Update and Upgrade

| Command | Description |
| ------- | ----------- |
| `brew update` | Update Homebrew and all tap metadata |
| `brew upgrade` | Upgrade all outdated packages |
| `brew upgrade git` | Upgrade a specific package |
| `brew pin git` | Pin: prevent a package from being upgraded |
| `brew unpin git` | Unpin |
| `brew outdated` | List packages with available upgrades |

### Search and Info

| Command | Description |
| ------- | ----------- |
| `brew search node` | Search formulae and casks |
| `brew info node` | Show version, deps, options, install path |
| `brew deps node` | List dependencies |
| `brew uses --installed node` | Which installed packages depend on node |
| `brew list` | List installed formulae |
| `brew list --cask` | List installed casks |
| `brew leaves` | List installed packages with no dependents |

### Maintenance

| Command | Description |
| ------- | ----------- |
| `brew cleanup` | Remove old versions of all packages |
| `brew cleanup -n` | Preview what cleanup would remove |
| `brew doctor` | Diagnose potential issues |
| `brew autoremove` | Remove unused dependencies |
| `brew missing` | Check for missing dependencies |

### Taps

| Command | Description |
| ------- | ----------- |
| `brew tap homebrew/cask-fonts` | Add a tap |
| `brew tap hashicorp/tap` | Add HashiCorp tap |
| `brew install hashicorp/tap/terraform` | Install from a specific tap |
| `brew tap-info homebrew/cask` | Show info about a tap |
| `brew untap some/tap` | Remove a tap |

---

## ⚙️ Common Commands

### Basic package operations

```bash
# Install
brew install neovim
brew install --cask visual-studio-code    # GUI app

# Upgrade
brew update                               # update Homebrew metadata
brew upgrade                              # upgrade everything
brew upgrade neovim                       # upgrade one package

# Search
brew search python                        # searches both formulae and casks
brew info python@3.12                     # details, version, installed location

# Uninstall
brew uninstall neovim
brew uninstall --cask visual-studio-code
brew autoremove                           # remove now-unused dependencies

# Maintenance
brew cleanup                              # remove old downloads and versions
brew doctor                               # check for problems and suggest fixes
brew outdated                             # list what needs upgrading
```

### Taps

```bash
# Add the HashiCorp tap and install Terraform
brew tap hashicorp/tap
brew install hashicorp/tap/terraform

# Add font cask (for Nerd Fonts, etc.)
brew tap homebrew/cask-fonts
brew install --cask font-jetbrains-mono-nerd-font

# List tapped repositories
brew tap

# Remove a tap (and all its formulae)
brew untap some/tap
```

### Services (processes managed by Homebrew)

```bash
brew services list                        # list all services and their status
brew services start postgresql@16         # start a service (and enable at login)
brew services stop postgresql@16          # stop a service
brew services restart nginx               # restart
brew services run postgresql@16           # start once (don't enable at login)
```

---

## 📦 Brewfile — Reproducible Setup

A `Brewfile` is a declarative manifest of all your Homebrew packages, casks, and taps. Commit it to your dotfiles repository to recreate your entire setup on a new machine with a single command.

```ruby
# Brewfile — commit to your dotfiles repo for reproducible setup

# ── Taps ──────────────────────────────────────────────────────────────────
tap "homebrew/cask-fonts"
tap "homebrew/cask-versions"
tap "hashicorp/tap"

# ── Development tools ─────────────────────────────────────────────────────
brew "git"
brew "neovim"
brew "tmux"
brew "fzf"                          # fuzzy finder
brew "ripgrep"                      # fast grep (rg)
brew "jq"                           # JSON processor
brew "httpie"                       # user-friendly curl alternative
brew "gh"                           # GitHub CLI
brew "lazygit"                      # terminal UI for git

# ── Languages & runtimes ──────────────────────────────────────────────────
brew "node"
brew "python"
brew "go"
brew "rustup"

# ── DevOps ────────────────────────────────────────────────────────────────
brew "hashicorp/tap/terraform"
brew "kubectl"
brew "helm"
brew "awscli"
brew "docker-compose"               # standalone Compose for Linux

# ── Shell enhancements ────────────────────────────────────────────────────
brew "zsh-syntax-highlighting"
brew "zsh-autosuggestions"
brew "starship"                     # cross-shell prompt

# ── GUI Apps (casks) ──────────────────────────────────────────────────────
cask "iterm2"
cask "visual-studio-code"
cask "docker"
cask "postman"
cask "rectangle"                    # window manager
cask "raycast"                      # Spotlight replacement

# ── Fonts ─────────────────────────────────────────────────────────────────
cask "font-jetbrains-mono-nerd-font"
cask "font-fira-code-nerd-font"
```
