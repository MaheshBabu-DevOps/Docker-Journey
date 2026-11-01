# 📦 03 — Container Commands

> Examples use `nginx` (image) and `web` (container name).

## ▶️ Run: Basics

```bash
# Create and start a container (foreground)
docker run nginx
# Run in the background (detached)
docker run -d nginx
# Give the container a name
docker run -d --name web nginx
# Interactive terminal (get a shell)
docker run -it ubuntu bash
# Remove the container automatically when it exits
docker run --rm alpine echo "hello"
# Run a one-off command
docker run alpine ls /
# Run a specific image tag
docker run -d --name web nginx:1.27
# Always pull the image first
docker run --pull always nginx
# Run for a specific platform
docker run --platform linux/amd64 alpine uname -m
# Same as docker run (long form)
docker container run -d --name web nginx
# Create without starting
docker create --name web nginx
# Create without starting (long form)
docker container create --name web nginx
```

## 🔌 Run: Ports

```bash
# Map host port 8080 to container port 80
docker run -d -p 8080:80 nginx
# Bind to localhost only
docker run -d -p 127.0.0.1:8080:80 nginx
# Map a UDP port
docker run -d -p 5353:53/udp alpine sleep 3600
# Map a range of ports
docker run -d -p 8000-8010:8000-8010 nginx
# Let Docker pick a random host port
docker run -d -p 80 nginx
# Publish all EXPOSEd ports on random host ports
docker run -d -P nginx
```

## 🌱 Run: Environment, Working Dir, User

```bash
# Set an environment variable
docker run -e MODE=prod alpine env
# Set several variables
docker run -e A=1 -e B=2 alpine env
# Load variables from a file
docker run --env-file .env alpine env
# Set the working directory
docker run -w /app alpine pwd
# Run as a specific user
docker run -u 1000:1000 alpine id
# Set the hostname
docker run --hostname myhost alpine hostname
# Override the image entrypoint
docker run --entrypoint /bin/sh alpine -c "echo hi"
# Add a label
docker run -d -l env=dev --name web nginx
# Add several labels from a file
docker run -d --label-file labels.txt --name web nginx
# Write the container ID to a file
docker run -d --cidfile web.cid nginx
# Document a port without publishing it
docker run -d --expose 8080 nginx
```

## 💾 Run: Storage

```bash
# Named volume
docker run -d -v mydata:/data alpine sleep 3600
# Bind mount a host directory
docker run -d -v /home/me/site:/usr/share/nginx/html nginx
# Bind mount in the current directory
docker run -d -v "$(pwd)":/app alpine sleep 3600
# Read-only mount
docker run -d -v /home/me/site:/usr/share/nginx/html:ro nginx
# Same with --mount syntax
docker run -d --mount type=bind,source="$(pwd)",target=/app alpine sleep 3600
# In-memory temporary filesystem
docker run -d --tmpfs /tmp alpine sleep 3600
# Reuse volumes of another container
docker run -d --volumes-from web alpine sleep 3600
# Make the root filesystem read-only
docker run -d --read-only nginx
# Set /dev/shm size
docker run -d --shm-size 256m nginx
```

## 🌐 Run: Network

```bash
# Attach to a custom network
docker run -d --network mynet --name web nginx
# Use the host network
docker run -d --network host nginx
# No networking
docker run -d --network none alpine sleep 3600
# Share another container's network
docker run -d --network container:web alpine sleep 3600
# Network alias
docker run -d --network mynet --network-alias site nginx
# Fixed IP (user-defined network only)
docker run -d --network mynet --ip 172.20.0.10 nginx
# Custom DNS server
docker run --dns 8.8.8.8 alpine cat /etc/resolv.conf
# Add an /etc/hosts entry
docker run --add-host myhost:10.0.0.5 alpine cat /etc/hosts
# Reach the host from the container
docker run --add-host host.docker.internal:host-gateway alpine ping -c1 host.docker.internal
# Set the MAC address
docker run --mac-address 02:42:ac:11:00:99 alpine ip link
```

## 📏 Run: Resource Limits

