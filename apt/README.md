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
sudo apt-mark hold nginx              # prevent nginx from being upgraded
sudo apt-mark unhold nginx            # remove the hold
sudo apt-mark showhold                # list all held packages
```

Priority values: `> 1000` = install even if downgrade; `1000` = install unless downgrade; `990` = default installed; `500` = default; `100` = backport; `< 0` = never install.

---

## Unattended Upgrades — Automatic Security Updates

```bash
# Install
sudo apt install unattended-upgrades

# Enable (also creates /etc/apt/apt.conf.d/20auto-upgrades)
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

Key configuration file `/etc/apt/apt.conf.d/50unattended-upgrades`:

```
Unattended-Upgrade::Allowed-Origins {
    "${distro_id}:${distro_codename}-security";  // security updates only (default)
    // "${distro_id}:${distro_codename}-updates"; // uncomment for all updates
};

Unattended-Upgrade::AutoFixInterruptedDpkg "true";
Unattended-Upgrade::Remove-Unused-Dependencies "true";
Unattended-Upgrade::Automatic-Reboot "false";          // set true to auto-reboot
Unattended-Upgrade::Automatic-Reboot-Time "02:00";
```

```bash
# Test (dry run — no changes)
sudo unattended-upgrades --dry-run --debug

# Check logs
cat /var/log/unattended-upgrades/unattended-upgrades.log
```

---

## 💡 Real-World Examples

### 1. Set up a fresh Ubuntu server

Standard first steps on a new Ubuntu server: update, install essentials, enable unattended upgrades.

```bash
# Update package index and upgrade everything
sudo apt update && sudo apt upgrade -y

# Install essential packages
sudo apt install -y --no-install-recommends \
  curl \
  wget \
  git \
  vim \
  htop \
  unzip \
  ca-certificates \
  gnupg \
  ufw

# Enable automatic security updates
sudo apt install -y unattended-upgrades
sudo dpkg-reconfigure --priority=low unattended-upgrades

# Clean up
sudo apt autoremove -y && sudo apt clean
```

### 2. Pin a package version to prevent it from being upgraded

Your application requires nginx 1.24.x and you don't want `apt upgrade` to bump it to 1.26.

```bash
# Check the currently installed version
dpkg -l nginx | awk '/^ii/{print $3}'

# Method 1: apt-mark (simplest)
sudo apt-mark hold nginx
sudo apt-mark showhold            # verify

# Method 2: preferences.d (more control)
cat <<'EOF' | sudo tee /etc/apt/preferences.d/nginx
Package: nginx
Pin: version 1.24.*
Pin-Priority: 1001
EOF

# Verify
apt-cache policy nginx            # should show held version as installed
sudo apt upgrade                  # nginx should not appear in upgrade list
```

### 3. Install a `.deb` file and fix missing dependencies

You downloaded a `.deb` file for an app not in the standard repos.

```bash
# Download the .deb file
wget https://releases.company.com/app/v2.1.0/app-2.1.0-amd64.deb

# Install it (dpkg doesn't resolve dependencies)
sudo dpkg -i app-2.1.0-amd64.deb

# If it reports missing dependencies:
# dpkg: dependency problems prevent configuration of app:
#   app depends on libssl3 (>= 3.0.0); however...

# Fix by letting apt resolve and install missing deps
sudo apt install -f

# Verify it installed
dpkg -l | grep "^ii.*app"
app --version
```

---

## 💡 Pro Tips

> 💡 **`apt update` before every install in scripts**
> Always run `apt-get update` before `apt-get install` in scripts and Dockerfiles. Without it, you're working with a stale package index and may install outdated versions or hit "package not found" errors.

> 💡 **`apt-cache policy` shows all available versions**
> `apt-cache policy nginx` shows which version is installed, which is available in each configured repo, and the repo priority. Essential for debugging "why is this version being installed?" questions.

> 💡 **Clean up in Dockerfiles to reduce image size**
> In every Dockerfile `RUN` block that uses `apt`:
> ```dockerfile
> RUN apt-get update && apt-get install -y --no-install-recommends \
>     nginx \
>  && apt-get clean \
>  && rm -rf /var/lib/apt/lists/*
> ```
> The `rm -rf /var/lib/apt/lists/*` removes the package index cache from the image layer.

> 💡 **`dpkg -S` to find which package owns a file**
> ```bash
> dpkg -S /usr/bin/python3       # → python3-minimal: /usr/bin/python3
> dpkg -S $(which nginx)          # find package for any binary in PATH
> ```

> ❌ **Don't mix `apt` and manual installs in production**
> Installing software by downloading binaries or compiling from source alongside apt-managed packages creates conflicts, breaks dependency tracking, and makes upgrades unpredictable. Use apt, snap, or a dedicated version manager for each tool.

---

## 🔍 Troubleshooting

| Problem | Likely Cause | Fix |
| ------- | ------------ | --- |
| `Unable to locate package X` | Package index stale or package not in repo | `sudo apt update` first; or add the package's repository |
| `dpkg was interrupted` | Previous install/upgrade crashed | `sudo dpkg --configure -a` |
| `Unmet dependencies` | Conflicting package versions | `sudo apt install -f` to auto-resolve |
| `Hash Sum mismatch` | Stale or corrupted package cache | `sudo apt clean && sudo apt update` |
| `Package X is kept back` | New dependencies needed for upgrade | `sudo apt full-upgrade` |
| `E: Could not get lock /var/lib/dpkg/lock` | Another apt process is running | Wait for it to finish; if stale: `sudo rm /var/lib/dpkg/lock-frontend` then `sudo dpkg --configure -a` |
| `W: GPG error: ... NO_PUBKEY` | Missing repository signing key | Add the key with the modern `signed-by=` method (see above) |

---

> 💡 **`apt-mark hold` — prevent a package from being upgraded**
> `sudo apt-mark hold linux-image-generic` pins the package at its current version. `apt upgrade` and `apt dist-upgrade` will skip it. Run `apt-mark showhold` to list all held packages. Remove with `sudo apt-mark unhold linux-image-generic`. Useful for kernel versions or packages with known regressions.

> 💡 **`apt-cache policy` — see installed vs available versions and repository priorities**
> `apt-cache policy nginx` shows the installed version, the candidate version from each repo, and each repo's pin priority. Higher priority wins during upgrades. This is the first command to run when debugging why a package is coming from an unexpected repository or stuck at an older version.

## 📚 Resources

- [Ubuntu documentation: apt](https://ubuntu.com/server/docs/package-management) — Package management guide
- [Debian apt manual](https://manpages.debian.org/bookworm/apt/apt.8.en.html) — Official man page
- [apt-get vs apt](https://itsfoss.com/apt-vs-apt-get-difference/) — When to use which
- [Debian Policy: Package priorities](https://www.debian.org/doc/debian-policy/ch-archive.html#priorities) — Priority system explained

> 💡 **`apt list --all-versions` — see every available version across all repos**
> `apt list --all-versions nginx` shows all versions of a package from every configured repository, not just the candidate. Useful for debugging pinning issues or confirming that a specific version is available before installing it with `apt install nginx=1.24.0-*`.
