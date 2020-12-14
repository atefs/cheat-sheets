> ← Back to [vim/README.md](./README.md) — Core Vim reference

# 📝 Vim Advanced Cheat Sheet

This document covers advanced Vim techniques and Neovim-specific configuration. It assumes you are comfortable with the core motions and operators from [vim/README.md](./README.md).

---

## Table of Contents

- [Macros](#-macros)
- [Registers](#-registers)
- [Marks (Advanced)](#-marks-advanced)
- [Visual Block Mode](#-visual-block-mode-ctrl-v)
- [Command-line Mode Tips](#-command-line-mode-tips)
- [vimrc / init.vim Annotated Config](#-vimrc--initvim-annotated-config)
- [Neovim-Specific Configuration](#-neovim-specific-configuration)
- [Pro Tips](#-pro-tips)
- [Resources](#-resources)

---

## 🎬 Macros

Macros record a sequence of keystrokes into a register and replay them on demand. They are the most powerful automation tool in Vim that requires no scripting.

### Macro Commands

```vim
q{register}    " start recording macro into register (a–z)
q              " stop recording
@{register}    " play macro from register
@@             " repeat last played macro
10@a           " play macro in register 'a' 10 times
:norm @a       " apply macro to current line via :normal
```

### Worked Example: Reformat CSV Lines into a Markdown Table

**Input lines:**

```
alice,30,engineer,new york
bob,25,designer,san francisco
carol,35,manager,austin
```

**Target output:**

```
| alice | 30 | engineer | new york |
| bob | 25 | designer | san francisco |
| carol | 35 | manager | austin |
```

**Record the macro (position cursor at start of first line):**

```vim
qa
I|<Esc>:s/,/ | /g<CR>A |<Esc>j
q
```

**Step-by-step breakdown:**

1. `qa` — begin recording into register `a`
2. `I|<Esc>` — jump to line start (`I`), insert `|`, return to Normal mode
3. `:s/,/ | /g<CR>` — substitute every comma on this line with ` | `
4. `A |<Esc>` — append ` |` at the end of the line, return to Normal mode
5. `j` — move down one line (so the next `@a` or `@@` processes the next line)
6. `q` — stop recording

**Replay on remaining lines:**

```vim
@a         " apply to next line
@@         " apply again (repeat last macro)
10@a       " apply to next 10 lines at once
```

> 💡 Always include the cursor movement (here `j`) at the end of a macro so it is self-advancing. This lets you run `100@a` to process 100 lines without manually repositioning.

### Edit a Macro

If you make a mistake mid-recording, do not re-record — edit the register directly:

```vim
" Paste register a as a new line to inspect it
:put a

" Edit the line as normal text, then yank it back into register a
"ayy

" The macro is now updated
```

---

## 📋 Registers

Registers are named storage slots for text. Understanding them turns copy/paste from a pain point into a superpower.

```vim
"{register}y   " yank into named register (a–z, 0–9, special)
"{register}p   " paste from named register
:reg           " list all register contents
:reg a         " show contents of register a only
```

### Register Reference

| Register | Contents |
| -------- | -------- |
| `""` | Unnamed (default) — last delete or yank |
| `"0` | Last yanked text (not overwritten by deletes) |
| `"1` – `"9` | Delete history: `"1` = most recent delete, `"9` = oldest |
| `"a` – `"z` | Named registers — you control these explicitly |
| `"+` | System clipboard (requires +clipboard build) |
| `"*` | Primary selection clipboard (X11 / Wayland only) |
| `"%` | Current filename |
| `".` | Last inserted text |
| `":` | Last executed Ex command |
| `"/` | Last search pattern |

### Clipboard Registers

```vim
"+y / "+p      " system clipboard register (works on macOS, Windows, Linux with clipboard support)
"*y / "*p      " primary selection clipboard (X11 only — middle-click paste)

" Paste last yank (not the unnamed register, which deletes overwrite)
"0p
```

> 💡 Use `"0p` when you yanked something and then deleted text (which overwrites `""`). Register `"0` always holds the last **yank**, unaffected by deletes.

### Appending to a Register

Uppercase register letters **append** to the register instead of overwriting it:

```vim
" Yank current line into register a (overwrites)
"ayy

" Yank current line and APPEND to register a
"Ayy

" Now "a contains both lines — useful for collecting scattered text
```

---

## 🔖 Marks (Advanced)

```vim
m{a-z}         " set file-local mark (lowercase)
m{A-Z}         " set global mark (persists across files and sessions)
'{mark}        " jump to mark line (first non-blank)
`{mark}        " jump to mark exact position
:marks         " list all marks
:delmarks a    " delete mark a
:delmarks!     " delete all lowercase marks in current file
```

### Automatic (Special) Marks

| Mark | Position |
| ---- | -------- |
| `` `. `` | Position of last change in this file |
| `` `^ `` | Position of last Insert mode exit |
| `` `[ `` | Start of last yank or change |
| `` `] `` | End of last yank or change |
| `` `< `` | Start of last visual selection |
| `` `> `` | End of last visual selection |
| ` `` ` | Position before last jump (jump back) |

> 💡 `` `. `` (backtick-dot) is extremely useful — it jumps to exactly where you last made a change, even after navigating away. A fast way to return to work in progress.

---

## 🟦 Visual Block Mode (`Ctrl-v`)

Visual Block mode selects a rectangular column of text across multiple lines. It enables operations that would otherwise require macros or external tools.

### Basic Commands

```vim
Ctrl-v         " enter Visual Block mode
" Select a column of text with j/k (and optionally $ for full lines), then:
d              " delete the selected column
I text Esc     " insert text before each selected line
A text Esc     " append text after each selected line
r{char}        " replace every character in the block with {char}
> / <          " indent / unindent all selected lines
```

### Column Insert: Comment Out Multiple Lines

```
Before:
server1.company.com
server2.company.com
server3.company.com
```

```vim
" 1. Move cursor to the first character of 'server1'
" 2. Press Ctrl-v to enter Visual Block
" 3. Press jj to select the same column on all 3 lines
" 4. Press I to insert before each line
" 5. Type:  #<Space>
" 6. Press Esc
```

```
After:
# server1.company.com
# server2.company.com
# server3.company.com
```

### Multi-Line Append: Add Semicolons to End of Lines

```vim
" 1. Move to the first line
" 2. Ctrl-v to enter Visual Block
" 3. jjj to select multiple lines
" 4. $ to extend the selection to the end of each line
" 5. A to append after each line
" 6. Type  ;
" 7. Press Esc
```

> 💡 After pressing `I` or `A` in Visual Block mode, your typed text appears on only the first line until you press `Esc` — then Vim applies it to all selected lines simultaneously.

---

## ⌨️ Command-line Mode Tips

The `:` command-line is far more powerful than most users realize. These commands handle bulk text operations that would take minutes manually.

```vim
:g/pattern/d            " delete all lines matching pattern
:g/pattern/y A          " yank all matching lines, append to register a
:v/pattern/d            " delete all lines NOT matching pattern (inverse grep)
:sort                   " sort lines alphabetically
:sort u                 " sort and remove duplicate lines
:sort n                 " sort numerically
:sort! rn               " sort reverse numeric (descending)
:r !command             " read/insert output of shell command below cursor
:r !date                " insert current date at cursor position
:!command               " run shell command (output shown, file unchanged)
:%!python3 -m json.tool " pipe entire file through Python JSON formatter (pretty-print)
```

### Global Command Examples

```vim
" Delete all blank lines in the file
:g/^$/d

" Yank every line containing 'ERROR' into register e
:g/ERROR/y E

" Prepend '> ' to every line (make whole file a blockquote)
:%s/^/> /

" Print all lines containing 'webserver' (without deleting)
:g/webserver/p

" Execute a normal-mode command on every matching line
:g/def /normal >>
```

> 💡 `:g` (global) runs any Ex command on each line matching the pattern. `:v` (vglobal) is the inverse — it runs the command on lines that do NOT match. These two commands replace most scripting for bulk edits.

---

## ⚙️ vimrc / init.vim Annotated Config

Place this in `~/.vimrc` (Vim) or `~/.config/nvim/init.vim` (Neovim using Vimscript).

**Config file paths by platform:**

| Platform | Vim | Neovim |
| -------- | --- | ------ |
| macOS / Linux | `~/.vimrc` | `~/.config/nvim/init.vim` or `~/.config/nvim/init.lua` |
| Windows (native) | `%USERPROFILE%\_vimrc` | `%LOCALAPPDATA%\nvim\init.vim` or `%LOCALAPPDATA%\nvim\init.lua` |
| Windows (WSL) | `~/.vimrc` | `~/.config/nvim/init.lua` (within WSL filesystem) |

> 💡 Run `:echo stdpath("config")` inside Neovim to print the exact config directory on your machine. On **Windows (native)** this is typically `C:\Users\<you>\AppData\Local\nvim`. Create the directory if it does not exist, then place `init.lua` inside it.

```vim
" ============ Basics ============
set number relativenumber     " hybrid line numbers (absolute on current, relative on others)
set tabstop=2 shiftwidth=2 expandtab  " 2-space indentation, spaces not tabs
set ignorecase smartcase      " case-insensitive unless you type an uppercase letter
set clipboard=unnamedplus     " use system clipboard for all yank/paste operations
set undofile                  " persistent undo history — survives closing the file
set splitright splitbelow     " new splits open to the right and below (more natural)
set scrolloff=8               " keep 8 lines visible above/below cursor while scrolling
set cursorline                " highlight the line the cursor is on
set signcolumn=yes            " always show sign column (prevents layout shift with LSP/git)
set updatetime=250            " faster CursorHold events (improves plugin responsiveness)
set termguicolors             " enable true-color (24-bit) support in terminal

" ============ Keymaps ============
let mapleader = " "           " space as leader key (most common choice)

nnoremap <leader>w :w<CR>
nnoremap <leader>q :q<CR>
nnoremap <leader>e :Ex<CR>    " open netrw file explorer

" Keep cursor centered after half-page scroll
nnoremap <C-d> <C-d>zz
nnoremap <C-u> <C-u>zz

" Keep cursor centered when jumping through search results
nnoremap n nzzzv
nnoremap N Nzzzv

" Move selected lines up/down in Visual mode and re-indent
vnoremap J :m '>+1<CR>gv=gv
vnoremap K :m '<-2<CR>gv=gv
```

> 💡 `zz` centers the screen on the cursor. Chaining it after navigation (e.g., `<C-d>zz`) keeps context visible and prevents disorientation in large files.

---

## 🚀 Neovim-Specific Configuration

Neovim supports both Vimscript (`init.vim`) and Lua (`init.lua`). The Lua API is the modern, preferred approach for new configurations.

### `init.lua` Basics

```lua
-- ~/.config/nvim/init.lua

-- Basic options (Lua equivalent of :set)
vim.opt.number = true
vim.opt.relativenumber = true
vim.opt.tabstop = 2
vim.opt.shiftwidth = 2
vim.opt.expandtab = true
vim.opt.ignorecase = true
vim.opt.smartcase = true
vim.opt.clipboard = "unnamedplus"
vim.opt.undofile = true
vim.opt.splitright = true
vim.opt.splitbelow = true
vim.opt.scrolloff = 8
vim.opt.cursorline = true
vim.opt.signcolumn = "yes"
vim.opt.updatetime = 250
vim.opt.termguicolors = true

-- Leader key (set before loading plugins)
vim.g.mapleader = " "

-- Keymaps
local map = vim.keymap.set
map("n", "<leader>w", "<cmd>w<cr>")
map("n", "<leader>q", "<cmd>q<cr>")
map("n", "<C-d>", "<C-d>zz")
map("n", "<C-u>", "<C-u>zz")
map("n", "n", "nzzzv")
map("n", "N", "Nzzzv")
map("v", "J", ":m '>+1<CR>gv=gv")
map("v", "K", ":m '<-2<CR>gv=gv")
```

### Plugin Manager: `lazy.nvim`

> 💡 `lazy.nvim` is the current community standard. You may encounter older configs using `packer.nvim` — it is deprecated and unmaintained. Migration is straightforward: lazy.nvim uses the same plugin spec format and adds parallel loading, lockfile support (`lazy-lock.json`), and a built-in UI.

`lazy.nvim` is the current standard plugin manager for Neovim. It lazy-loads plugins automatically for fast startup.

```lua
-- Bootstrap lazy.nvim (add near the top of init.lua)
local lazypath = vim.fn.stdpath("data") .. "/lazy/lazy.nvim"
if not vim.loop.fs_stat(lazypath) then
  vim.fn.system({
    "git", "clone", "--filter=blob:none",
    "https://github.com/folke/lazy.nvim.git",
    "--branch=stable", lazypath,
  })
end
vim.opt.rtp:prepend(lazypath)

-- Load plugins
require("lazy").setup({
  { "nvim-treesitter/nvim-treesitter", build = ":TSUpdate" },
  { "neovim/nvim-lspconfig" },
  { "hrsh7th/nvim-cmp" },
  {
    "nvim-telescope/telescope.nvim",
    dependencies = { "nvim-lua/plenary.nvim" },
  },
  { "lewis6991/gitsigns.nvim" },
  { "nvim-lualine/lualine.nvim" },
  { "echasnovski/mini.nvim" },
})
```

### Essential Plugins

| Plugin | Purpose |
| ------ | ------- |
| `folke/lazy.nvim` | Plugin manager — lazy-loads plugins for fast startup |
| `nvim-treesitter/nvim-treesitter` | Syntax highlighting and code navigation via AST |
| `neovim/nvim-lspconfig` | Quickstart configs for Neovim's built-in LSP client |
| `hrsh7th/nvim-cmp` | Completion engine with multiple source plugins |
| `nvim-telescope/telescope.nvim` | Fuzzy finder for files, buffers, grep, git, and more |
| `lewis6991/gitsigns.nvim` | Git blame, diff, and hunk navigation in the sign column |
| `nvim-lualine/lualine.nvim` | Fast and configurable statusline written in Lua |
| `echasnovski/mini.nvim` | Collection of small, independent modules (pairs, surround, etc.) |

### LSP Setup

Neovim has a built-in LSP client. `nvim-lspconfig` provides pre-configured setups for most language servers.

```lua
-- In your plugin spec or a separate lua/lsp.lua file:
local lspconfig = require("lspconfig")

-- TypeScript / JavaScript (install: npm install -g typescript-language-server)
lspconfig.ts_ls.setup({})

-- Python (install: pip install pyright)
lspconfig.pyright.setup({})

-- Go (install: go install golang.org/x/tools/gopls@latest)
lspconfig.gopls.setup({})

-- Key mappings applied when LSP attaches to a buffer
vim.api.nvim_create_autocmd("LspAttach", {
  callback = function(args)
    local map = vim.keymap.set
    local opts = { buffer = args.buf }
    map("n", "gd",         vim.lsp.buf.definition,    opts)  -- go to definition
    map("n", "K",          vim.lsp.buf.hover,          opts)  -- hover docs
    map("n", "<leader>rn", vim.lsp.buf.rename,         opts)  -- rename symbol
    map("n", "<leader>ca", vim.lsp.buf.code_action,    opts)  -- code actions
    map("n", "gr",         vim.lsp.buf.references,     opts)  -- list references
  end,
})
```

> 💡 Language servers must be installed separately from Neovim. Use `mason.nvim` (`williamboman/mason.nvim`) to install and manage language servers, linters, and formatters from inside Neovim with `:MasonInstall pyright`.

---

## 💡 Pro Tips

> 💡 **Repeat substitutions with `&`**
> After running `:%s/foo/bar/g`, press `&` on any line to repeat the last substitution on just that line. Use `g&` to repeat it across the whole file. Useful when you want to re-apply a substitution after making manual edits.

> 💡 **`gn` — the motion for the last search pattern**
> `gn` selects the next match of the last search. `cgn` changes the next search match. After one change, `.` repeats it on the subsequent match — this is a composable, composable find-and-replace without running `:%s`.

> 💡 **Edit macros stored in registers**
> Paste a register into a buffer, edit it as plain text, then yank it back. Far easier than re-recording from scratch.
>
> ```vim
> " Paste register a as a new line to inspect and edit
> :put a
>
> " Edit the text, then yank the corrected line back into register a
> "ayy
> ```

> 💡 **`:norm` applies Normal mode commands to a range**
> `:5,10norm A;` appends a semicolon to lines 5–10. `:%norm I// ` prepends `// ` to every line. Combine with `:g` for filtered application.
>
> ```vim
> " Append semicolons to lines 1–20
> :1,20norm A;
>
> " Comment out every line containing 'debug'
> :g/debug/norm I// 
> ```

> ❌ **Don't use arrow keys in Normal mode**
> It works, but it trains the wrong muscle memory. `h/j/k/l` keep your hands on the home row and combine with counts (`5j` = down 5 lines, `12l` = right 12 characters). Navigating with counts is dramatically faster than holding arrow keys.

---

> 💡 **`:checkhealth` — diagnose your Neovim setup**
> Run `:checkhealth` inside Neovim to get a full report of providers (Python, Node, clipboard), plugin requirements, and configuration issues. Each item is marked OK, WARNING, or ERROR with actionable fix instructions. Run it after any new plugin install or on a new machine to quickly identify what is missing.


## 📚 Resources

- [Neovim documentation](https://neovim.io/doc/user/) — Comprehensive Neovim-specific reference; also available via `:help`
- [lazy.nvim README](https://github.com/folke/lazy.nvim) — Plugin manager documentation and configuration examples
- [nvim-lspconfig server configurations](https://github.com/neovim/nvim-lspconfig/blob/master/doc/configs.md) — LSP setup for every supported language
- [Vimcasts](http://vimcasts.org/) — Free screencasts on advanced Vim techniques by Drew Neil