```bash
# Limit memory
docker run -d --memory 512m nginx
# Limit memory + swap
docker run -d --memory 512m --memory-swap 1g nginx
# Limit CPU (1.5 cores)
docker run -d --cpus 1.5 nginx
# Relative CPU weight
docker run -d --cpu-shares 512 nginx
# Pin to specific CPUs
docker run -d --cpuset-cpus 0,1 nginx
# Limit number of processes
docker run -d --pids-limit 100 nginx
# Set ulimits
docker run -d --ulimit nofile=1024:2048 nginx
# Do not let the kernel OOM-kill the container
docker run -d --memory 512m --oom-kill-disable nginx
# Use GPUs (needs the NVIDIA container toolkit)
docker run --gpus all nvidia/cuda:12.4.0-base-ubuntu22.04 nvidia-smi
```

## 🔁 Run: Restart, Health, Behaviour

```bash
# Restart on failure only
docker run -d --restart on-failure nginx
# Restart on failure, max 5 times
docker run -d --restart on-failure:5 nginx
# Restart always
docker run -d --restart always nginx
# Restart unless manually stopped
docker run -d --restart unless-stopped nginx
# Add a health check
docker run -d --health-cmd "curl -f http://localhost || exit 1" --health-interval 30s nginx
# Disable the image's health check
docker run -d --no-healthcheck nginx
# Use a tiny init process to reap zombies
docker run -d --init nginx
# Change the stop signal
docker run -d --stop-signal SIGINT nginx
# Change the stop timeout (seconds)
docker run -d --stop-timeout 30 nginx
# Set a kernel parameter
docker run --sysctl net.core.somaxconn=1024 alpine sysctl net.core.somaxconn
# Pass a device through
docker run --device /dev/ttyUSB0 alpine ls /dev/ttyUSB0
# Add a supplementary group
docker run --group-add 1001 alpine id
```

## 📝 Run: Logging

```bash
# Choose a log driver
docker run -d --log-driver json-file nginx
# Rotate logs: max size and number of files
docker run -d --log-opt max-size=10m --log-opt max-file=3 nginx
# Disable logging
docker run -d --log-driver none nginx
```

## ⏯️ Lifecycle

```bash
# Start a stopped container
docker start web
# Start and attach to output
docker start -a web
# Start interactively
docker start -ai web
# Start several containers
docker start web db cache
# Stop gracefully (SIGTERM, then SIGKILL after 10 s)
docker stop web
# Stop with a custom timeout
docker stop -t 30 web
# Stop all running containers
docker stop $(docker ps -q)
# Restart a container
docker restart web
# Restart with a timeout
docker restart -t 5 web
# Pause all processes in a container
docker pause web
# Resume a paused container
docker unpause web
# Kill immediately (SIGKILL)
docker kill web
# Send a specific signal
docker kill -s SIGHUP web
# Rename a container
docker rename web web-old
# Block until the container stops, print its exit code
docker wait web
# Same as docker start (long form)
docker container start web
# Same as docker stop (long form)
docker container stop web
```

## 🔄 Update a Running Container

```bash
# Change the restart policy
docker update --restart unless-stopped web
# Change memory limit (set swap too)
docker update --memory 512m --memory-swap 1g web
# Change CPU limit
docker update --cpus 2 web
# Change PIDs limit
docker update --pids-limit 200 web
```

## 📋 List

```bash
# Running containers
docker ps
# All containers, including stopped
docker ps -a
# Only IDs of running containers
docker ps -q
# Only IDs of all containers
docker ps -aq
# The most recently created container
docker ps -l
# The last 3 created containers
docker ps -n 3
# Show container sizes
docker ps -s
# Do not truncate output
docker ps --no-trunc
# Only exited containers
docker ps -a --filter "status=exited"
# Containers by name
docker ps --filter "name=web"
# Containers started from an image
docker ps --filter "ancestor=nginx"
# Containers with a label
docker ps --filter "label=env=dev"
# Unhealthy containers
docker ps --filter "health=unhealthy"
# Containers that exited with a given code
docker ps -a --filter "exited=1"
# Containers publishing a port
docker ps --filter "publish=8080"
# Custom table
docker ps --format "table {{.ID}}\t{{.Names}}\t{{.Status}}\t{{.Ports}}"
# Same as docker ps -a (long form)
docker container ls -a
```

## 🔍 Inspect and Debug

