# 🧰 12 — Context, Plugins, Remote Docker and Misc Commands

## 🌍 Contexts (Switch Between Docker Hosts)

```bash
# List contexts
docker context ls
# Show the current context
docker context show
# Create a context for a remote host over SSH
docker context create remote --docker "host=ssh://user@192.168.1.10"
# Switch to a context
docker context use remote
# Switch back to the local Docker
docker context use default
# Run one command against a context without switching
docker --context remote ps
# Inspect a context
docker context inspect remote
# Update a context description
docker context update --description "Prod server" remote
# Export a context to a file
docker context export remote
# Import a context from a file
docker context import remote remote.dockercontext
# Remove a context
docker context rm remote
```

## 📡 Remote Docker (Without Contexts)

```bash
# Use an environment variable for the host
export DOCKER_HOST=ssh://user@192.168.1.10
# List containers on the remote host
docker ps
# Remove the variable to go back to local
unset DOCKER_HOST
# One-off command against a remote host over SSH
docker -H ssh://user@192.168.1.10 ps
# One-off command over TCP (unencrypted: trusted networks only)
docker -H tcp://192.168.1.10:2375 ps
```

## 🔌 Plugins

```bash
# List installed plugins
docker plugin ls
# Install a plugin
docker plugin install grafana/loki-docker-driver:latest --alias loki --grant-all-permissions
# Disable a plugin
docker plugin disable loki
# Enable a plugin
docker plugin enable loki
# Plugin details
docker plugin inspect loki
# Change a plugin setting
docker plugin set loki some.setting=value
# Upgrade a plugin
docker plugin upgrade loki grafana/loki-docker-driver:latest
# Create a plugin from a rootfs folder
docker plugin create myplugin ./plugin-dir
# Push a plugin to a registry
docker plugin push myuser/myplugin
# Remove a plugin
docker plugin rm loki
```

## ⏸️ Checkpoints (Experimental, Needs CRIU)

```bash
# Save the state of a running container
docker checkpoint create web cp1
# List checkpoints of a container
docker checkpoint ls web
# Start a container from a checkpoint
docker start --checkpoint cp1 web
# Remove a checkpoint
docker checkpoint rm web cp1
```

## 🧪 Starter Files and Debugging

```bash
# Generate a Dockerfile and compose file for a project (Docker Desktop)
docker init
# Check Docker environment details for bug reports
docker info
# Run a throwaway debug container with network tools
docker run --rm -it nicolaka/netshoot
# Run a throwaway container sharing another container's network
docker run --rm -it --network container:web nicolaka/netshoot
# Run a throwaway shell in an image to explore it
docker run --rm -it --entrypoint sh nginx
# Show what a container will run (image defaults)
docker image inspect --format '{{.Config.Entrypoint}} {{.Config.Cmd}}' nginx
```

## ⌨️ Handy Shell Patterns

```bash
# Stop all running containers
docker stop $(docker ps -q)
# Remove all stopped containers
docker rm $(docker ps -aq -f status=exited)
# Get the IP of every running container
docker ps -q | xargs docker inspect --format '{{.Name}} {{.NetworkSettings.IPAddress}}'
# Shell alias for quick listing
alias dps='docker ps --format "table {{.ID}}\t{{.Names}}\t{{.Status}}\t{{.Ports}}"'
# Shell alias for compose
alias dc='docker compose'
# Enter the newest container's shell
docker exec -it $(docker ps -lq) sh
# Follow logs of the newest container
docker logs -f $(docker ps -lq)
# Count running containers
docker ps -q | wc -l
# List image names and sizes, largest first
docker images --format "{{.Size}}\t{{.Repository}}:{{.Tag}}" | sort -rh
```

---

⬅️ Previous: [11 — Swarm](11-swarm.md) · [Index](README.md)
