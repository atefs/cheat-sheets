# 🐳 Docker Cheat Sheet

[![Official Docs](https://img.shields.io/badge/docs-docs.docker.com-blue)](https://docs.docker.com)
[![License](https://img.shields.io/badge/license-MIT-green)](../LICENSE)

Docker packages applications and their dependencies into containers — isolated, reproducible runtime environments. A container built on your laptop runs identically on any server or cloud. Version: Docker Engine 24+ / Docker Desktop 4.x (2024)

---

## Table of Contents

- [Installation](#installation)
- [Core Concepts](#-core-concepts)
- [Quick Reference](#-quick-reference)
  - [Images](#images)
  - [Containers](#containers)
  - [Volumes](#volumes)
  - [Networks](#networks)
  - [Cleanup](#cleanup)
- [Common Commands](#-common-commands)
  - [Running Containers](#running-containers)
  - [Inspecting and Debugging](#inspecting-and-debugging)
  - [Volumes](#volumes-1)
  - [Networks](#networks-1)
  - [Cleanup](#cleanup-1)
- [Dockerfile Reference](#-dockerfile-reference)
  - [Annotated Best-Practice Dockerfile](#annotated-best-practice-dockerfile)
  - [Multi-Stage Build](#multi-stage-build-keep-production-images-small)
  - [Dockerfile Instruction Reference](#dockerfile-instruction-reference)
  - [CMD vs ENTRYPOINT](#cmd-vs-entrypoint)
- [Real-World Examples](#-real-world-examples)
- [Pro Tips](#-pro-tips)
- [Troubleshooting](#-troubleshooting)
- [Resources](#-resources)

---

## Installation

| Platform | Command |
| -------- | ------- |
| macOS | Docker Desktop: https://docs.docker.com/desktop/mac/ |
| Ubuntu/Debian | `curl -fsSL https://get.docker.com \| sh` (official script) |
| Fedora | `sudo dnf install docker-ce` (after adding Docker repo) |
| Windows | Docker Desktop with WSL2 backend |

After installing, verify your setup:

```bash
docker version
docker run hello-world
```

---

## 🧱 Core Concepts

- **Image** — read-only blueprint made of layers; built from a Dockerfile or pulled from a registry
- **Container** — a running instance of an image; has its own filesystem, network namespace, and process space. Ephemeral by default — stopping and removing a container discards all changes unless volumes are used.
- **Registry** — stores and distributes images. Docker Hub (`docker.io`) is the default public registry.
- **Volume** — persistent storage that lives outside the container lifecycle. Data survives container removal.
- **Network** — virtual network bridge for containers to communicate using hostnames.

```
Dockerfile ──build──► Image ──run──► Container
                          │
                     Registry (push/pull)

Container ──► Volumes  (persistent data)
          └─► Networks (inter-container communication)

---

_Content being added — work in progress._
