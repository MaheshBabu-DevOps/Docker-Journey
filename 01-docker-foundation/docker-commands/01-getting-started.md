# 🚀 01 — Getting Started: Service, Version, Help, Login

> Examples use `nginx` (image), `web` (container), `myuser` (username). Replace them with your own names.

## 🔧 Docker Service (systemd)

```bash
# Check whether the Docker daemon is running
sudo systemctl status docker
# Start the daemon
sudo systemctl start docker
# Stop the daemon
sudo systemctl stop docker
# Restart the daemon
sudo systemctl restart docker
# Start Docker automatically on boot
sudo systemctl enable docker
# Do not start Docker on boot
sudo systemctl disable docker
# Status of the Docker socket unit
sudo systemctl status docker.socket
# Show daemon logs
sudo journalctl -u docker.service
# Follow daemon logs live
sudo journalctl -u docker -f
# Daemon logs from the last hour
sudo journalctl -u docker --since "1 hour ago"
```

## 👤 Run Docker Without sudo

```bash
# Add your user to the docker group
sudo usermod -aG docker $USER
# Apply the group in the current shell (or log out and in)
newgrp docker
# Show your groups (docker should be listed)
id -nG
# Check permissions on the Docker socket
ls -l /var/run/docker.sock
```

## ℹ️ Version and Info

```bash
# Client version (short)
docker --version
# Client version (short flag)
docker -v
# Client and server (daemon) versions
docker version
# Only the server version
docker version --format '{{.Server.Version}}'
# Only the client version
docker version --format '{{.Client.Version}}'
# System-wide information
docker info
# Only the daemon version
docker info --format '{{.ServerVersion}}'
# Storage driver in use
docker info --format '{{.Driver}}'
# Where Docker stores its data
docker info --format '{{.DockerRootDir}}'
# Security options enabled on the daemon (seccomp, apparmor, ...)
docker info --format '{{.SecurityOptions}}'
# Same as docker info
docker system info
# Docker Compose version
docker compose version
# Buildx version
docker buildx version
```

## ❓ Help

```bash
# List all commands
docker
# List all commands and global options
docker --help
# Same as --help
docker help
# Help for one command
docker run --help
# Help for the image command group
docker image --help
# Help for the container command group
docker container --help
# Help for Compose
docker compose --help
# Generate bash tab-completion
docker completion bash
```

## 👋 Your First Containers

```bash
# Verify the installation
docker run hello-world
# Interactive Ubuntu shell
docker run -it ubuntu bash
# Nginx in the background on port 8080
docker run -d -p 8080:80 nginx
# List running containers
docker ps
# List all containers, including stopped
docker ps -a
```

## 🔑 Login and Logout

```bash
# Log in to Docker Hub (prompts for credentials)
docker login
# Log in with a username (prompts for password)
docker login -u myuser
# Log in non-interactively (safer than -p)
echo "$TOKEN" | docker login -u myuser --password-stdin
# Log in to GitHub Container Registry
docker login ghcr.io
# Log out of Docker Hub
docker logout
# Log out of a specific registry
docker logout ghcr.io
# See where credentials are stored
cat ~/.docker/config.json
```

## 🌍 Global Options

```bash
# Debug output for any command
docker --debug ps
# Set the log level
docker -l debug ps
# Talk to a specific socket
docker -H unix:///var/run/docker.sock ps
# Use a different client config directory
docker --config ~/.docker-alt ps
# Use a specific context
docker --context default ps
# Talk to a remote daemon over SSH
docker -H ssh://user@192.168.1.10 ps
```

---

⬅️ [Index](README.md) · ➡️ Next: [02 — Images](02-images.md)
