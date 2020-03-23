# 🪟 tmux Cheat Sheet

tmux is a terminal multiplexer that lets you create, manage, and navigate multiple terminal sessions, windows, and panes from a single connection. Sessions persist after disconnecting — start a long job, detach, and reconnect hours later to find it still running. Version: tmux 3.x (2024)

[![Official Docs](https://img.shields.io/badge/docs-tmux.github.io-blue)](https://github.com/tmux/tmux/wiki)
[![License](https://img.shields.io/badge/license-MIT-green)](../LICENSE)

---

## Table of Contents

- [Installation](#-installation)
- [Core Concepts](#-core-concepts)
- [Quick Reference](#-quick-reference)
  - [Sessions](#sessions)
  - [Windows](#windows)
  - [Panes](#panes)
  - [Copy Mode](#copy-mode)
  - [Misc](#misc)
- [Common Commands](#-common-commands)
- [Real-World Examples](#-real-world-examples)
- [Configuration](#-configuration)
- [Plugin Ecosystem](#-plugin-ecosystem)
- [Pro Tips](#-pro-tips)
- [Resources](#-resources)

---

## 📦 Installation

| Platform | Command |
| -------- | ------- |
| macOS | `brew install tmux` |
| Ubuntu/Debian | `sudo apt install tmux` |
| Fedora | `sudo dnf install tmux` |
| From source | See https://github.com/tmux/tmux/wiki/Installing |

Verify your installation:

```bash
tmux -V
```

---

## 🧠 Core Concepts

**Session** — a workspace you can detach from and reattach to; persists when you close your terminal or disconnect SSH. Think of it as a persistent container for your work context. You can have multiple sessions running simultaneously, each completely independent.

**Window** — a tab within a session; full-screen. You can have multiple windows open simultaneously and switch between them instantly. Each window fills the entire terminal and can be named to reflect its purpose (e.g., "editor", "server", "logs").

**Pane** — a split within a window; each pane is an independent terminal. Useful for seeing an editor and a terminal side by side, or watching logs while running commands. Panes share the screen area of a single window.

**Prefix key** — all tmux shortcuts start with `Ctrl-b` by default. You press the prefix first, release it, then press the shortcut key. In this sheet, all shortcuts are shown as "Prefix + key". The prefix can be remapped in `~/.tmux.conf` — a common choice is `Ctrl-a`.

**Session / Window / Pane hierarchy:**

```
tmux server
└── Session: "work"
    ├── Window 0: "editor"   [vim main.go]
    ├── Window 1: "server"   [go run .]
    └── Window 2: "logs"
        ├── Pane 0 (left)   [tail -f app.log]
        └── Pane 1 (right)  [htop]
```

---

## ⚡ Quick Reference

> The **prefix key** is `Ctrl-b` by default. Every shortcut below requires pressing `Ctrl-b` first, then the key. Example: "Prefix + d" means press `Ctrl-b`, release, then press `d`.

### Sessions

| Shortcut / Command | Action |
| ------------------ | ------ |
| `tmux new -s work` | New named session |
| `tmux ls` | List sessions |
| `tmux attach -t work` | Attach to session named "work" |
| `tmux kill-session -t work` | Kill named session |
| `Prefix + d` | Detach from session |
| `Prefix + $` | Rename current session |
| `Prefix + (` / `Prefix + )` | Previous / next session |
| `Prefix + s` | Interactive session list |

### Windows

| Shortcut | Action |
| -------- | ------ |
| `Prefix + c` | New window |
| `Prefix + ,` | Rename current window |
| `Prefix + n` / `Prefix + p` | Next / previous window |
| `Prefix + 0-9` | Switch to window by number |
| `Prefix + &` | Kill current window |
| `Prefix + w` | Interactive window list |
| `Prefix + .` | Move window to number |

### Panes

| Shortcut | Action |
| -------- | ------ |
| `Prefix + %` | Split vertically (left/right) |
| `Prefix + "` | Split horizontally (top/bottom) |
| `Prefix + ←/→/↑/↓` | Navigate between panes |
| `Prefix + z` | Toggle pane zoom (fullscreen) |
| `Prefix + {` / `Prefix + }` | Swap pane left / right |
| `Prefix + x` | Kill current pane |
| `Prefix + Ctrl-↑/↓/←/→` | Resize pane |
| `Prefix + q` | Show pane numbers (press number to jump) |
| `Prefix + !` | Break pane into its own window |

### Copy Mode

| Shortcut | Action |
| -------- | ------ |
| `Prefix + [` | Enter copy mode |
| `Space` | Start selection (vi keys) |
| `Enter` | Copy selection to tmux buffer |
| `Prefix + ]` | Paste from tmux buffer |
| `/` | Search in copy mode |
| `q` | Exit copy mode |

### Clipboard Integration by Platform

Copying in tmux copy mode writes to tmux's internal buffer. To also copy to the OS clipboard, you need a clipboard provider.

| Platform | Clipboard provider | tmux config |
| -------- | ------------------ | ----------- |
| macOS | Built-in (`pbcopy`) | tmux ≥ 3.2 uses it automatically; add `set -g default-command "reattach-to-user-namespace -l $SHELL"` for older versions |
| Linux (X11) | `xclip` or `xsel` (`sudo apt install xclip`) | See config below |
| Linux (Wayland) | `wl-clipboard` (`sudo apt install wl-clipboard`) | Replace `xclip` with `wl-copy` in config below |
| Windows (WSL2) | Windows clipboard interop is automatic in WSL2 | Use `clip.exe` as the copy command |

Add to `~/.tmux.conf` to copy to OS clipboard on selection (vi copy-mode):

```bash
# macOS
bind -T copy-mode-vi y send -X copy-pipe-and-cancel "pbcopy"

# Linux X11
bind -T copy-mode-vi y send -X copy-pipe-and-cancel "xclip -selection clipboard"

# Linux Wayland
bind -T copy-mode-vi y send -X copy-pipe-and-cancel "wl-copy"

# Windows/WSL2
bind -T copy-mode-vi y send -X copy-pipe-and-cancel "clip.exe"
```

> 💡 The `tmux-yank` plugin (`set -g @plugin 'tmux-plugins/tmux-yank'`) auto-detects your platform and wires up the correct clipboard command — it supports macOS, Linux X11/Wayland, and WSL out of the box.

### Misc

| Shortcut / Command | Action |
| ------------------ | ------ |
| `Prefix + :` | Open tmux command prompt |
| `Prefix + ?` | Show all key bindings |
| `Prefix + r` (after config) | Reload config |
| `Prefix + t` | Show clock |

---

## 🖥️ Common Commands

### Session management

```bash
# Create sessions
tmux                              # new unnamed session
tmux new -s work                  # new session named "work"
tmux new -s deploy -d             # new detached session (start in background)

# Attach and list
tmux ls                           # list all sessions
tmux attach                       # attach to most recent session
tmux attach -t work               # attach to "work"
tmux new-session -A -s main       # attach if exists, create if not

# Kill
tmux kill-session -t work
tmux kill-server                  # kill everything # ⚠️
```

### Window management

```bash
# Inside tmux, at the command prompt (Prefix + :)
new-window -n "server"            # new window named "server"
rename-window editor
move-window -t 3                  # move to slot 3
```

### Pane management

```bash
# Split keeping current directory
bind '"' split-window -v -c "#{pane_current_path}"
bind '%' split-window -h -c "#{pane_current_path}"

# Resize from command line
tmux resize-pane -D 5             # down 5 rows
tmux resize-pane -R 10            # right 10 cols
```

### Sending commands to panes

```bash
# Send a command to a specific pane without switching to it
tmux send-keys -t work:1.0 "npm run dev" Enter   # session:window.pane
```

---

## 🔧 Real-World Examples

### 1. Development environment with editor, server, and logs

> You want a persistent workspace with vim in one window, a dev server in another, and logs split across two panes.

```bash
# Create the session with a named window for editing
tmux new -s dev -n editor

# Open your editor
vim src/main.go

# Create a second window for the dev server
# Prefix + c, then name it: Prefix + , → "server"
# Run: go run .

# Create a third window for logs
# Prefix + c → name "logs"
# Split it vertically: Prefix + %
# Left pane: tail -f app.log
# Right pane: htop
```

### 2. Pair programming: share a session

> Two developers need to see the same terminal session over SSH.

```bash
# Developer 1: start a named session
tmux new -s shared

# Developer 2: attach to the same session (read-write)
tmux attach -t shared

# Developer 2: attach in read-only mode
tmux attach -t shared -r
```

