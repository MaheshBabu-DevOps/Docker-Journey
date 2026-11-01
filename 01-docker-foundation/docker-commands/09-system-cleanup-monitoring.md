# 🧹 09 — System, Cleanup and Monitoring Commands

## 💽 Disk Usage

```bash
# Disk used by images, containers, volumes, build cache
docker system df
# Detailed per-item breakdown
docker system df -v
# Size of Docker's data directory
sudo du -sh /var/lib/docker
# Breakdown by subfolder
sudo du -sh /var/lib/docker/*
# Free disk space on the host
df -h
```

## 🧼 Prune (Safe Cleanup)

```bash
# Remove stopped containers, unused networks, dangling images, build cache
docker system prune
# Prune without confirmation
docker system prune -f
# Also remove ALL unused images (not just dangling)
docker system prune -a
# Also remove unused volumes (data loss risk!)
docker system prune --volumes
# Everything: all unused images and volumes
docker system prune -a --volumes
# Only things older than 24 hours
docker system prune --filter "until=24h"
# Only stopped containers
docker container prune
# Only dangling images
docker image prune
# Only unused images
docker image prune -a
# Only unused networks
docker network prune
# Only unused volumes
docker volume prune
# Only build cache
docker builder prune
```

## 💣 Remove Everything (Careful!)

```bash
# Force-remove all containers
docker rm -f $(docker ps -aq)
# Remove all images
docker rmi -f $(docker images -q)
# Remove all volumes
docker volume rm $(docker volume ls -q)
# Remove all custom networks
docker network rm $(docker network ls -q --filter type=custom)
# Total reset of unused objects
docker system prune -a --volumes -f
```

## 📈 Monitoring

```bash
# Live resource usage of containers
docker stats
# One-time snapshot
docker stats --no-stream
# Include stopped containers
docker stats --all
# Custom columns
docker stats --no-stream --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}\t{{.NetIO}}"
# Processes inside a container
docker top web
# Live event stream from the daemon
docker system events
# Only container die events
docker events --filter "event=die"
# Events from the last hour
docker events --since 1h
# Events as JSON
docker events --format '{{json .}}'
# Events for one image
docker events --filter "image=nginx"
```

## 🩺 Health and Troubleshooting

```bash
# Check whether the daemon is up
docker info > /dev/null && echo "Docker is running"
# Show container state
docker inspect --format '{{.State.Status}}' web
# Why did a container exit? (exit code)
docker inspect --format '{{.State.ExitCode}}' web
# Was it killed for using too much memory?
docker inspect --format '{{.State.OOMKilled}}' web
# Show the last error message of a container
docker inspect --format '{{.State.Error}}' web
# Show container logs (first stop when something breaks)
docker logs --tail 100 web
# List containers that exited with an error
docker ps -a --filter "status=exited" --filter "exited=1"
# List unhealthy containers
docker ps --filter "health=unhealthy"
# Show the health check history
docker inspect --format '{{json .State.Health}}' web
# See which processes the container is running
docker top web
# See changes made to the container filesystem
docker diff web
```

## ⚙️ Daemon Configuration

```bash
# View the daemon config file
cat /etc/docker/daemon.json
# Edit the daemon config file
sudo nano /etc/docker/daemon.json
# Reload config without restarting containers (where supported)
sudo systemctl reload docker
# Restart the daemon to apply changes
sudo systemctl restart docker
# Show the logging driver the daemon uses
docker info --format '{{.LoggingDriver}}'
# Show the cgroup driver
docker info --format '{{.CgroupDriver}}'
# Show the number of running containers
docker info --format '{{.ContainersRunning}}'
# Show the number of images
docker info --format '{{.Images}}'
# Check the storage driver
docker info | grep "Storage Driver"
# Run the daemon in the foreground with debug logs (stop the service first)
sudo dockerd --debug
```

---

⬅️ Previous: [08 — Registry](08-registry.md) · [Index](README.md) · ➡️ Next: [10 — Security](10-security.md)
