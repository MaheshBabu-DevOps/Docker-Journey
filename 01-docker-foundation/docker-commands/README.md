# 📚 Docker Commands — A to Z Reference

Every command is in a `bash` block with a one-line comment above it, so you can copy and run it. The files follow the learning order: setup first, then images, containers, networking, storage, builds, Compose, registries, cleanup, security, Swarm, and extras.

## 🗂️ Index

| # | File | What's inside |
|---|------|---------------|
| 01 | [Getting Started](01-getting-started.md) | systemd service, run without sudo, version, info, help, login, global flags |
| 02 | [Images](02-images.md) | pull, list, inspect, history, tag, remove, prune, save, load, import, commit, search, push |
| 03 | [Containers](03-containers.md) | `docker run` options, lifecycle, list, inspect, logs, stats, events, exec, attach, cp, remove |
| 04 | [Networks](04-networks.md) | create, connect, inspect, test connectivity, port publishing, host and none networks |
| 05 | [Volumes](05-volumes.md) | create, mounts, bind mounts, tmpfs, backup and restore, prune |
| 06 | [Dockerfile and Build](06-dockerfile-build.md) | `docker build` options, build cache, Buildx, multi-platform builds, bake |
| 07 | [Compose](07-compose.md) | up, down, build, logs, exec, run, config, override files, profiles |
| 08 | [Registry](08-registry.md) | Docker Hub, GHCR, private registry, Harbor, AWS ECR, manifests |
| 09 | [System, Cleanup and Monitoring](09-system-cleanup-monitoring.md) | disk usage, prune, stats, events, troubleshooting, daemon config |
| 10 | [Security](10-security.md) | Docker Scout, Trivy, non-root, capabilities, seccomp, secrets, content trust |
| 11 | [Swarm](11-swarm.md) | swarm, node, service, stack, secret, config, overlay networks |
| 12 | [Context, Plugins and Misc](12-context-plugins-misc.md) | contexts, remote Docker, plugins, checkpoints, shell patterns |

## 📖 How to Read These Files

- **Examples use fixed names** such as `nginx` (image), `web` (container), `mynet` (network), `myvol` (volume), and `myuser` (Docker Hub user). Swap in your own.
- **`<angle brackets>`** mark values you must fill in (for example `<token>` or `<account-id>`).
- **Two forms exist for many commands.** `docker images` and `docker image ls` do the same thing. Both are listed where it helps.
- **Careful sections** are marked in the headings (⚠️ and 💣). They delete data or weaken security.

## ✏️ Notes on Commands That Changed

Some commands you may see in older guides behave differently now:

| Old / common form | Use this instead |
|-------------------|------------------|
| `docker-compose ...` (v1) | `docker compose ...` (v2, no hyphen) |
| `docker scan` | `docker scout` or `trivy` |
| `docker update --security-opt` | Not supported. Set `--security-opt` with `docker run` |
| `docker info --security` | `docker info --format '{{.SecurityOptions}}'` |
| `docker build --squash` | Experimental. Use multi-stage builds instead (Phase 8) |
| Docker Trusted Registry (DTR) commands | DTR is not part of Docker Engine. Use Docker Hub, Harbor, or ECR (Phase 11) |

## 🔗 Where Each Topic Is Taught

These files are a quick reference. Start with the lessons in the parent folder ([01-what-is-docker.md](../01-what-is-docker.md) to [05-linux-internals-for-docker.md](../05-linux-internals-for-docker.md)). Later phases go deeper on each area.
