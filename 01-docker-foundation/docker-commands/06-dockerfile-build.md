# 📝 06 — Dockerfile, Build and Buildx Commands

> Examples use `myapp` (image) and `myuser` (Docker Hub username). Run build commands in the folder that contains your `Dockerfile`.

## 🔨 Build

```bash
# Build an image from the Dockerfile in the current folder
docker build -t myapp .
# Build with a tag
docker build -t myapp:1.0 .
# Several tags at once
docker build -t myapp:1.0 -t myapp:latest .
# Use a different Dockerfile
docker build -f Dockerfile.dev -t myapp:dev .
# Build from another folder
docker build -t myapp ./backend
# Build without using the cache
docker build --no-cache -t myapp .
# Always pull the newest base image
docker build --pull -t myapp .
# Pass a build argument
docker build --build-arg VERSION=1.2 -t myapp .
# Pass several build arguments
docker build --build-arg A=1 --build-arg B=2 -t myapp .
# Build only up to a named stage (multi-stage)
docker build --target builder -t myapp:builder .
# Build for a specific platform
docker build --platform linux/amd64 -t myapp .
# Show full, plain build output
docker build --progress=plain -t myapp .
# Add a label to the image
docker build --label version=1.0 -t myapp .
# Use the host network during build
docker build --network host -t myapp .
# Use another image as cache source
docker build --cache-from myuser/myapp:latest -t myapp .
# Only print the image ID
docker build -q -t myapp .
# Write the image ID to a file
docker build --iidfile image.id -t myapp .
# Pass a build secret (used with RUN --mount=type=secret)
docker build --secret id=npmrc,src=$HOME/.npmrc -t myapp .
# Forward your SSH agent into the build
docker build --ssh default -t myapp .
# Check the Dockerfile for problems without building
docker build --check .
# Limit build memory
docker build --memory 1g -t myapp .
# Set a shared-memory size for the build
docker build --shm-size 256m -t myapp .
# Same as docker build (long form)
docker image build -t myapp .
# Same as docker build (builder form)
docker builder build -t myapp .
```

## 🌍 Build from Other Sources

```bash
# Build from a Git repository
docker build -t myapp https://github.com/user/repo.git
# Build from a branch and subfolder of a repo
docker build -t myapp https://github.com/user/repo.git#main:docker
# Build from a Dockerfile on stdin (no context)
docker build -t myapp - < Dockerfile
# Build from a tar archive
docker build -t myapp - < context.tar.gz
# Force BuildKit on older setups
DOCKER_BUILDKIT=1 docker build -t myapp .
```

## 🔬 After the Build

```bash
# Confirm the image exists
docker images myapp
# See each layer and its size
docker history myapp
# Run the image you built
docker run -d --name myapp -p 8080:80 myapp
# Check build size
docker image inspect --format '{{.Size}}' myapp
# Compare image sizes
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"
```

## 🚢 Tag and Push After Building

```bash
# Tag the image for Docker Hub
docker tag myapp myuser/myapp:1.0
# Log in
docker login
# Push it
docker push myuser/myapp:1.0
```

## 🧹 Build Cache

```bash
# Show builder cache disk usage
docker buildx du
# Remove dangling build cache
docker builder prune
# Remove all build cache
docker builder prune -a
# Prune without confirmation
docker builder prune -f
# Prune cache older than 24 hours
docker builder prune --filter "until=24h"
# Keep up to 5 GB of cache
docker builder prune --keep-storage 5GB
```

## 🏗️ Buildx (Advanced Builder)

```bash
# List builders
docker buildx ls
# Create a new builder
docker buildx create --name mybuilder
# Create and switch to it
docker buildx create --name mybuilder --use
# Switch to a builder
docker buildx use mybuilder
# Inspect a builder and start it
docker buildx inspect --bootstrap
# Build with Buildx
docker buildx build -t myapp .
# Build and load the image into local Docker
docker buildx build --load -t myapp .
# Build and push directly to a registry
docker buildx build --push -t myuser/myapp:1.0 .
# Multi-platform build and push
docker buildx build --platform linux/amd64,linux/arm64 -t myuser/myapp:1.0 --push .
# Use a registry as a remote cache
docker buildx build --cache-from type=registry,ref=myuser/myapp:cache --cache-to type=registry,ref=myuser/myapp:cache,mode=max -t myapp .
# Export the result to a local folder
docker buildx build --output type=local,dest=./out .
# Build every target in docker-bake.hcl
docker buildx bake
# Bake with a specific file
docker buildx bake -f docker-bake.hcl
# Bake and push
docker buildx bake --push
# Inspect a multi-platform image in a registry
docker buildx imagetools inspect myuser/myapp:1.0
# Merge images into one multi-platform tag
docker buildx imagetools create -t myuser/myapp:latest myuser/myapp:amd64 myuser/myapp:arm64
# Stop a builder
docker buildx stop mybuilder
# Remove a builder
docker buildx rm mybuilder
# Remove build cache of the current builder
docker buildx prune
```

---

⬅️ Previous: [05 — Volumes](05-volumes.md) · [Index](README.md) · ➡️ Next: [07 — Compose](07-compose.md)
