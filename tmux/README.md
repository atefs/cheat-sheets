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

---

_Content being added — work in progress._
