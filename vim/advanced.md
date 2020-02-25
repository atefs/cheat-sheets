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

---

_Content being added — work in progress._
