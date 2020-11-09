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
ZZ             " save and quit (Normal mode shortcut)
:q!            " quit without saving
ZQ             " quit without saving (Normal mode shortcut)
:wa            " save all buffers
:qa!           " quit all without saving
```

> ⚠️ `:q!` discards all unsaved changes permanently. Unlike most editors, Vim will not prompt you again. Use `:w` first, or `:wqa` to save-and-quit all buffers.

### Search and Replace

```vim
/pattern       " search forward (regex supported)
?pattern       " search backward
n / N          " next / previous match
*              " search word under cursor (forward)
#              " search word under cursor (backward)

:%s/old/new/g          " replace all occurrences in file
:%s/old/new/gc         " replace all, confirm each
:10,20s/old/new/g      " replace in lines 10–20
:%s/\bword\b/new/g     " whole-word replacement (word boundary)
```

### Buffers and Files

```vim
:e filename    " open file (or create new buffer)
:ls            " list open buffers
:b2            " switch to buffer 2
:b filename    " switch to buffer by name (Tab to complete)
:bd            " close/delete current buffer
:bn / :bp      " next / prev buffer
```

### Windows and Splits

```vim
:sp filename   " horizontal split (open file in split)
:vsp filename  " vertical split
Ctrl-w w       " cycle to next window
Ctrl-w h/j/k/l " navigate windows (left/down/up/right)
Ctrl-w =       " equalize all window sizes
Ctrl-w q       " close current window
Ctrl-w o       " close all windows except current
Ctrl-w r       " rotate windows
```

### Marks

```vim
m{a-z}         " set local mark (lowercase = file-local)
m{A-Z}         " set global mark (uppercase = cross-file, persists)
'{mark}        " jump to mark (first non-blank of line)
`{mark}        " jump to mark (exact cursor position)
:marks         " list all marks
``             " jump back to position before last jump
```

---

## 🌍 Real-World Examples

### 1. Edit a Config File Efficiently

You want to update a specific block in `/etc/nginx/nginx.conf` without touching the rest.

```vim
" Open the file
vim /etc/nginx/nginx.conf

" Search for the server block
/server_name

" Jump inside the braces and replace the entire block content
ci{
" Now type your new block content, then Esc

" Save and quit
:wq
```

> 💡 `ci{` means "change inside curly braces" — it deletes everything between `{` and `}` and drops you into Insert mode. No need to manually select or navigate to the boundaries.

### 2. Multi-Line Search and Replace with Confirmation

You want to rename a function across the whole file but verify each replacement.

```vim
" Replace all occurrences of getUserData with fetchUserProfile, confirm each
:%s/getUserData/fetchUserProfile/gc

" At each match, type:
"   y  — yes, replace
"   n  — no, skip
"   a  — replace all remaining without confirming
"   q  — quit substitution
"   l  — replace this one and quit
```

### 3. Use Marks to Jump Between Two Distant Locations

You are editing two sections far apart in a large file.

```vim
" At the first location, set mark a
ma

" Navigate to the second location, set mark b
mb

" Jump back and forth instantly
'a    " jump to line of mark a (first non-blank)
'b    " jump to line of mark b

" Jump to the exact cursor position (not just line start)
`a
`b
```

> 💡 Use uppercase marks (`mA`, `mB`) to jump across files. `'A` will open the file and jump to the mark, even if that file is not currently open.

### 4. Yank To and From System Clipboard

You want to copy text from Vim and paste it into another app (or vice versa).

```vim
" Copy a line to system clipboard (requires +clipboard build)
"+yy

" Copy a paragraph to system clipboard
"+yap

" Paste from system clipboard
"+p

" Check if clipboard is available
:echo has('clipboard')   " 1 = yes, 0 = no

" In your vimrc/init.lua, make clipboard the default register (all yank/paste uses system clipboard):
set clipboard=unnamedplus         " Vimscript
" vim.opt.clipboard = "unnamedplus"  -- Lua (init.lua)
```

**Clipboard setup by platform:**

