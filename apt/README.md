# 📦 apt Cheat Sheet

`apt` is the package manager for Debian-based Linux distributions (Debian, Ubuntu, Linux Mint, Pop!_OS, and derivatives). It handles downloading, installing, upgrading, and removing software packages from configured repositories. Version: apt 2.x / Debian 12 / Ubuntu 24.04 LTS (2024)

[![Official Docs](https://img.shields.io/badge/docs-manpages-blue)](https://manpages.debian.org/bookworm/apt/apt.8.en.html)
[![License](https://img.shields.io/badge/license-MIT-green)](../LICENSE)

---

## Table of Contents

- [Installation](#installation)
- [Core Concepts](#-core-concepts)
- [Quick Reference](#-quick-reference)
- [Common Commands](#-common-commands)
- [Adding a Third-Party Repository](#adding-a-third-party-repository-modern-method)
- [Package Pinning](#package-pinning--prevent-or-force-specific-versions)
- [Unattended Upgrades](#unattended-upgrades--automatic-security-updates)
- [Real-World Examples](#-real-world-examples)
- [Pro Tips](#-pro-tips)
- [Troubleshooting](#-troubleshooting)
- [Resources](#-resources)

---

## Installation

`apt` is pre-installed on all Debian and Ubuntu systems. No installation needed.

```bash
# Verify
apt --version
```

---

## 🧠 Core Concepts

Three layers, from highest to lowest:

**`apt`** — the recommended user-facing frontend. Handles dependency resolution, downloads packages from repositories, and shows progress bars. Use this interactively in your terminal. Added in Debian 8 / Ubuntu 14.04. The main improvements over `apt-get`: a progress indicator, color output, and a simplified interface.

**`apt-get`** — the stable scripting interface. Same capability as `apt`, but output and behavior are guaranteed not to change between versions. Use `apt-get` (not `apt`) in shell scripts, Dockerfiles, and CI pipelines where stable, parseable output matters.

**`dpkg`** — the lowest-level tool. Directly installs, removes, and queries `.deb` files without touching repositories or resolving dependencies. `apt` calls `dpkg` under the hood. Use `dpkg` when you have a local `.deb` file or need to query the installed package database directly.

**`/etc/apt/sources.list.d/`** — where repository definitions live. Each `.list` or `.sources` file adds a repository that `apt update` will query when refreshing the package index.

---

## 📋 Quick Reference

### Package Management

| Command | Description |
| ------- | ----------- |
| `sudo apt update` | Refresh package index (do this first) |
| `sudo apt upgrade` | Upgrade all upgradable packages |
| `sudo apt full-upgrade` | Upgrade + handle removed/added dependencies |
| `sudo apt install nginx` | Install package |
| `sudo apt install nginx=1.24.0-*` | Install specific version |
| `sudo apt remove nginx` | Remove package (keep config files) |
| `sudo apt purge nginx` | Remove package + delete config files |
| `sudo apt autoremove` | Remove unused dependency packages |
| `sudo apt clean` | Clear downloaded package cache |

### Search and Info

| Command | Description |
| ------- | ----------- |
| `apt search nginx` | Search package names and descriptions |
| `apt show nginx` | Package details, version, dependencies |
| `apt list --installed` | All installed packages |
| `apt list --upgradable` | Packages with available upgrades |
| `apt-cache policy nginx` | Show available versions and repo priority |
| `apt-cache depends nginx` | Show dependencies |
| `apt-cache rdepends nginx` | Show reverse dependencies |

### dpkg

| Command | Description |
| ------- | ----------- |
| `sudo dpkg -i package.deb` | Install local `.deb` file |
| `dpkg -l` | List all installed packages |
| `dpkg -l nginx` | Check if nginx is installed |
| `dpkg -L nginx` | List files installed by package |
| `dpkg -S /usr/sbin/nginx` | Which package owns this file |
| `dpkg --get-selections` | List all packages with their status |

---

## ⚙️ Common Commands

### Everyday package management

```bash
# Always update before installing
sudo apt update

# Install packages
sudo apt install nginx
sudo apt install nginx postgresql redis-server   # multiple at once
sudo apt install -y nginx                        # non-interactive (no prompt)
sudo apt install --no-install-recommends nginx   # minimal install (no optional deps)

# Upgrade
sudo apt upgrade                                 # safe: never removes packages
sudo apt full-upgrade                            # may remove packages to resolve deps

# Remove
sudo apt remove nginx                            # remove binary, keep /etc/nginx
sudo apt purge nginx                             # remove binary AND /etc/nginx
sudo apt autoremove                              # remove orphaned dependencies

# Cache
sudo apt clean                                   # delete all cached .deb files
sudo apt autoclean                               # delete only outdated cached .deb files

# Fix broken installs
sudo apt install -f                              # fix and complete broken installs
sudo dpkg --configure -a                         # reconfigure partially installed packages
```

### apt-get in scripts and Dockerfiles

```bash
# In scripts and Dockerfiles: use apt-get, not apt
apt-get update -qq                              # quiet update (warnings only)
apt-get install -y --no-install-recommends \    # non-interactive, minimal
  nginx \
  curl \
  ca-certificates

# Clean up after install to reduce image size
apt-get clean && rm -rf /var/lib/apt/lists/*
```

### dpkg usage

```bash
# Install a local .deb file
sudo dpkg -i ./slack-desktop-4.36.140-amd64.deb
# If dpkg reports missing dependencies:
sudo apt install -f                              # resolve and install missing deps

# Query installed packages
dpkg -l                                         # all packages
dpkg -l | grep nginx                            # filter
dpkg -L nginx                                   # files installed by nginx
dpkg -S /usr/sbin/nginx                         # which package owns /usr/sbin/nginx

# Get package version
dpkg -l nginx | awk '/^ii/{print $3}'
```

---

## Adding a Third-Party Repository (Modern Method)

The old method (`apt-key add`) is deprecated because keys added that way are trusted for **all** repositories, not just the one you're adding. The modern approach scopes the key to a specific repository using `signed-by=` in the repository definition.

```bash
# Example: Add Docker's official repository (Ubuntu)

# Step 1: Download the signing key and store it in the keyring directory
curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Step 2: Add the repository pointing to that specific key
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" \
  | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Step 3: Update and install
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io
```

> ⚠️ **Why not `apt-key add`?** The old `sudo apt-key add pubkey.gpg` method adds the key to a global keyring (`/etc/apt/trusted.gpg`) that applies to every repository. This means a compromised or malicious key could sign packages from any repo. The `signed-by=` approach scopes the key to one specific repository line.

---

## Package Pinning — Prevent or Force Specific Versions

Pinning controls which version `apt` installs or upgrades to.

```
# /etc/apt/preferences.d/nginx
# Pin nginx to a specific version — prevent upgrade
Package: nginx
Pin: version 1.24.*
Pin-Priority: 1001
```

```
# /etc/apt/preferences.d/hold-nginx
# Priority -1 = never install from any repo
Package: nginx
Pin: release *
Pin-Priority: -1
```

Alternative: use `apt-mark` for simpler holds:

```bash
