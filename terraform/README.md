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
                                                       terraform destroy
```

> ⚠️ **Never edit `.tfstate` by hand.** The state file is managed exclusively by Terraform. Manual edits corrupt the state and can cause Terraform to create duplicate resources or destroy infrastructure it thinks is missing. Use `terraform state` subcommands for all state manipulation.

---

## 📋 Quick Reference

### Core Workflow

| Command | Description |
| ------- | ----------- |
| `terraform init` | Download providers, set up backend |
| `terraform validate` | Check syntax and configuration validity |
| `terraform fmt` | Auto-format all `.tf` files |
| `terraform plan` | Preview changes |
| `terraform plan -out=tfplan` | Save plan to file |
| `terraform apply` | Apply changes (prompts for confirmation) |
| `terraform apply tfplan` | Apply saved plan (no confirmation) |
| `terraform destroy` | Destroy all managed resources # ⚠️ |
| `terraform apply -target=aws_instance.web` | Apply only a specific resource |

### State Management

| Command | Description |
| ------- | ----------- |
| `terraform show` | Human-readable current state |
| `terraform state list` | List all resources in state |
| `terraform state show aws_s3_bucket.assets` | Show one resource's state |
| `terraform state rm aws_s3_bucket.assets` | Remove resource from state (without destroying) |
| `terraform import aws_s3_bucket.assets my-bucket` | Import existing resource |
| `terraform output` | Show output values |

### Workspaces

| Command | Description |
| ------- | ----------- |
| `terraform workspace list` | List workspaces |
| `terraform workspace new staging` | Create workspace |
| `terraform workspace select staging` | Switch to workspace |
| `terraform workspace show` | Show current workspace |

---

## 🔧 HCL Syntax

```hcl
# ── Provider Configuration ──────────────────────────────────────────────────
terraform {
  required_version = ">= 1.6.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"       # allow 5.x, not 6.x (lock minor version)
    }
  }
}

provider "aws" {
  region = var.region
}

# ── Variables ───────────────────────────────────────────────────────────────
variable "region" {
  description = "AWS region to deploy to"
  type        = string
  default     = "us-east-1"
}

variable "environment" {
  description = "Deployment environment"
  type        = string
  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "environment must be dev, staging, or prod."
  }
}

variable "allowed_ports" {
  description = "List of ports to allow inbound"
  type        = list(number)
  default     = [80, 443]
}

# ── Locals ──────────────────────────────────────────────────────────────────
locals {
  env    = var.environment
  prefix = "myapp-${local.env}"
  tags = {
    Environment = local.env
    ManagedBy   = "terraform"
    Project     = "myapp"
  }
}

# ── Resource ────────────────────────────────────────────────────────────────
resource "aws_s3_bucket" "assets" {
  bucket = "${local.prefix}-assets"
  tags   = local.tags
}

# ── Data Source ─────────────────────────────────────────────────────────────
data "aws_ami" "ubuntu" {
  most_recent = true
  owners      = ["099720109477"]   # Canonical
  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-22.04-amd64-server-*"]
  }
}

# ── Output ──────────────────────────────────────────────────────────────────
output "bucket_arn" {
  description = "S3 bucket ARN for the assets bucket"
  value       = aws_s3_bucket.assets.arn
}

output "bucket_name" {
  value     = aws_s3_bucket.assets.bucket
  sensitive = false
}

# ── Count-based conditionals ─────────────────────────────────────────────────
resource "aws_instance" "bastion" {
  count         = var.environment == "prod" ? 1 : 0
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t3.micro"
  tags          = merge(local.tags, { Name = "${local.prefix}-bastion" })
}

# ── for_each ─────────────────────────────────────────────────────────────────
resource "aws_iam_user" "team" {
  for_each = toset(["alice", "bob", "carol"])
  name     = each.key
  tags     = local.tags
}

# ── Dynamic blocks ───────────────────────────────────────────────────────────
resource "aws_security_group" "web" {
  name   = "${local.prefix}-web-sg"
  vpc_id = aws_vpc.main.id

  dynamic "ingress" {
    for_each = var.allowed_ports
    content {
      from_port   = ingress.value
      to_port     = ingress.value
      protocol    = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
    }
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

### Backend Configuration — Remote State in S3 with DynamoDB Locking

```hcl
# backend.tf
terraform {
  backend "s3" {
    bucket         = "myapp-terraform-state"    # must exist before terraform init
    key            = "prod/terraform.tfstate"   # path within the bucket
    region         = "us-east-1"
    encrypt        = true                       # encrypt state at rest

    # DynamoDB table for state locking (prevents concurrent applies)
    dynamodb_table = "myapp-terraform-locks"
  }
}
```

Create the S3 bucket and DynamoDB table (do this once manually or with a bootstrap module):

```bash
# S3 bucket
aws s3api create-bucket \
  --bucket myapp-terraform-state \
  --region us-east-1

aws s3api put-bucket-versioning \
  --bucket myapp-terraform-state \
  --versioning-configuration Status=Enabled

# DynamoDB table (LockID is the required partition key name)
aws dynamodb create-table \
  --table-name myapp-terraform-locks \
  --attribute-definitions AttributeName=LockID,AttributeType=S \
  --key-schema AttributeName=LockID,KeyType=HASH \
  --billing-mode PAY_PER_REQUEST \
  --region us-east-1
```

### Module Structure

Standard folder layout for a reusable module:

```
modules/
└── ec2-instance/
    ├── main.tf        # resources
    ├── variables.tf   # input variable declarations
    ├── outputs.tf     # output value declarations
    └── versions.tf    # required_providers and terraform version constraint
```

Calling a module:

```hcl
# main.tf (root configuration)
module "web_server" {
  source = "./modules/ec2-instance"   # local module

  # Or from Terraform Registry:
  # source  = "terraform-aws-modules/ec2-instance/aws"
  # version = "~> 5.0"

  # Pass values to the module's variables
  instance_type = "t3.small"
  ami_id        = data.aws_ami.ubuntu.id
  name          = "${local.prefix}-web"
  tags          = local.tags
}

# Reference module outputs
output "web_server_ip" {
  value = module.web_server.public_ip
}
```

---

## 💡 Real-World Examples

### 1. Provision an EC2 instance with a security group

```hcl
# ec2.tf
resource "aws_security_group" "web" {
  name        = "myapp-prod-web-sg"
  description = "Allow HTTP and SSH"
  vpc_id      = data.aws_vpc.default.id

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["203.0.113.0/24"]  # your office IP range
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_instance" "web" {
  ami                    = data.aws_ami.ubuntu.id
  instance_type          = "t3.small"
  vpc_security_group_ids = [aws_security_group.web.id]
  key_name               = "myapp-prod"

  tags = {
    Name        = "myapp-prod-web"
    Environment = "prod"
  }
}
```

```bash
terraform init
terraform plan -out=tfplan
terraform apply tfplan
```

### 2. Remote state in S3 with DynamoDB locking