| Platform | Requirement | Notes |
| -------- | ----------- | ----- |
| macOS | `brew install neovim` (includes clipboard) | `"+` and `"*` both work |
| Linux (X11) | `sudo apt install xclip` or `xsel` | `"+` = clipboard, `"*` = primary selection |
| Linux (Wayland) | `sudo apt install wl-clipboard` | `"+` works via `wl-copy` |
| Windows (gVim/Neovim native) | Included in installer | `"+` and `"*` both work |
| Windows (WSL + Neovim) | Download [win32yank](https://github.com/equalsraf/win32yank), add to WSL `$PATH` | `"+` routes through `win32yank.exe` |

> 💡 If `has('clipboard')` returns 0: on macOS use `brew install neovim`; on Linux (X11) install `xclip` or `vim-gtk3`; on Linux (Wayland) install `wl-clipboard`; on Windows/WSL add `win32yank.exe` to your WSL PATH.

---

## 💡 Pro Tips

> 💡 **The dot operator is your best friend**
> After any change, `.` repeats it. Change a word with `cw`, type the replacement, press `Esc` — now `.` repeats that change on the next word. Combine with `n` to find the next match and `.` to replace it.

> 💡 **Use `*` to search the word under cursor**
> Place the cursor on a word and press `*` to immediately search forward for all occurrences. Combine with `cgn` (change next match) + `.` for a powerful multi-cursor-style substitute without a plugin.

> 💡 **`ci"` and friends are the fastest edit pattern**
> `ci"` (change inside quotes) immediately deletes the content of the nearest quoted string and enters Insert mode. Works with `()`, `{}`, `[]`, and any delimiter. `ca"` includes the delimiters themselves.

> 💡 **`q:` opens the command history window**
> Press `q:` in Normal mode to see your full `:` command history in a navigable buffer. Move to any line and press `Enter` to re-run it. Press `Ctrl-c` or `:q` to close.

> 💡 **`:g/pattern/command` is a powerful line filter**
> `:g/TODO/d` deletes every line containing TODO. `:g/error/y A` yanks every matching line into register `a` (accumulating). `:g/^$/d` removes all blank lines. This unlocks complex bulk edits without scripting.

> ❌ **Don't navigate with arrow keys in Insert mode**
> It works, but it breaks your flow and trains the wrong muscle memory. Exit to Normal with `Esc` (or `Ctrl-[`), navigate, then re-enter Insert mode. This is faster once internalized.

> ⚠️ **`:q!` discards all unsaved changes permanently**
> Unlike most editors, Vim will not prompt you again after `:q!`. Use `:w` first if you want to keep changes, or `:wqa` to save-and-quit all open buffers at once.

---

## 🚀 Going Further

> 💡 **Want more?** See [advanced.md](./advanced.md) for: macros in depth, registers, visual block mode, `.vimrc` / `init.lua` configuration, and Neovim-specific features (LSP, `init.lua`, plugins).

---

> 💡 **`Ctrl-o` / `Ctrl-i` — navigate the jumplist**
> Every time you jump (search, `G`, mark, etc.) Vim adds the position to the jumplist. `Ctrl-o` goes back, `Ctrl-i` goes forward. Use `:jumps` to see the full list. Great for returning to where you were after exploring a file.

> 💡 **`:set paste` before pasting from outside Vim**
> When pasting text into Insert mode from the system clipboard via the terminal, Vim's auto-indent re-indents each line, producing a staircase effect. Run `:set paste` first, paste, then `:set nopaste`. Or set `set clipboard=unnamedplus` so `p` always pastes from the system clipboard without the problem.

## 📚 Resources

- [Vim official documentation](https://vimdoc.sourceforge.net/) — Built-in `:help` is also excellent; try `:help motion.txt`
- [vimtutor](https://www.openvim.com/) — Run `vimtutor` in your terminal for a 30-minute interactive lesson
- [Practical Vim by Drew Neil](https://pragprog.com/titles/dnvim2/practical-vim-second-edition/) — The best book on Vim
- [vim-adventures.com](https://vim-adventures.com/) — Game-based learning of Vim motions
- [Neovim documentation](https://neovim.io/doc/) — For Neovim-specific features
