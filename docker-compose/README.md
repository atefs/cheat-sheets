# 🐙 Docker Compose Cheat Sheet

[![Official Docs](https://img.shields.io/badge/docs-docs.docker.com-blue)](https://docs.docker.com/compose/)
[![Compose v2](https://img.shields.io/badge/Compose-v2-brightgreen)](https://docs.docker.com/compose/migrate/)

Docker Compose defines and runs multi-container applications from a single YAML file. Instead of juggling multiple `docker run` commands, you describe all services, volumes, and networks declaratively and bring everything up with one command. Version: Compose v2 (docker compose plugin) (2024)

> 💡 **Compose v2 vs v1:** Since Docker Desktop 3.4 and Docker Engine 20.10.13, Compose is built into the Docker CLI as `docker compose` (no hyphen). The standalone `docker-compose` binary (v1) is deprecated. This sheet covers Compose v2. If you see `docker-compose` in older docs, substitute `docker compose`.

> 📦 **New to Docker?** Read [docker/README.md](../docker/README.md) first for container fundamentals.

---

## Table of Contents

- [Installation](#installation)
- [Core Concepts](#-core-concepts)
- [Anatomy of compose.yml](#-anatomy-of-composeyml)
  - [compose.override.yml — Local Development Overrides](#composeoverrideyml--local-development-overrides)
- [Common Commands](#-common-commands)
- [Real-World Examples](#-real-world-examples)
- [Pro Tips](#-pro-tips)
- [Resources](#-resources)

---

## Installation

Compose v2 ships bundled with Docker Desktop (macOS/Windows). On Linux:

```bash
# Compose v2 is included with Docker Engine via the plugin:
sudo apt install docker-compose-plugin    # Ubuntu/Debian

# Verify
docker compose version
```

---

## 🧱 Core Concepts

- **Service** — a container definition; maps to one container (or multiple with `scale`). The service name becomes the DNS hostname for inter-container communication.
- **`compose.yml`** — the declarative manifest. Also accepts `docker-compose.yml` for backwards compatibility.
- **Project** — a set of services sharing a network and named with a common prefix. Default: directory name.
- **Override files** — `compose.override.yml` is automatically merged with `compose.yml`. Used for local dev overrides.

---

## 📝 Anatomy of `compose.yml`

The following annotated example covers the most commonly used keys:

```yaml
# compose.yml (or docker-compose.yml)
name: myapp                      # project name (default: directory name)

services:
  web:                           # service name (also the DNS hostname)
    build:
      context: .                 # build from Dockerfile in current directory
      dockerfile: Dockerfile     # explicit Dockerfile name (optional)
      args:
        NODE_ENV: production     # ARG values passed to Dockerfile build
    image: myapp:dev             # OR use a pre-built image instead of build:
    ports:
      - "8080:3000"              # host_port:container_port
    environment:
      - NODE_ENV=development
      - DATABASE_URL=postgres://alice:secret@db:5432/myapp
    env_file:
      - .env                     # load additional vars from .env file
    depends_on:
      db:
        condition: service_healthy  # wait for db healthcheck to pass
    volumes:
      - .:/app                   # bind mount: local dir → container (hot reload)
      - /app/node_modules        # anonymous volume: exclude node_modules from bind mount
    networks:
      - backend
    restart: unless-stopped      # restart policy: no | always | on-failure | unless-stopped
    healthcheck:
      test: ["CMD", "wget", "-qO-", "http://localhost:3000/health"]
      interval: 30s
      timeout: 5s
      retries: 3

  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: myapp
      POSTGRES_USER: alice
      POSTGRES_PASSWORD: secret
    volumes:
      - pgdata:/var/lib/postgresql/data   # named volume for persistence
    networks:
      - backend
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U alice -d myapp"]
      interval: 5s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    volumes:
      - redisdata:/data
    networks:
      - backend

volumes:
  pgdata:       # named volume — Docker manages the storage location
  redisdata:

networks:
  backend:
    name: myapp-backend    # explicit network name (otherwise prefixed with project name)
```

### compose.override.yml — Local Development Overrides

`compose.override.yml` is automatically merged when you run `docker compose up`. Fields are merged (not replaced) — the override adds to or overrides specific keys without replacing the entire service definition. Use it for dev-specific settings you don't want in the committed `compose.yml`.

```yaml
# compose.override.yml — automatically merged with compose.yml
# Do NOT commit this file if it contains local secrets or paths

services:
  web:
    environment:
      - DEBUG=true
      - LOG_LEVEL=debug
    volumes:
      - .:/app                   # bind mount for hot reload (dev only)
    ports:
      - "9229:9229"              # expose Node.js debugger port (dev only)

  db:
    ports:
      - "5432:5432"              # expose db port to host for local tooling (dev only)
```

---

## 💻 Common Commands

```bash
# Start services
docker compose up                    # foreground, show all logs, build if missing
docker compose up -d                 # detached (background)
docker compose up --build            # force rebuild images before starting
docker compose up --build web        # rebuild and start only the web service
docker compose up web redis          # start specific services (and their dependencies)

# Stop and remove
docker compose down                  # stop + remove containers and networks
docker compose down -v               # also remove named volumes ⚠️
docker compose stop                  # stop containers (keeps them, no removal)
docker compose restart web           # restart a specific service

# Logs
