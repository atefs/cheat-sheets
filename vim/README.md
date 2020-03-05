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

### Mode Diagram

```
Normal ──i/a/o/O/I/A──► Insert
  │                         │
  │◄──────── Esc ───────────┘
  │
  ├────── v/V/Ctrl-v ──► Visual
  │                         │
  │◄──────── Esc ───────────┘
  │
  └──────── : ──────────► Command-line
                              │
                         Enter / Esc
```

---

## ⚡ Quick Reference

### Motions

| Key | Motion |
| --- | ------ |
| `h j k l` | ← ↓ ↑ → |
| `w / b` | Next/prev word start |
| `e / ge` | Next/prev word end |
| `0 / ^` | Line start (col 0 / first non-blank) |
| `$` | Line end |
| `gg / G` | File start / end |
| `{N}G` | Go to line N |
| `f{c} / F{c}` | Find char forward/backward on line |
| `t{c} / T{c}` | Till char forward/backward |
| `%` | Jump to matching bracket/paren/brace |
| `Ctrl-d / Ctrl-u` | Half-page down/up |
| `Ctrl-f / Ctrl-b` | Full-page down/up |
| `{ / }` | Prev/next empty line (paragraph) |
| `H / M / L` | Top/middle/bottom of screen |

### Operators

| Key | Action |
| --- | ------ |
| `d` | Delete (cut) |
| `c` | Change (delete + enter Insert mode) |
| `y` | Yank (copy) |
| `p / P` | Paste after/before cursor |
| `> / <` | Indent / unindent |
| `=` | Auto-indent |
| `g~ / gU / gu` | Toggle / uppercase / lowercase |
| `!` | Filter through external command |

### Text Objects

Text objects let you operate on semantic units of text. Used as the motion in `operator + text object`:

| Key | Object |
| --- | ------ |
| `iw / aw` | Inner word / a word (with trailing space) |
| `i" / a"` | Inner / outer double quotes |
| `i' / a'` | Inner / outer single quotes |
| `i( / a(` | Inner / outer parentheses |
| `i{ / a{` | Inner / outer curly braces |
| `i[ / a[` | Inner / outer square brackets |
| `ip / ap` | Inner / outer paragraph |
| `it / at` | Inner / outer HTML tag |
| `is / as` | Inner / outer sentence |

### Entering Modes

| Key | Effect |
| --- | ------ |
| `i` | Insert before cursor |
| `a` | Append after cursor |
| `o` | Open new line below, enter Insert |
| `O` | Open new line above, enter Insert |
| `I` | Insert at first non-blank of line |
| `A` | Append at end of line |
| `s` | Substitute char (delete + Insert) |
| `S` | Substitute line |
| `v` | Visual char-wise |
| `V` | Visual line-wise |
| `Ctrl-v` | Visual block-wise |
| `Esc` | Return to Normal mode |

### Search

| Key | Effect |
| --- | ------ |
| `/pattern` | Search forward |
| `?pattern` | Search backward |
| `n / N` | Next / previous match |
| `*` | Search word under cursor (forward) |
| `#` | Search word under cursor (backward) |
| `:%s/old/new/g` | Replace all in file |
| `:%s/old/new/gc` | Replace all, confirm each |

### Files & Buffers

| Command | Effect |
| ------- | ------ |
| `:e filename` | Open file (or create new) |
| `:ls` | List open buffers |
| `:b2` | Switch to buffer 2 |
| `:b filename` | Switch to buffer by name |
| `:bd` | Close/delete current buffer |
| `:bn / :bp` | Next / previous buffer |

### Windows & Splits

| Command | Effect |
| ------- | ------ |
| `:sp filename` | Horizontal split |
| `:vsp filename` | Vertical split |
| `Ctrl-w w` | Cycle to next window |
| `Ctrl-w h/j/k/l` | Navigate left/down/up/right |
| `Ctrl-w =` | Equalize all window sizes |
| `Ctrl-w q` | Close current window |
| `Ctrl-w o` | Close all windows except current |

### Marks & Registers

| Command | Effect |
| ------- | ------ |
| `m{a-z}` | Set local file mark |
| `m{A-Z}` | Set global mark (cross-file) |
| `'{mark}` | Jump to mark (line start) |
| `` `{mark} `` | Jump to mark (exact position) |
| `:marks` | List all marks |
| `"{register}y` | Yank into named register |
| `"{register}p` | Paste from named register |
| `:reg` | List all registers |

### Macros

| Command | Effect |
| ------- | ------ |
| `q{a-z}` | Start recording macro into register |
| `q` | Stop recording |
| `@{register}` | Play macro |
| `@@` | Repeat last played macro |
| `10@a` | Play macro 10 times |

---

## 🔧 Common Commands

### Entering and Exiting Modes

```vim
i   " insert before cursor
a   " append after cursor
o   " open new line below, enter Insert
O   " open new line above, enter Insert
I   " insert at line start (first non-blank)
A   " append at line end
s   " substitute char (delete + Insert)
S   " substitute line
Esc " return to Normal mode
```

### Saving and Quitting

```vim
:w             " save
:q             " quit (fails if unsaved changes)
:wq            " save and quit
:x             " save and quit (only writes if changed)
