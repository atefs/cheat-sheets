# 🖥️ CLI Cheat Sheets

> A curated, community-driven collection of clean and practical cheat sheets
> for developers, DevOps engineers, and power users.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![Contributors](https://img.shields.io/github/contributors/YOUR_ORG/cheat-sheets)](https://github.com/YOUR_ORG/cheat-sheets/graphs/contributors)
[![Stars](https://img.shields.io/github/stars/YOUR_ORG/cheat-sheets?style=social)](https://github.com/YOUR_ORG/cheat-sheets)

---

## 🗺️ Quick Navigation

| Tool | Category | Description |
| ---- | -------- | ----------- |
| [🔀 Git](./git/) | VCS | Version control commands, branching, rebasing |
| [📝 Vim](./vim/) | Editor | Modal editing, motions, macros |
| [🪟 tmux](./tmux/) | Terminal | Session, window and pane management |
| [🐧 Linux Core Utils](./linux-core-utils/) | Shell | grep, awk, sed, find and more |
| [🐳 Docker](./docker/) | Container | Build, run, inspect containers |
| [🐙 Docker Compose](./docker-compose/) | Container | Multi-container orchestration |
| [🔐 SSH](./ssh/) | Network | Remote access, tunneling, key management |
| [🔍 Regex](./regex/) | Universal | Pattern matching across all tools |
| [🍺 Homebrew](./homebrew/) | Package Manager | macOS/Linux package management |
| [🏗️ Terraform](./terraform/) | IaC | Infrastructure as code |
| [📦 apt](./apt/) | Package Manager | Debian/Ubuntu package management |

---

## 📖 How to Use This Repo

### Browse on GitHub

Click any tool in the table above to open the cheat sheet directly in your browser.
GitHub renders Markdown with syntax highlighting, making it easy to scan and copy commands.

### View Raw Markdown

Append `?plain=1` to any GitHub URL, or click **Raw** on any file to get the plain-text source —
useful for piping into a pager: `curl -s <raw-url> | less`.

### Clone Locally

```bash
git clone https://github.com/YOUR_ORG/cheat-sheets.git
cd cheat-sheets
# Open any sheet in your terminal pager
less git/README.md
```

### Search Across All Sheets

```bash
# Find every mention of "rebase" across all cheat sheets
grep -r "rebase" . --include="*.md" -l

# Search with context
grep -r "force-with-lease" . --include="*.md" -A 2
```

---

## 🤝 Contributing

Contributions are welcome! Read [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on adding new cheat sheets, improving existing ones, and the PR process.

---

## 📊 Version Coverage

| Tool | Version Documented | Last Updated |
| ---- | ------------------ | ------------ |
| Git | 2.x | 2025 |
| Vim / Neovim | Vim 8.x / Neovim 0.9+ | 2025 |
| tmux | 3.x | 2025 |
| Linux Core Utils | GNU coreutils 8.x | 2025 |
| Docker | Engine 24+ / Desktop 4.x | 2025 |
| Docker Compose | v2 (plugin) | 2025 |
| SSH | OpenSSH 9.x | 2025 |
| Regex | PCRE2 / ERE / BRE | 2025 |
| Homebrew | 4.x | 2025 |
| Terraform / OpenTofu | 1.6+ | 2025 |
| apt | Debian 12 / Ubuntu 24.04 LTS | 2025 |

---

## 👥 Contributors

Contributors are listed here after their first PR is merged.
See [CONTRIBUTING.md](CONTRIBUTING.md) for how to get started.
