# ☁️ 08 — Registry Commands (Docker Hub, Private, Harbor, ECR)

> Examples use `myuser` (Docker Hub user), `myapp` (image), and placeholders such as `<account-id>` and `<region>` for AWS.

## 🐳 Docker Hub

```bash
# Log in
docker login
# Search for an image
docker search nginx
# Pull an image
docker pull nginx
# Tag your image for your Docker Hub account
docker tag myapp myuser/myapp:1.0
# Push it
docker push myuser/myapp:1.0
# Push all tags
docker push --all-tags myuser/myapp
# Pull your image on another machine
docker pull myuser/myapp:1.0
# Log out
docker logout
```

## 🔐 Other Registries

```bash
# GitHub Container Registry
echo "$GITHUB_TOKEN" | docker login ghcr.io -u myuser --password-stdin
# Tag for GitHub Container Registry
docker tag myapp ghcr.io/myuser/myapp:1.0
# Push to GitHub Container Registry
docker push ghcr.io/myuser/myapp:1.0
# Google Artifact Registry
gcloud auth configure-docker <region>-docker.pkg.dev
# Azure Container Registry
az acr login --name myregistry
# Log in to any custom registry
docker login registry.example.com
# Tag for a custom registry
docker tag myapp registry.example.com/team/myapp:1.0
# Push to a custom registry
docker push registry.example.com/team/myapp:1.0
# Log out of a custom registry
docker logout registry.example.com
```

## 🏠 Run Your Own Registry

```bash
# Start a local registry on port 5000
docker run -d -p 5000:5000 --restart always --name registry registry:2
# Start it with persistent storage
docker run -d -p 5000:5000 --name registry -v registry-data:/var/lib/registry registry:2
# Tag an image for the local registry
docker tag myapp localhost:5000/myapp:1.0
# Push to the local registry
docker push localhost:5000/myapp:1.0
# Pull from the local registry
docker pull localhost:5000/myapp:1.0
# List repositories in the registry
curl http://localhost:5000/v2/_catalog
# List tags of a repository
curl http://localhost:5000/v2/myapp/tags/list
# Allow an HTTP (insecure) registry: edit the daemon config
sudo nano /etc/docker/daemon.json
# Restart Docker to apply daemon.json changes
sudo systemctl restart docker
```

Example `/etc/docker/daemon.json` for an HTTP registry (use only on trusted networks):

```json
{ "insecure-registries": ["registry.example.com:5000"] }
```

## 🚢 Harbor

```bash
# Log in to Harbor
docker login harbor.example.com
# Tag into a Harbor project
docker tag myapp harbor.example.com/myproject/myapp:1.0
# Push to Harbor
docker push harbor.example.com/myproject/myapp:1.0
# Pull from Harbor
docker pull harbor.example.com/myproject/myapp:1.0
```

## 🟧 AWS ECR

```bash
# Create an ECR repository
aws ecr create-repository --repository-name myapp --region <region>
# Log Docker in to ECR
aws ecr get-login-password --region <region> | docker login --username AWS --password-stdin <account-id>.dkr.ecr.<region>.amazonaws.com
# Tag for ECR
docker tag myapp:1.0 <account-id>.dkr.ecr.<region>.amazonaws.com/myapp:1.0
# Push to ECR
docker push <account-id>.dkr.ecr.<region>.amazonaws.com/myapp:1.0
# Pull from ECR
docker pull <account-id>.dkr.ecr.<region>.amazonaws.com/myapp:1.0
# List images in an ECR repository
aws ecr list-images --repository-name myapp --region <region>
# Delete an image from ECR
aws ecr batch-delete-image --repository-name myapp --image-ids imageTag=1.0 --region <region>
# Delete an ECR repository and its images
aws ecr delete-repository --repository-name myapp --force --region <region>
```

## 📜 Manifests (Multi-Platform)

```bash
# Inspect an image's manifest
docker manifest inspect nginx
# Create a manifest list from per-platform images
docker manifest create myuser/myapp:1.0 myuser/myapp:amd64 myuser/myapp:arm64
# Mark the architecture of an entry
docker manifest annotate myuser/myapp:1.0 myuser/myapp:arm64 --arch arm64
# Push the manifest list
docker manifest push myuser/myapp:1.0
# Inspect a multi-platform image with Buildx
docker buildx imagetools inspect myuser/myapp:1.0
```

---

⬅️ Previous: [07 — Compose](07-compose.md) · [Index](README.md) · ➡️ Next: [09 — System, Cleanup and Monitoring](09-system-cleanup-monitoring.md)
