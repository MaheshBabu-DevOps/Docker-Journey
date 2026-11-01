# 🔐 10 — Security Commands

> Security options (user, capabilities, seccomp) are set when you **run** a container. There is no `docker update --security-opt`.
> `docker scan` is deprecated and replaced by **Docker Scout**. **Trivy** is a popular free alternative.

## 🛡️ Docker Scout (Image Scanning)

```bash
# Quick vulnerability summary of an image
docker scout quickview nginx
# List all known CVEs
docker scout cves nginx
# Only critical and high CVEs
docker scout cves --only-severity critical,high nginx
# Scan a local image
docker scout cves local://myapp:1.0
# Suggest a better base image
docker scout recommendations nginx
# Compare two images
docker scout compare --to nginx:1.27 nginx:alpine
# Software bill of materials (SBOM)
docker scout sbom nginx
```

## 🔎 Trivy (Image, Filesystem and Config Scanning)

```bash
# Scan an image
trivy image nginx
# Show only HIGH and CRITICAL
trivy image --severity HIGH,CRITICAL nginx
# Fail the command if any CRITICAL is found (useful in CI)
trivy image --exit-code 1 --severity CRITICAL nginx
# Ignore CVEs that have no fix yet
trivy image --ignore-unfixed nginx
# JSON output
trivy image -f json -o result.json nginx
# Scan a saved image tar
trivy image --input nginx.tar
# Scan a project folder for vulnerable dependencies
trivy fs .
# Scan a Dockerfile and configs for misconfigurations
trivy config .
# Scan only for secrets
trivy image --scanners secret myapp:1.0
# Run Trivy itself as a container
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image nginx
```

## 👤 Run as Non-Root and Drop Privileges

```bash
# Run as a specific non-root user
docker run -u 1000:1000 alpine id
# Run as the "nobody" user
docker run -u nobody alpine id
# Check which user an image uses
docker image inspect --format '{{.Config.User}}' myapp
# Check which user a container runs as
docker exec web id
# Show the user name
docker exec web whoami
# Read-only root filesystem
docker run -d --read-only nginx
# Read-only root + writable temp dir
docker run -d --read-only --tmpfs /tmp --tmpfs /var/run nginx
# Drop all Linux capabilities
docker run -d --cap-drop ALL nginx
# Drop all, add back only what is needed
docker run -d --cap-drop ALL --cap-add NET_BIND_SERVICE nginx
# Block privilege escalation
docker run -d --security-opt no-new-privileges nginx
# Use a custom seccomp profile
docker run -d --security-opt seccomp=profile.json nginx
# Use a specific AppArmor profile
docker run -d --security-opt apparmor=docker-default nginx
# Limit processes (stops fork bombs)
docker run -d --pids-limit 100 nginx
# Limit memory
docker run -d --memory 256m nginx
# Run with a tiny init process
docker run -d --init nginx
```

## ⚠️ Risky Options (Avoid in Production)

```bash
# Full host access: avoid
docker run --privileged alpine sh
# Share the host process namespace: avoid
docker run --pid host alpine ps
# Mount the Docker socket (gives root-equivalent control): avoid
docker run -v /var/run/docker.sock:/var/run/docker.sock alpine sh
# Check whether any running container is privileged
docker ps -q | xargs docker inspect --format '{{.Name}} privileged={{.HostConfig.Privileged}}'
```

## 🔑 Secrets (Build Time)

```bash
# Build with a secret that never ends up in the image
docker build --secret id=mysecret,src=./secret.txt -t myapp .
# Build with an environment variable secret
docker build --secret id=token,env=API_TOKEN -t myapp .
# Check that no secret leaked into the image history
docker history --no-trunc myapp
# Look for env variables baked into an image
docker image inspect --format '{{.Config.Env}}' myapp
```

In the Dockerfile, a build secret is used like this:

```dockerfile
RUN --mount=type=secret,id=mysecret cat /run/secrets/mysecret
```

## ✍️ Docker Content Trust (Signed Images)

```bash
# Only pull/run signed images in this shell
export DOCKER_CONTENT_TRUST=1
# Turn it off again
export DOCKER_CONTENT_TRUST=0
# Generate a signing key
docker trust key generate mykey
# Add a signer to a repository
docker trust signer add --key mykey.pub myuser myuser/myapp
# Sign and push an image
docker trust sign myuser/myapp:1.0
# Show signature details
docker trust inspect --pretty myuser/myapp:1.0
# Revoke a signature
docker trust revoke myuser/myapp:1.0
# Push without signing (override)
docker push --disable-content-trust myuser/myapp:1.0
```

## 🧪 Audit the Docker Host

```bash
# Get the CIS Docker Benchmark checker
git clone https://github.com/docker/docker-bench-security.git
# Run the audit
cd docker-bench-security && sudo sh docker-bench-security.sh
# Install rootless Docker (daemon runs without root)
dockerd-rootless-setuptool.sh install
# Check who can talk to the Docker socket
ls -l /var/run/docker.sock
# See who is in the docker group
getent group docker
```

---

⬅️ Previous: [09 — System, Cleanup and Monitoring](09-system-cleanup-monitoring.md) · [Index](README.md) · ➡️ Next: [11 — Swarm](11-swarm.md)
