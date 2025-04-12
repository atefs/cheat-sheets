# 🔐 SSH Cheat Sheet

[![Official Docs](https://img.shields.io/badge/docs-openssh.com-blue)](https://www.openssh.com/manual.html)
[![License](https://img.shields.io/badge/license-MIT-green)](../LICENSE)

SSH (Secure Shell) provides encrypted remote access to servers, secure file transfer, and port forwarding. OpenSSH is the standard implementation, available on every major platform. Version: OpenSSH 9.x (2024)

---

## Table of Contents

- [Installation](#installation)
- [Core Concepts](#-core-concepts)
- [Quick Reference](#-quick-reference)
  - [Connecting](#connecting)
  - [Key Management](#key-management)
  - [Tunneling](#tunneling)
  - [File Transfer](#file-transfer)
- [Common Commands](#-common-commands)
  - [Generating Keys](#generating-keys)
  - [Installing a Key on a Server](#installing-a-key-on-a-server)
  - [SSH Agent](#ssh-agent)
  - [Tunneling](#tunneling-1)
- [SSH Config File](#-ssh-config-file)
- [Server Configuration](#-server-configuration)
- [Best Practices](#-best-practices)
- [Real-World Examples](#-real-world-examples)
- [Troubleshooting](#-troubleshooting)
- [Pro Tips](#-pro-tips)
- [Resources](#-resources)

---

## Installation

| Platform | Command |
| -------- | ------- |
| macOS | Pre-installed (`ssh` + `sshd` via System Preferences) |
| Ubuntu/Debian | `sudo apt install openssh-client openssh-server` |
| Fedora | `sudo dnf install openssh-clients openssh-server` |
| Windows | Built-in since Windows 10 1809 (Settings → Optional features) |

Verify your installation:

```bash
ssh -V
```

---

## 🧱 Core Concepts

### How Key Pairs Work

When you run `ssh-keygen`, it creates two files: a private key (`id_ed25519`) that never leaves your machine, and a public key (`id_ed25519.pub`) that you distribute freely. The server verifies your identity by checking your public key against the ones in `~/.ssh/authorized_keys`. The private key signs a challenge; the server verifies the signature using the public key. No password is ever transmitted.

### The `authorized_keys` File

Located at `~/.ssh/authorized_keys` on the server. Each line is one public key. When a client connects, the server checks if the client's public key is in this file. `ssh-copy-id` automates appending your public key to this file.

### `known_hosts`

Located at `~/.ssh/known_hosts` on the client. When you first connect to a server, SSH shows you the server's fingerprint and asks you to verify it. If you accept, the fingerprint is stored in `known_hosts`. On subsequent connections, SSH checks that the server's key matches — preventing man-in-the-middle attacks. If the key changes unexpectedly, SSH refuses the connection.

### The SSH Agent

`ssh-agent` holds your decrypted private keys in memory for the session. You add keys with `ssh-add`. When SSH needs to authenticate, it asks the agent to sign the challenge. This means you enter your passphrase once per session instead of once per connection.

### Agent Forwarding

When enabled (`-A` flag or `ForwardAgent yes` in config), the remote host can use your local agent to authenticate to further hosts. Useful when deploying from a jump host to internal servers using your laptop's key. Use with care — anyone with root on the intermediate host can use your agent.

---

## 📋 Quick Reference

### Connecting

| Command | Description |
| ------- | ----------- |
| `ssh alice@server.company.com` | Basic connection |
| `ssh alice@server.company.com -p 2222` | Custom port |
| `ssh -i ~/.ssh/work_ed25519 alice@server.company.com` | Specific key |
| `ssh -v alice@server.company.com` | Verbose (debug mode) |
| `ssh -A alice@bastion.company.com` | With agent forwarding |
| `ssh work` | Use alias from `~/.ssh/config` |

### Key Management

| Command | Description |
| ------- | ----------- |
| `ssh-keygen -t ed25519 -C "alice@company.com"` | Generate ed25519 key (recommended) |
| `ssh-keygen -t rsa -b 4096 -C "alice@company.com"` | Generate RSA key (legacy) |
| `ssh-copy-id alice@server.company.com` | Install public key on server |
| `ssh-add ~/.ssh/id_ed25519` | Add key to agent |
| `ssh-add -l` | List keys loaded in agent |
| `ssh-add -d ~/.ssh/id_ed25519` | Remove key from agent |
| `cat ~/.ssh/id_ed25519.pub` | Print public key |

### Tunneling

| Command | Description |
| ------- | ----------- |
| `ssh -L 8080:localhost:80 alice@server` | Local port forward: localhost:8080 → server:80 |
| `ssh -R 9090:localhost:3000 alice@server` | Remote port forward: server:9090 → local:3000 |
| `ssh -D 1080 alice@server` | SOCKS proxy on localhost:1080 |
| `ssh -J alice@bastion alice@internal` | Jump host (bastion) |
| `ssh -N -f -L 5432:db:5432 alice@server` | Background tunnel, no shell |

### File Transfer

| Command | Description |
| ------- | ----------- |
| `scp file.txt alice@server:/home/alice/` | Copy file to server |
| `scp alice@server:/var/log/app.log ./` | Copy file from server |
| `scp -r ./project alice@server:/opt/` | Copy directory to server |
| `rsync -avz ./project/ alice@server:/opt/project/` | Efficient sync |

---

## 💻 Common Commands

### Generating Keys

```bash
# Modern: ed25519 (faster, smaller keys, more secure than RSA)
ssh-keygen -t ed25519 -C "alice@company.com" -f ~/.ssh/id_ed25519
# -C is a comment (usually your email) — helps identify the key in authorized_keys
# -f specifies the filename; omit to use the default

# Legacy RSA (use only when ed25519 is not supported)
ssh-keygen -t rsa -b 4096 -C "alice@company.com"
```

**File permissions by platform:**

```bash
# macOS / Linux — SSH refuses keys with loose permissions
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub
```

```powershell
# Windows — chmod has no effect; SSH enforces NTFS ACLs instead.
# ssh-keygen on Windows sets correct ACLs automatically.
# If you copy a key from elsewhere, fix permissions with:
icacls $HOME\.ssh\id_ed25519 /inheritance:r /grant:r "$($env:USERNAME):R"
```

> ⚠️ On Windows, do not use `chmod` — it is silently ignored. The Windows OpenSSH client reads NTFS ACLs and will reject any key file that grants access to users other than your account and SYSTEM.

### Installing a Key on a Server

```bash
# Automated (recommended)
ssh-copy-id alice@server.company.com
ssh-copy-id -i ~/.ssh/work_ed25519.pub alice@server.company.com  # specific key

# Manual (if ssh-copy-id is not available)
cat ~/.ssh/id_ed25519.pub | ssh alice@server.company.com \
  "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
```

### SSH Agent

**macOS** — The agent integrates with the macOS Keychain. Keys with passphrases are stored in Keychain after first use, so you only type the passphrase once (survives reboots).

```bash
# Add key to agent and save passphrase to Keychain (macOS)
ssh-add --apple-use-keychain ~/.ssh/id_ed25519

# Load Keychain-stored keys automatically — add to ~/.ssh/config:
# Host *
#   UseKeychain yes
#   AddKeysToAgent yes
```

**Linux** — Most desktop environments (GNOME, KDE) start `ssh-agent` or `gnome-keyring` automatically on login. In minimal environments, start it manually.

```bash
# Start agent manually (add to ~/.bashrc / ~/.zshrc for headless setups)
eval "$(ssh-agent -s)"

# Add keys
ssh-add ~/.ssh/id_ed25519
ssh-add ~/.ssh/work_ed25519

# List loaded keys
ssh-add -l

# Remove all keys from agent
ssh-add -D
```

**Windows** — Use the built-in OpenSSH Authentication Agent service (included with Windows 10 1809+ and Windows 11). Run PowerShell as Administrator once to enable it.

```powershell
# Enable and start the built-in SSH agent service (PowerShell, run as Admin — once)
Set-Service -Name ssh-agent -StartupType Automatic
Start-Service ssh-agent

# Add a key (regular PowerShell or Command Prompt)
ssh-add $HOME\.ssh\id_ed25519

# List loaded keys
ssh-add -l
```

> 💡 On Windows, Git for Windows also ships its own SSH agent. To avoid conflicts, configure Git to use the Windows OpenSSH agent: `git config --global core.sshCommand "C:/Windows/System32/OpenSSH/ssh.exe"`

### Tunneling

**Local port forwarding**

Use case: Access a service on the remote server (or its network) as if it were running locally. Common for accessing a database or internal web app behind a firewall.

```bash
# Forward localhost:5432 → db.internal:5432 (accessed via bastion)
ssh -L 5432:db.internal:5432 alice@bastion.company.com

# Now connect from your laptop:
psql -h localhost -p 5432 -U alice myapp
# Traffic flows: laptop:5432 → bastion → db.internal:5432

# Background tunnel (no interactive shell)
ssh -N -f -L 5432:db.internal:5432 alice@bastion.company.com
# -N = no command, just tunnel
# -f = fork to background
```

**Remote port forwarding**

Use case: Expose a service running on your local machine on a port of the remote server. Useful for sharing a local dev server with a colleague, or for receiving webhooks during development.

```bash
# Expose localhost:3000 as server.company.com:9090
ssh -R 9090:localhost:3000 alice@server.company.com

# Anyone connecting to server.company.com:9090 reaches your local port 3000
```

**SOCKS proxy**

Use case: Route all browser traffic through an encrypted SSH tunnel to the remote server. Useful for accessing internal resources or for privacy on untrusted networks. Configure your browser's proxy settings to SOCKS5 localhost:1080.

```bash
ssh -D 1080 alice@server.company.com
# Set browser proxy: SOCKS5 / localhost / 1080
```

**Jump host (bastion)**

Use case: Reach an internal server that's only accessible from a bastion host, using a single command from your laptop.

```bash
ssh -J alice@bastion.company.com alice@internal.10.0.1.50
# Or with multiple hops:
ssh -J alice@bastion.company.com,alice@relay.internal alice@target.internal

# In ~/.ssh/config (cleaner):
# Host internal
#   ProxyJump alice@bastion.company.com
```

---

## ⚙️ SSH Config File

The `~/.ssh/config` file lets you define aliases, defaults, and per-host settings so you never have to type long flags again.

**Config file location by platform:**

| Platform | Path |
| -------- | ---- |
| macOS / Linux | `~/.ssh/config` — set permissions with `chmod 600 ~/.ssh/config` |
| Windows | `%USERPROFILE%\.ssh\config` — fix ACLs: `icacls config /inheritance:r /grant:r "%USERNAME%:R"` |

```
# ~/.ssh/config  (macOS/Linux)  or  %USERPROFILE%\.ssh\config  (Windows)
# Permissions: chmod 600 ~/.ssh/config

# ── Global Defaults ────────────────────────────────────────────────────────
Host *
  AddKeysToAgent yes             # automatically add keys to agent on first use
  IdentityFile ~/.ssh/id_ed25519 # default key to try
  ServerAliveInterval 60         # send keepalive every 60s
  ServerAliveCountMax 3          # disconnect after 3 missed keepalives
  ControlMaster auto             # connection multiplexing (share one TCP connection)
  ControlPath ~/.ssh/cm/%r@%h:%p # socket path for multiplexing
  ControlPersist 10m             # keep master connection alive for 10 minutes

# ── Work Server ────────────────────────────────────────────────────────────
Host work
  HostName 203.0.113.50          # actual IP or hostname
  User alice
  Port 22
  IdentityFile ~/.ssh/work_ed25519

# ── Bastion + Internal Host ────────────────────────────────────────────────
Host bastion
  HostName bastion.company.com
  User alice
  ForwardAgent yes               # forward agent to bastion (needed for ProxyJump)

Host internal
  HostName 10.0.1.50
  User alice
  ProxyJump alice@bastion.company.com  # jump through bastion

# ── GitHub ─────────────────────────────────────────────────────────────────
Host github.com
  User git
  IdentityFile ~/.ssh/id_ed25519
  AddKeysToAgent yes
```

---

## 🖥️ Server Configuration

### Recommended `sshd_config` for Production

```conf
# /etc/ssh/sshd_config
# After editing: sudo systemctl restart sshd

# ── Authentication ──────────────────────────────────────────────────────────
PasswordAuthentication no          # disable password login (keys only)
PermitRootLogin no                 # never allow root login
PubkeyAuthentication yes           # enable key-based auth
AuthorizedKeysFile .ssh/authorized_keys  # standard location
ChallengeResponseAuthentication no # disable PAM challenge/response

# ── Protocol ────────────────────────────────────────────────────────────────
Port 22                            # consider changing to non-standard port
Protocol 2                         # SSHv2 only
AddressFamily inet                 # IPv4 only (or 'any' for dual-stack)

# ── Session Limits ──────────────────────────────────────────────────────────
LoginGraceTime 30                  # seconds to authenticate before disconnect
MaxAuthTries 3                     # attempts before connection close
MaxSessions 10                     # max sessions per connection

# ── Idle Timeout ────────────────────────────────────────────────────────────
ClientAliveInterval 300            # send keepalive every 5 minutes
ClientAliveCountMax 2              # disconnect after 2 missed keepalives (10 min idle)

# ── Features ────────────────────────────────────────────────────────────────
X11Forwarding no                   # disable X11 forwarding unless needed
AllowTcpForwarding yes             # enable port forwarding
GatewayPorts no                    # prevent remote-forwarded ports binding to 0.0.0.0

# ── AllowUsers ──────────────────────────────────────────────────────────────
AllowUsers alice bob deploy         # whitelist specific users
```

---

## ✅ Best Practices

- ✅ Use **ed25519** keys — smaller, faster, and more secure than RSA 2048
- ✅ **Always set a passphrase** on your private key; use `ssh-agent` so you only type it once per session
- ✅ Use `~/.ssh/config` aliases — stop typing IPs and flags for every connection
- ✅ Disable password authentication on servers (`PasswordAuthentication no` in `sshd_config`)
- ✅ Run `fail2ban` or `sshguard` on internet-facing servers to block brute-force attempts
- ✅ Set `chmod 700 ~/.ssh` and `chmod 600 ~/.ssh/*` — SSH ignores keys with loose permissions
- ✅ Use `ssh-copy-id` to install keys — it handles permissions correctly
- ⚠️ **Never share your private key** (`id_ed25519`) — only distribute `id_ed25519.pub`
- ⚠️ **Agent forwarding** (`-A` / `ForwardAgent yes`) trusts the remote host with your keys — only use on servers you control
- ⚠️ **`StrictHostKeyChecking no`** disables the `known_hosts` check — never use in production; it defeats MITM protection

---

## 🚀 Real-World Examples

### 1. Generate a Key and Add It to a Server

> You want to log into a server without a password.

```bash
# Generate key with a passphrase
ssh-keygen -t ed25519 -C "alice@company.com" -f ~/.ssh/id_ed25519

# Copy public key to server
ssh-copy-id alice@server.company.com

# Test (should not prompt for password, only for key passphrase if set)
ssh alice@server.company.com

# Add to ~/.ssh/config for a one-word alias
echo "
Host prod
  HostName server.company.com
  User alice
  IdentityFile ~/.ssh/id_ed25519" >> ~/.ssh/config

ssh prod    # done
```

### 2. Tunnel to a Database Behind a Firewall

> Your Postgres database is on an internal server only accessible via a bastion host. You want to connect with your local SQL client.

```bash
# Create a background tunnel: localhost:5433 → internal-db:5432 (via bastion)
ssh -N -f \
  -L 5433:db.internal.company.com:5432 \
  alice@bastion.company.com

# Now connect your SQL client to localhost:5433
psql -h localhost -p 5433 -U alice -d myapp

# When done, find and stop the tunnel
ps aux | grep ssh
kill <PID>
```

### 3. Set Up SSH for GitHub

> Configure git to authenticate to GitHub using your SSH key.

```bash
# Generate a dedicated key for GitHub
ssh-keygen -t ed25519 -C "alice@company.com" -f ~/.ssh/github_ed25519

# Print the public key — add this to GitHub Settings → SSH Keys
cat ~/.ssh/github_ed25519.pub

# Add to ~/.ssh/config
echo "
Host github.com
  User git
  IdentityFile ~/.ssh/github_ed25519
  AddKeysToAgent yes" >> ~/.ssh/config

# Test the connection
ssh -T git@github.com
# Expected: Hi alice! You've successfully authenticated...

# Now git operations work without a password
git clone git@github.com:org/repo.git
```

---

## 🔧 Troubleshooting

| Problem | Likely Cause | Fix |
| ------- | ------------ | --- |
| `Permission denied (publickey)` | Key not in `authorized_keys` or wrong permissions | Run `ssh-copy-id`; check `chmod 700 ~/.ssh` and `chmod 600 ~/.ssh/authorized_keys` on server |
| `WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED` | Server key changed (possible MITM or server rebuilt) | Verify the change is legitimate, then: `ssh-keygen -R hostname` |
| `Connection refused` | SSH not running or wrong port | Check `sudo systemctl status sshd`; verify port with `sudo ss -tlnp \| grep ssh` |
| `Too many authentication failures` | Too many keys tried | Add `-i ~/.ssh/specific_key` or use `IdentitiesOnly yes` in config |
| SSH drops connection after idle | No keepalive configured | Add `ServerAliveInterval 60` to `~/.ssh/config` |
| Agent not running / `Could not open connection to agent` | ssh-agent not started | Run `eval "$(ssh-agent -s)"` then `ssh-add` |

---

## 💡 Pro Tips

> 💡 **`ControlMaster` multiplexing speeds up repeated connections**
> With `ControlMaster auto` and `ControlPersist 10m` in your config, subsequent SSH connections to the same host reuse the existing TCP connection — no new handshake. `git push` to a remote SSH server becomes nearly instant.

> 💡 **`ssh -W` for transparent proxy forwarding**
> Chain through a bastion cleanly by using `-W` in your ProxyCommand rather than running a full shell on the bastion.

> 💡 **Use `ssh-keygen -R hostname` to clean up stale host keys**
> When you rebuild a server and reconnect, SSH will warn about changed host keys. Run `ssh-keygen -R hostname` to remove the old entry, then reconnect to accept the new one.

> ❌ **Don't use `StrictHostKeyChecking no` in automation**
> It's tempting to add to scripts to avoid interactive prompts, but it completely disables MITM protection. Instead, pre-populate `known_hosts` during provisioning: `ssh-keyscan hostname >> ~/.ssh/known_hosts`.

---

> 💡 **`ControlMaster` — reuse SSH connections for faster subsequent logins**
> Add to `~/.ssh/config`:
> ```
> Host *
>   ControlMaster auto
>   ControlPath ~/.ssh/sockets/%r@%h-%p
>   ControlPersist 600
> ```
> After the first connection, subsequent `ssh`, `scp`, and `rsync` to the same host reuse the existing TCP connection — near-instant. Create the sockets directory with `mkdir -p ~/.ssh/sockets`.

> 💡 **SSH escape sequences — manage a hung session without closing the terminal**
> While connected, press `Enter` then `~.` to terminate a hung connection immediately. `~&` puts the session in the background, `~#` lists forwarded connections, `~?` shows all escape sequences. The leading `Enter` is required; `~` typed mid-line is sent as a literal tilde.

> 💡 **`ProxyJump` — reach hosts behind a bastion with one command**
> `ssh -J bastion.example.com internal-server` jumps through the bastion transparently. Or set it permanently in `~/.ssh/config`:
> ```
> Host internal-server
>   ProxyJump bastion.example.com
> ```
> After that, `ssh internal-server` handles the jump automatically. Replaces the older `ProxyCommand` approach and works with `scp` and `rsync` too.

> 💡 **Verify host fingerprints on first connection to avoid MITM**
> When connecting to a new host for the first time, SSH shows the server's fingerprint and asks you to confirm. Get the expected fingerprint from the server admin out-of-band (Slack, ticket) before accepting. After accepting, the fingerprint is stored in `~/.ssh/known_hosts`. A changed fingerprint on a subsequent connection triggers a warning — investigate before overriding with `ssh-keygen -R hostname`.


## 📚 Resources

- [OpenSSH manual pages](https://www.openssh.com/manual.html) — `ssh`, `ssh_config`, `sshd_config`, `ssh-keygen` man pages
- [SSH Academy](https://www.ssh.com/academy/ssh) — Conceptual guides and tutorials
- [Smallstep SSH Handbook](https://smallstep.com/ssh/) — Modern SSH practices including certificate-based auth
- [ArchWiki: SSH Keys](https://wiki.archlinux.org/title/SSH_keys) — Comprehensive reference for key management

> 💡 **Persistent tunnels via `~/.ssh/config` — no flags to remember**
> Instead of typing `-L 5432:db:5432` every time, define the tunnel permanently in your config file:
> ```
> Host db-tunnel
>   HostName bastion.company.com
>   User alice
>   LocalForward 5432 db.internal:5432
>   RequestTTY no
> ```
> Then just `ssh -Nf db-tunnel` to start it in the background. Combine with `ControlMaster` to keep the tunnel alive across sessions.
