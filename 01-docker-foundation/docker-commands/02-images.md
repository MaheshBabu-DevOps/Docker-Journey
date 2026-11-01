# 🖼️ 02 — Image Commands

> Examples use `nginx` (image) and `myuser` (Docker Hub username).

## ⬇️ Pull

```bash
# Pull the latest tag
docker pull nginx
# Pull a specific tag
docker pull nginx:1.27
# Pull a lightweight tag
docker pull nginx:alpine
# Pull an exact version by digest
docker pull nginx@sha256:<digest>
# Pull for a specific platform
docker pull --platform linux/arm64 nginx
# Pull every tag of an image
docker pull --all-tags nginx
# Pull without progress output
docker pull --quiet nginx
# Pull from another registry
docker pull ghcr.io/owner/image:tag
# Same as docker pull (long form)
docker image pull nginx
```

## 📋 List

```bash
# List local images
docker images
# Same as docker images (long form)
docker image ls
# Include intermediate images
docker images -a
# Only image IDs
docker images -q
# Show digests
docker images --digests
# Do not truncate IDs
docker images --no-trunc
# Images of one repository
docker images nginx
# Images with a specific tag
docker images nginx:alpine
# Dangling images (untagged leftovers)
docker images --filter "dangling=true"
# Filter by name pattern
docker images --filter "reference=nginx*"
# Images created before another image
docker images --filter "before=nginx:alpine"
# Images created after another image
docker images --filter "since=nginx:alpine"
# Custom table output
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"
# Output as JSON
docker images --format json
```

## 🔍 Inspect and History

```bash
# Full image details as JSON
docker image inspect nginx
# Only the image size in bytes
docker image inspect --format '{{.Size}}' nginx
# Ports the image exposes
docker image inspect --format '{{.Config.ExposedPorts}}' nginx
# Default command (CMD) of the image
docker image inspect --format '{{.Config.Cmd}}' nginx
# Entrypoint of the image
docker image inspect --format '{{.Config.Entrypoint}}' nginx
# Environment variables baked into the image
docker image inspect --format '{{.Config.Env}}' nginx
# Operating system and architecture
docker image inspect --format '{{.Os}}/{{.Architecture}}' nginx
# Show the layers that make up the image
docker history nginx
# Show full, untruncated commands
docker history --no-trunc nginx
# Only layer IDs
docker history -q nginx
# Sizes in bytes instead of human-readable
docker history --human=false nginx
# Same as docker history (long form)
docker image history nginx
```

## 🏷️ Tag

```bash
# Add a new name to an image
docker tag nginx myuser/nginx:v1
# Tag as latest
docker tag myuser/nginx:v1 myuser/nginx:latest
# Tag for a private registry
docker tag nginx registry.example.com/team/nginx:1.0
# Same as docker tag (long form)
docker image tag nginx myuser/nginx:v2
```

## ❌ Remove

```bash
# Remove an image
docker rmi nginx
# Remove by image ID
docker rmi 3b25b682ea82
# Force remove
docker rmi -f nginx
# Remove several images at once
docker rmi nginx redis alpine
# Remove all local images
docker rmi $(docker images -q)
# Remove all dangling images
docker rmi $(docker images -f "dangling=true" -q)
# Same as docker rmi (long form)
docker image rm nginx
# Force remove (long form)
docker image rm -f nginx
# Remove all dangling images
docker image prune
# Remove all images not used by any container
docker image prune -a
# Prune without confirmation
docker image prune -f
# Remove unused images older than 24 hours
docker image prune -a --filter "until=24h"
```

## 💾 Save, Load, Import, Export

```bash
# Save an image to a tar file
docker save -o nginx.tar nginx
# Save and compress
docker save nginx | gzip > nginx.tar.gz
# Save several images into one tar
docker save -o images.tar nginx:latest alpine:latest
# Load an image from a tar file
docker load -i nginx.tar
# Load from a compressed tar through stdin
docker load < nginx.tar.gz
# Load quietly
docker load -q -i nginx.tar
# Export a container filesystem (container "web") to a tar
docker export web > web-fs.tar
# Export using -o
docker export -o web-fs.tar web
# Create an image from a filesystem tar
docker import web-fs.tar myimage:imported
# Import and set a CMD
docker import -c 'CMD ["/bin/sh"]' web-fs.tar myimage:imported
# Same as docker save (long form)
docker image save -o nginx.tar nginx
# Same as docker load (long form)
docker image load -i nginx.tar
# Same as docker import (long form)
docker image import web-fs.tar myimage:imported
```

## 🧩 Create an Image from a Container

```bash
# Snapshot a container as a new image
docker commit web myuser/web:snapshot
# With a message and author
docker commit -m "added config" -a "Mahesh" web myuser/web:snapshot
# Change the CMD while committing
docker commit -c 'CMD ["nginx","-g","daemon off;"]' web myuser/web:snapshot
```

## 🔎 Search

```bash
# Search Docker Hub
docker search nginx
# Limit the number of results
docker search --limit 5 nginx
# Only official images
docker search --filter is-official=true nginx
# Only images with at least 100 stars
docker search --filter stars=100 nginx
# Do not truncate descriptions
docker search --no-trunc nginx
# Custom output format
docker search --format "table {{.Name}}\t{{.StarCount}}" nginx
```

## ⬆️ Push

```bash
# Push an image to Docker Hub (log in first)
docker push myuser/nginx:v1
# Push all tags of a repository
docker push --all-tags myuser/nginx
# Same as docker push (long form)
docker image push myuser/nginx:v1
```

## 📜 Multi-Platform Manifests

```bash
# Show the platforms an image supports
docker manifest inspect nginx
# Inspect the manifest without pulling
docker buildx imagetools inspect nginx
```

---

⬅️ Previous: [01 — Getting Started](01-getting-started.md) · [Index](README.md) · ➡️ Next: [03 — Containers](03-containers.md)
