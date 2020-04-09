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


---

_Content being added — work in progress._
