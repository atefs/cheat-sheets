# 📝 Vim Cheat Sheet

Vim is a highly configurable terminal text editor built for efficient text manipulation. It uses a modal editing model where each mode serves a distinct purpose. Why learn it? Once internalized, Vim's composable command grammar lets you edit faster than any mouse-driven editor. Version: Vim 8.x / Neovim 0.9+ (2024)

[![Official Docs](https://img.shields.io/badge/docs-vimdoc-blue)](https://vimdoc.sourceforge.net/)
[![Neovim](https://img.shields.io/badge/Neovim-0.9%2B-green)](https://neovim.io)

> 💡 **Neovim users:** This sheet covers core Vim that applies to both Vim and Neovim.
> For Neovim-specific content (LSP, `init.lua`, plugins), see [advanced.md](./advanced.md).

---

## Table of Contents

- [Installation](#-installation)
- [Core Concepts](#-core-concepts)
- [Quick Reference](#-quick-reference)
- [Common Commands](#-common-commands)
- [Real-World Examples](#-real-world-examples)
- [Pro Tips](#-pro-tips)
- [Going Further](#-going-further)
- [Resources](#-resources)

---

## 📦 Installation

| Platform | Command |
| -------- | ------- |
| macOS | `brew install neovim` or `brew install vim` |
| Ubuntu/Debian | `sudo apt install neovim` or `sudo apt install vim` |
| Fedora | `sudo dnf install neovim` |
| Windows | `winget install Neovim.Neovim` |

Verify your installation:

```bash
vim --version | head -1
```

---

## 🧠 Core Concepts

**Modal editing** — The mode you are in determines what keystrokes do. Most editors have only one mode (insert text); Vim has several. This is the single most important concept to understand before using Vim.

**The 4 main modes:**

- **Normal** — navigation and operators (default when opening a file; where you spend most time)
- **Insert** — typing text (entered via `i`, `a`, `o`, etc.)
- **Visual** — selecting text (`v` char-wise, `V` line-wise, `Ctrl-v` block-wise)
- **Command-line** — Ex commands via `:` prefix (save, quit, search/replace, etc.)

**Grammar of Vim:** `[count] operator [motion]`

Vim commands compose like a language. Once you know the operators and motions, you can combine them freely:

- `3dw` = delete 3 words
- `ci"` = change inside quotes
- `yap` = yank a paragraph
- `>G` = indent from current line to end of file

**Buffers, Windows, Tabs:**

- **Buffer** = a file loaded into memory (can be invisible — no window showing it)
- **Window** = a viewport showing a buffer (multiple windows can show the same buffer)
- **Tab** = a collection of windows (not like browser tabs — more like layouts)

**The dot operator** — `.` repeats the last change. This is the most powerful single key in Vim. Make a change, then press `.` to repeat it anywhere.


---

_Content being added — work in progress._
