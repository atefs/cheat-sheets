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
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD wget -qO- http://localhost:3000/health || exit 1

# Use exec form (array) — not shell form (string)
# Exec form: signals reach the process directly
# Shell form: signals go to sh, not your process
CMD ["node", "server.js"]
```

### Multi-Stage Build (keep production images small)

```dockerfile
# Stage 1: Build
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Stage 2: Production image (only the compiled output)
FROM nginx:alpine AS production
# Copy only the built output from stage 1
COPY --from=builder /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]

# Result: production image contains only nginx + dist files
# No node_modules, no source code, no dev dependencies
```

### Dockerfile Instruction Reference

| Instruction | Purpose |
| ----------- | ------- |
| `FROM image:tag` | Base image (every Dockerfile starts here) |
| `WORKDIR /path` | Set working directory (and create it) |
| `COPY src dest` | Copy files from build context into image |
| `RUN command` | Execute command and commit result as a new layer |
| `ENV KEY=value` | Set environment variable (persists in container) |
| `ARG NAME=default` | Build-time variable (not in final image) |
| `EXPOSE port` | Document that the container listens on this port |
| `VOLUME ["/path"]` | Declare a mount point for volumes |
| `USER name` | Set user for subsequent RUN/CMD/ENTRYPOINT |
| `ENTRYPOINT ["cmd"]` | Main executable (exec form recommended) |
| `CMD ["arg"]` | Default arguments for ENTRYPOINT (or default command) |
| `HEALTHCHECK ...` | Command to test container health |
| `LABEL key=value` | Add metadata (author, version, etc.) |

### CMD vs ENTRYPOINT

```dockerfile
# CMD alone — easily overridden at runtime:
CMD ["nginx", "-g", "daemon off;"]
# docker run myimage              → runs nginx
# docker run myimage /bin/sh      → overrides CMD, runs /bin/sh

# ENTRYPOINT alone — always runs, args appended:
ENTRYPOINT ["nginx"]
# docker run myimage -g "daemon off;"   → nginx -g "daemon off;"

# ENTRYPOINT + CMD — ENTRYPOINT is the executable, CMD is default args:
ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["postgres"]
# docker run myimage               → docker-entrypoint.sh postgres
# docker run myimage pg_dump       → docker-entrypoint.sh pg_dump
```

> 💡 **Use ENTRYPOINT for the main process, CMD for default arguments.** This pattern is used by all official Docker Hub images (postgres, redis, nginx, etc.).

---

## 🚀 Real-World Examples

### 1. Containerize a Node.js Application

> You have a Node.js app and want to build and run it in Docker.

```bash
# Create a Dockerfile (see best-practice template above)
# Then build:
docker build -t myapp:1.0 .

# Test locally
docker run -d --name myapp -p 3000:3000 myapp:1.0

# Verify it's running
curl http://localhost:3000/health

# View logs
docker logs -f myapp

# Tag and push to registry
docker tag myapp:1.0 registry.company.com/myapp:1.0
docker push registry.company.com/myapp:1.0
```

### 2. Run a Local Postgres Database with Persistent Data

> You need a Postgres instance for local development that persists data between container restarts.

```bash
# Create a named volume so data persists
docker volume create pgdata

# Run Postgres
docker run -d \
  --name postgres-dev \
  -e POSTGRES_DB=myapp \
  -e POSTGRES_USER=alice \
  -e POSTGRES_PASSWORD=localdev \
  -v pgdata:/var/lib/postgresql/data \
  -p 5432:5432 \
  postgres:16

# Connect with psql
docker exec -it postgres-dev psql -U alice -d myapp

# Or from your host machine (if psql is installed):
psql -h localhost -U alice -d myapp
```

### 3. Debug a Container That Won't Start

> Your container exits immediately after `docker run`. How to diagnose.

```bash
# Check what happened
docker ps -a                          # see exit code

# View logs from the failed container
docker logs myapp

# Run interactively with a shell to inspect the environment
docker run -it --entrypoint /bin/sh myapp:1.0

# Inside the container, check:
env                                   # environment variables
ls -la /app                           # files present?
cat /app/server.js                    # config correct?

