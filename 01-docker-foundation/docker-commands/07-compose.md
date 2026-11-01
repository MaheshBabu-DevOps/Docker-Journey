# 🐙 07 — Docker Compose Commands

> Modern Docker uses `docker compose` (with a space, Compose v2). The old `docker-compose` (with a hyphen) is deprecated, but the subcommands are the same.
> Examples use a service called `web`. Run these in the folder with your `docker-compose.yml` (or `compose.yaml`).

## ▶️ Start

```bash
# Create and start all services (foreground)
docker compose up
# Start in the background
docker compose up -d
# Rebuild images before starting
docker compose up -d --build
# Recreate containers even if nothing changed
docker compose up -d --force-recreate
# Start one service
docker compose up -d web
# Start a service without its dependencies
docker compose up -d --no-deps web
# Run 3 copies of a service
docker compose up -d --scale web=3
# Remove containers of services no longer in the file
docker compose up -d --remove-orphans
# Always pull images first
docker compose up -d --pull always
# Wait until services are running/healthy
docker compose up -d --wait
# Stop everything when any container exits
docker compose up --abort-on-container-exit
# Rebuild and restart on file changes
docker compose watch
```

## ⏹️ Stop and Remove

```bash
# Stop and remove containers and networks
docker compose down
# Also remove named volumes
docker compose down -v
# Also remove images used by services
docker compose down --rmi all
# Remove only locally built images
docker compose down --rmi local
# Also remove orphan containers
docker compose down --remove-orphans
# Stop containers without removing them
docker compose stop
# Stop one service
docker compose stop web
# Start stopped containers
docker compose start
# Start one service
docker compose start web
# Restart all services
docker compose restart
# Restart one service
docker compose restart web
# Pause services
docker compose pause
# Resume services
docker compose unpause
# Kill services immediately
docker compose kill
# Remove stopped containers
docker compose rm
# Remove stopped containers without asking
docker compose rm -f
# Create containers without starting them
docker compose create
```

## 🔨 Build and Images

```bash
# Build all services that have a build section
docker compose build
# Build one service
docker compose build web
# Build without cache
docker compose build --no-cache
# Pull the newest base images while building
docker compose build --pull
# Pass a build argument
docker compose build --build-arg VERSION=1.2
# Pull all service images
docker compose pull
# Push all service images
docker compose push
# List images used by the project
docker compose images
```

## 📋 Status and Logs

```bash
# List the project's containers
docker compose ps
# Include stopped containers
docker compose ps -a
# Show only service names
docker compose ps --services
# JSON output
docker compose ps --format json
# List all Compose projects on this host
docker compose ls
# Show logs of all services
docker compose logs
# Follow logs live
docker compose logs -f
# Last 100 lines
docker compose logs --tail 100
# Logs of one service
docker compose logs -f web
# Logs with timestamps
docker compose logs -t
# Show running processes
docker compose top
# Show published ports of a service
docker compose port web 80
# Stream container events
docker compose events
```

## 🖥️ Run Commands in Services

```bash
# Open a shell in a running service
docker compose exec web sh
# Run a command in a service
docker compose exec web ls /app
# Without a TTY (for scripts and CI)
docker compose exec -T web ls /app
# Run as a different user
docker compose exec -u root web sh
# Run a one-off container for a service
docker compose run web sh
# One-off container, removed afterwards
docker compose run --rm web sh
# One-off container with a published port
docker compose run --rm --service-ports web
# One-off container with an env variable
docker compose run --rm -e DEBUG=1 web sh
# Copy a file from host to a service
docker compose cp ./config.yml web:/app/config.yml
# Copy a file from a service to the host
docker compose cp web:/app/log.txt ./log.txt
```

## ⚙️ Config and Files

```bash
# Validate and print the final merged config
docker compose config
# Only validate (no output)
docker compose config -q
# List service names
docker compose config --services
# List images used
docker compose config --images
# List volumes
docker compose config --volumes
# Use a specific compose file
docker compose -f docker-compose.prod.yml up -d
# Merge a base file and an override file
docker compose -f docker-compose.yml -f docker-compose.override.yml up -d
# Show the merged result of two files
docker compose -f docker-compose.yml -f docker-compose.prod.yml config
# Set the project name
docker compose -p myproject up -d
# Use an env file
docker compose --env-file .env.prod up -d
# Enable a profile
docker compose --profile debug up -d
# Show the Compose version
docker compose version
```

## 🔙 Legacy Syntax (docker-compose v1)

```bash
# Old form of docker compose up -d
docker-compose up -d
# Old form of docker compose down
docker-compose down
# Old form of docker compose logs -f
docker-compose logs -f
```

---

⬅️ Previous: [06 — Dockerfile and Build](06-dockerfile-build.md) · [Index](README.md) · ➡️ Next: [08 — Registry](08-registry.md)
