# 🏗️ Terraform Cheat Sheet

Terraform (and its open-source fork OpenTofu) is an infrastructure-as-code tool that lets you define cloud and on-premises resources in declarative HCL configuration files, then create, update, and destroy them safely and predictably. Version: Terraform / OpenTofu 1.6+ (2024)

[![Official Docs](https://img.shields.io/badge/docs-terraform.io-blue)](https://developer.hashicorp.com/terraform/docs)
[![OpenTofu](https://img.shields.io/badge/OpenTofu-1.6%2B-green)](https://opentofu.org/docs/)
[![License](https://img.shields.io/badge/license-MIT-green)](../LICENSE)

---

## Table of Contents

- [Installation](#installation)
- [Core Concepts](#-core-concepts)
- [Quick Reference](#-quick-reference)
- [HCL Syntax](#-hcl-syntax)
  - [Backend Configuration](#backend-configuration--remote-state-in-s3-with-dynamodb-locking)
  - [Module Structure](#module-structure)
- [Real-World Examples](#-real-world-examples)
- [Pro Tips](#-pro-tips)
- [Troubleshooting](#-troubleshooting)
- [Resources](#-resources)

---

## Installation

```bash
# macOS
brew tap hashicorp/tap
brew install hashicorp/tap/terraform

# Ubuntu/Debian
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(. /etc/os-release && echo "$VERSION_CODENAME") main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform

# Version manager (recommended)
brew install tfenv                  # manage multiple Terraform versions
tfenv install 1.6.6
tfenv use 1.6.6
```

Verify: `terraform version`

---

## 🧠 Core Concepts

**Provider** — a plugin that knows how to talk to a specific API (AWS, GCP, Azure, GitHub, Kubernetes). Declared in `required_providers`, downloaded automatically by `terraform init`.

**Resource** — a piece of infrastructure to create and manage: an S3 bucket, an EC2 instance, a DNS record. The main building block of every Terraform configuration.

**Data source** — reads existing infrastructure without managing it. Use for looking up AMIs, VPCs, or any read-only values that your resources need to reference.

**State** — a JSON file (`.tfstate`) that maps your configuration to real infrastructure. Terraform uses it to compute diffs between desired and actual state. Must be treated as a source of truth and never edited by hand.

**Plan** — a preview of what Terraform will do: create, update, or destroy resources. Always review the plan before applying. Save it with `-out=tfplan` so the apply step runs exactly what you reviewed.

**Workspace** — isolated state per environment (dev/staging/prod) within a single configuration directory. Useful for managing multiple deployments of the same configuration.

**Module** — a reusable group of resources with inputs (variables) and outputs. The foundation of DRY infrastructure code. Modules can be local or sourced from the Terraform Registry.

### Workflow

```
Write .tf files ──► terraform init ──► terraform plan ──► terraform apply
                                                              │
                                                       terraform.tfstate
                                                              │

---

_Content being added — work in progress._