# Check image layers for clues
docker history myapp:1.0
```

### 4. Clean Up Disk Space Used by Docker

> Docker accumulates stopped containers, dangling images, and unused volumes over time.

```bash
# Check how much space Docker is using
docker system df

# Step 1: remove stopped containers
docker container prune

# Step 2: remove dangling images (untagged layers)
docker image prune

# Step 3: remove unused volumes (careful — may contain data)
docker volume prune                   # ⚠️ prompts for confirmation

# Nuclear option: remove everything unused
docker system prune -a --volumes      # ⚠️ removes ALL unused images + volumes
```

---

## 💡 Pro Tips

> 💡 **Pin image tags in production**
> `FROM node:20-alpine` changes with every release. In production Dockerfiles, use the full digest: `FROM node:20.11.0-alpine3.19` or the SHA digest. This makes builds reproducible.

> 💡 **Order Dockerfile layers by change frequency**
> Docker caches layers. Put rarely-changed instructions (OS packages, dependency install) before frequently-changed ones (COPY source code). This keeps builds fast.

> 💡 **`.dockerignore` — exclude what the container doesn't need**
> Create `.dockerignore` alongside your Dockerfile:
> ```
> node_modules
> .git
> .env
> *.log
> dist
> coverage
> ```
> Without it, `COPY . .` sends your entire `node_modules` (potentially gigabytes) to the build daemon.

> 💡 **Use `docker exec` instead of SSH**
> There's no need to run an SSH server in your containers. `docker exec -it container bash` gives you an interactive shell instantly.

> ❌ **Never run containers as root in production**
> The default is root inside the container. If an attacker escapes the container, they have root access to the host. Always add `USER nonroot` in your Dockerfile.

> ⚠️ **`docker system prune -a` removes images you might need**
> This command removes all images not referenced by a running container — including ones you've built locally but aren't currently running. Run `docker images` first to see what would be affected.

---

## 🔧 Troubleshooting

| Problem | Likely Cause | Fix |
| ------- | ------------ | --- |
| Container exits immediately | Process crashes on start | `docker logs container-name` to see output |
| `port is already allocated` | Host port in use | Change host port: `-p 8081:80` |
| `cannot connect to Docker daemon` | Docker not running | Start Docker Desktop or `sudo systemctl start docker` |
| Container can't reach internet | DNS or network config | Try `--network host` for testing; check firewall |
| `no space left on device` | Docker disk full | `docker system prune -a` |
| Changes not reflected after rebuild | Old container still running | `docker rm -f container && docker run ...` |
| `exec format error` | Wrong CPU architecture | Ensure image arch matches host: `--platform linux/amd64` |

---

> 💡 **`docker exec -it` — open a shell in a running container**
> `docker exec -it mycontainer bash` drops you into a Bash shell inside a running container. Use `sh` if `bash` is not installed (Alpine-based images). Add `-e VAR=value` to inject environment variables for the session. The container keeps running after you exit.

> 💡 **Multi-stage builds keep production images lean**
> Use `FROM node:20 AS builder` to compile, then `FROM node:20-alpine` and `COPY --from=builder /app/dist ./dist` to copy only the output. The final image excludes build tools and dev dependencies. A 800 MB build image commonly yields a 50 MB production image.

> 💡 **Always use a `.dockerignore` file**
> Without it, `COPY . .` sends `node_modules`, `.git`, and local secrets to the Docker build context. Create `.dockerignore` with at minimum: `node_modules`, `.git`, `.env`, `*.log`. A smaller build context means faster builds and smaller layers even before the final image is optimised.

## 📚 Resources

- [Docker Documentation](https://docs.docker.com/) — Comprehensive official reference
- [Dockerfile best practices](https://docs.docker.com/build/building/best-practices/) — Official guide to writing efficient Dockerfiles
- [Docker Hub](https://hub.docker.com/) — Official image registry
- [Play with Docker](https://labs.play-with-docker.com/) — Free browser-based Docker playground
- [dive](https://github.com/wagoodman/dive) — Tool for exploring Docker image layers

> 💡 **`docker build --progress=plain` — see full build output for debugging**
> By default, BuildKit shows a condensed, animated progress view that hides individual command output. When a build step fails, add `--progress=plain` to get the full scrollable output of every RUN instruction. Essential for diagnosing failures in CI or complex multi-stage builds.
