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

---

_Content being added — work in progress._