```bash
# Full container details as JSON
docker inspect web
# Include file sizes
docker inspect -s web
# IP address of a container
docker inspect --format '{{.NetworkSettings.IPAddress}}' web
# IP address on a named network
docker inspect --format '{{.NetworkSettings.Networks.mynet.IPAddress}}' web
# Current state (running, exited, ...)
docker inspect --format '{{.State.Status}}' web
# Exit code
docker inspect --format '{{.State.ExitCode}}' web
# Was it OOM-killed?
docker inspect --format '{{.State.OOMKilled}}' web
# Restart policy
docker inspect --format '{{.HostConfig.RestartPolicy.Name}}' web
# Mounts of a container
docker inspect --format '{{json .Mounts}}' web
# Environment variables
docker inspect --format '{{.Config.Env}}' web
# Health status
docker inspect --format '{{.State.Health.Status}}' web
# Inspect only a container (not an image with the same name)
docker inspect --type container web
# Show port mappings
docker port web
# Show the host port for container port 80
docker port web 80
# Show files changed in the container filesystem
docker diff web
# Show running processes in the container
docker top web
# Show processes with ps options
docker top web aux
```

## 📜 Logs

```bash
# Show logs
docker logs web
# Follow logs live
docker logs -f web
# Last 100 lines
docker logs --tail 100 web
# Last 100 lines, then follow
docker logs -f --tail 100 web
# Logs from the last 10 minutes
docker logs --since 10m web
# Logs after a timestamp
docker logs --since 2026-01-01T10:00:00 web
# Logs before a timestamp
docker logs --until 2026-01-01T12:00:00 web
# Add timestamps
docker logs -t web
# Show extra log details
docker logs --details web
```

## 📈 Stats and Events

```bash
# Live CPU, memory, network, I/O usage
docker stats
# One snapshot instead of a live stream
docker stats --no-stream
# Include stopped containers
docker stats --all
# Stats for one container
docker stats web
# Custom stats table
docker stats --no-stream --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}"
# Live events from the daemon
docker events
# Only container events
docker events --filter "type=container"
# Only container start events
docker events --filter "event=start"
# Events for one container
docker events --filter "container=web"
# Events since a time
docker events --since 1h
# Events in a time window
docker events --since 2026-01-01T10:00:00 --until 2026-01-01T11:00:00
```

## 🖥️ Exec, Attach, Copy

```bash
# Open a shell in a running container
docker exec -it web bash
# Use sh if bash is missing
docker exec -it web sh
# Run a single command
docker exec web ls /etc/nginx
# Run in the background
docker exec -d web touch /tmp/started
# Run as root
docker exec -u root web whoami
# Run in a specific directory
docker exec -w /etc/nginx web pwd
# Pass an environment variable
docker exec -e DEBUG=1 web env
# Run a command in a privileged way
docker exec --privileged web id
# Attach to the main process (detach with Ctrl+p then Ctrl+q)
docker attach web
# Attach without forwarding stdin
docker attach --no-stdin web
# Change the detach key sequence
docker attach --detach-keys="ctrl-x" web
# Copy a file from host to container
docker cp ./index.html web:/usr/share/nginx/html/index.html
# Copy a file from container to host
docker cp web:/etc/nginx/nginx.conf ./nginx.conf
# Copy a directory from container to host
docker cp web:/var/log/nginx ./nginx-logs
# Copy and preserve ownership
docker cp -a web:/etc/nginx ./nginx-conf
# Same as docker exec (long form)
docker container exec -it web bash
```

## 🗑️ Remove

```bash
# Remove a stopped container
docker rm web
# Force-remove a running container
docker rm -f web
# Remove a container and its anonymous volumes
docker rm -v web
# Remove several containers
docker rm web db cache
# Remove all stopped containers
docker rm $(docker ps -aq -f status=exited)
# Stop and remove everything
docker rm -f $(docker ps -aq)
# Remove all stopped containers (built-in)
docker container prune
# Prune without confirmation
docker container prune -f
# Prune containers stopped more than 24 hours ago
docker container prune --filter "until=24h"
# Same as docker rm (long form)
docker container rm web
```

---

⬅️ Previous: [02 — Images](02-images.md) · [Index](README.md) · ➡️ Next: [04 — Networks](04-networks.md)
