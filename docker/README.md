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
```

---

## 📋 Quick Reference

### Images

| Command | Description |
| ------- | ----------- |
| `docker pull nginx:1.25` | Pull image from registry |
| `docker images` | List local images |
| `docker images -a` | Include intermediate images |
| `docker rmi nginx:1.25` | Remove image |
| `docker build -t myapp:1.0 .` | Build from Dockerfile in current dir |
| `docker tag myapp:1.0 registry.company.com/myapp:1.0` | Tag image |
| `docker push registry.company.com/myapp:1.0` | Push to registry |
| `docker image prune` | Remove dangling images |
| `docker image prune -a` | Remove all unused images |
| `docker history nginx:1.25` | Show layer history |

### Containers

| Command | Description |
| ------- | ----------- |
| `docker run nginx` | Run container (foreground) |
| `docker run -d nginx` | Run detached |
| `docker run -it ubuntu bash` | Interactive shell |
| `docker run --name webserver nginx` | Named container |
| `docker run -p 8080:80 nginx` | Map host port 8080 → container port 80 |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers |
| `docker stop webserver` | Graceful stop (SIGTERM) |
| `docker kill webserver` | Force stop (SIGKILL) |
| `docker rm webserver` | Remove stopped container |
| `docker rm -f webserver` | Force remove running container |
| `docker exec -it webserver bash` | Shell into running container |
| `docker logs -f webserver` | Follow container logs |
| `docker inspect webserver` | Full JSON metadata |
| `docker stats` | Live resource usage |

### Volumes

| Command | Description |
| ------- | ----------- |
| `docker volume create pgdata` | Create named volume |
| `docker volume ls` | List volumes |
| `docker volume inspect pgdata` | Show volume details |
| `docker volume rm pgdata` | Remove volume |
| `docker volume prune` | Remove all unused volumes |

### Networks

| Command | Description |
| ------- | ----------- |
| `docker network create mynet` | Create network |
| `docker network ls` | List networks |
| `docker network inspect mynet` | Show network details |
| `docker network rm mynet` | Remove network |
| `docker network connect mynet webserver` | Connect container to network |

### Cleanup

| Command | Description |
| ------- | ----------- |
| `docker system prune` | Remove stopped containers + dangling images + unused networks |
| `docker system prune -a` | Also remove all unused images # ⚠️ |
| `docker system df` | Show disk usage breakdown |
| `docker container prune` | Remove all stopped containers |

---

## 💻 Common Commands

### Running Containers

```bash
# Basic run
docker run nginx

# Detached, named, port-mapped
docker run -d --name webserver -p 8080:80 nginx:1.25

# With environment variables
docker run -d \
  --name postgres \
  -e POSTGRES_PASSWORD=secret \
  -e POSTGRES_DB=myapp \
  -p 5432:5432 \
  postgres:16

# With named volume (data persists after container removal)
docker run -d \
  --name postgres \
  -v pgdata:/var/lib/postgresql/data \
  -e POSTGRES_PASSWORD=secret \
  postgres:16

# With bind mount (local dir synced into container)
docker run -d \
  --name webserver \
  -v "$(pwd)/html:/usr/share/nginx/html:ro" \
  -p 8080:80 \
  nginx

# Resource limits
docker run -d \
  --name api \
  --memory="512m" \
  --cpus="0.5" \
  myapp:1.0

# Environment file
docker run -d --env-file .env myapp:1.0
```

### Inspecting and Debugging

```bash
docker logs webserver                    # view logs
docker logs -f webserver                 # follow logs live
docker logs --tail 100 webserver         # last 100 lines
docker logs --since 10m webserver        # logs from last 10 minutes

docker exec -it webserver bash           # interactive shell
docker exec -it webserver sh             # if bash not available
docker exec webserver cat /etc/nginx/nginx.conf   # single command

docker inspect webserver                 # full JSON metadata
docker inspect webserver | jq '.[0].NetworkSettings.IPAddress'   # extract field

docker stats                             # live CPU/memory/network for all containers
docker stats webserver                   # single container
docker top webserver                     # processes inside container
```

### Volumes

```bash
docker volume create pgdata
docker volume ls
docker volume inspect pgdata
docker volume rm pgdata
docker volume prune                     # remove all unused volumes ⚠️
```

### Networks

```bash
docker network create mynet
docker network ls
docker run -d --name api --network mynet myapp:1.0
docker run -d --name db  --network mynet postgres:16
# 'api' container can reach 'db' by hostname 'db'
```

### Cleanup

```bash
docker system prune                     # remove stopped containers + dangling images + unused networks
docker system prune -a                  # also remove all unused images ⚠️
docker system df                        # show disk usage breakdown
docker container prune                  # remove all stopped containers
```

---

## 📄 Dockerfile Reference

### Annotated Best-Practice Dockerfile

```dockerfile
# Start from an official, minimal base image
# Use specific tags — never 'latest' in production
FROM node:20-alpine AS base

# Set working directory (creates it if it doesn't exist)
WORKDIR /app

# Copy dependency manifests FIRST (before source code)
# This layer is cached — only re-runs when package.json changes
COPY package*.json ./

# Install dependencies (ci = clean install, respects package-lock.json)
RUN npm ci --only=production

# Copy application source code
# This layer re-runs on every code change
COPY . .

# Create a non-root user for security
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

# Document the port the app listens on (informational — does not publish it)
EXPOSE 3000

# HEALTHCHECK tells Docker how to test if the container is healthy
