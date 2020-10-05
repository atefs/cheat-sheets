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
docker compose logs                  # all services
docker compose logs -f web           # follow a specific service
docker compose logs --tail=50 db     # last 50 lines from db service

# Run commands
docker compose exec web sh           # interactive shell in running service
docker compose run --rm web npm test # one-off command in new container (auto-removes)
docker compose run --rm web npm install some-package

# Scale
docker compose up -d --scale web=3  # run 3 instances of web service

# Status and inspection
docker compose ps                    # list containers + their state
docker compose top                   # processes running inside each container
docker compose config                # print merged/resolved config (debug overrides)

# Pull and push
docker compose pull                  # pull latest versions of all images
docker compose push                  # push built images to registry
```

---

## 🚀 Real-World Examples

### 1. Full-Stack Web App: Node.js + Postgres + Redis

> You want to bring up all three services with a single command for local development.

```bash
# Start everything (first time — builds the web image)
docker compose up --build

# Once images are built, subsequent starts are faster
docker compose up -d

# Check everything is healthy
docker compose ps

# Follow the web service logs
docker compose logs -f web

# Connect to the database directly for debugging
docker compose exec db psql -U alice -d myapp

# Run a one-off migration
docker compose run --rm web npm run db:migrate

# Tear down (keep volumes — data preserved)
docker compose down

# Tear down including all data ⚠️
docker compose down -v
```

### 2. Override Files: Dev vs Prod Configuration

> You want hot-reload and exposed debug ports in dev, but not in production.

Directory layout:

```
myapp/
├── compose.yml           # base config (committed)
├── compose.override.yml  # dev overrides (committed or git-ignored)
├── compose.prod.yml      # production overrides (committed)
└── .env                  # local secrets (git-ignored)
```

```bash
# Development (auto-merges compose.override.yml)
docker compose up

# Production (explicit override, no compose.override.yml)
docker compose -f compose.yml -f compose.prod.yml up -d

# Validate merged config before deploying
docker compose -f compose.yml -f compose.prod.yml config
```

### 3. Multi-Environment Setup with `--env-file` and `--file` Flags

> You maintain separate env files for staging and production.

```bash
# Staging
docker compose --env-file .env.staging up -d

# Production (with production compose file)
docker compose -f compose.yml -f compose.prod.yml \
  --env-file .env.production \
  up -d

# Show resolved config for production (no actual start)
docker compose -f compose.yml -f compose.prod.yml \
  --env-file .env.production \
  config
```

---

## 💡 Pro Tips

> 💡 **`docker compose config` is your best debugging tool**
> Run it to see the fully merged and interpolated compose file — all variables substituted, all override files applied. Catches misconfiguration before you start anything.

> 💡 **`depends_on` with `condition: service_healthy` prevents startup race conditions**
> Plain `depends_on: [db]` only waits for the container to start, not for Postgres to be ready. Add a `healthcheck` to your db service and use `condition: service_healthy` in `depends_on` to wait for actual readiness.

> 💡 **Named volumes vs bind mounts**
> Use named volumes (`pgdata:/var/lib/postgresql/data`) for databases and persistent state — Docker manages the location and they're portable. Use bind mounts (`.:/app`) for source code that you want to edit and see reflected live in the container.

> 💡 **The anonymous volume trick for `node_modules`**
> When using a bind mount for your app code, add an anonymous volume for `node_modules`:
> ```yaml
> volumes:
>   - .:/app
>   - /app/node_modules    # prevents host node_modules from overwriting container's
> ```
> Without this, your host's `node_modules` (or empty directory) overwrites the container's installed packages.

> ❌ **Don't put secrets in `compose.yml`**
> Environment variables in `compose.yml` are committed to git. Use an `.env` file (git-ignored) with `env_file:` in the service definition, or Docker secrets for production.

---

## 📚 Resources

- [Docker Compose documentation](https://docs.docker.com/compose/) — Official reference
- [Compose file reference](https://docs.docker.com/compose/compose-file/) — Full YAML key reference
- [Migrate from v1 to v2](https://docs.docker.com/compose/migrate/) — Guide for updating old `docker-compose.yml` files
