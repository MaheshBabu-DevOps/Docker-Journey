# 💾 05 — Volume Commands

> Examples use `myvol` (volume) and `web` (container).

## ➕ Create

```bash
# Create a named volume
docker volume create myvol
# Specify the driver explicitly
docker volume create --driver local myvol
# Add a label
docker volume create --label env=dev myvol
# NFS-backed volume
docker volume create --driver local --opt type=nfs --opt o=addr=192.168.1.10,rw --opt device=:/exports/data nfsvol
# tmpfs-backed volume
docker volume create --driver local --opt type=tmpfs --opt device=tmpfs --opt o=size=100m,uid=1000 tmpvol
```

## 📋 List and Inspect

```bash
# List volumes
docker volume ls
# Only volume names
docker volume ls -q
# Volumes not used by any container
docker volume ls --filter dangling=true
# Volumes by name
docker volume ls --filter name=my
# Volumes by label
docker volume ls --filter label=env=dev
# Volumes by driver
docker volume ls --filter driver=local
# Custom format
docker volume ls --format "table {{.Name}}\t{{.Driver}}"
# Full volume details
docker volume inspect myvol
# Where the volume lives on the host
docker volume inspect --format '{{.Mountpoint}}' myvol
# Show the volume directory on the host
sudo ls /var/lib/docker/volumes/myvol/_data
# Disk usage including volumes
docker system df -v
```

## 🔗 Use Volumes with Containers

```bash
# Mount a named volume
docker run -d --name web -v myvol:/data alpine sleep 3600
# Docker creates the volume if it does not exist
docker run -d -v newvol:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=pass mysql
# Mount as read-only
docker run -d -v myvol:/data:ro alpine sleep 3600
# Same with --mount syntax
docker run -d --mount type=volume,source=myvol,target=/data alpine sleep 3600
# --mount, read-only
docker run -d --mount type=volume,source=myvol,target=/data,readonly alpine sleep 3600
# Bind mount a host directory
docker run -d -v /home/me/site:/usr/share/nginx/html nginx
# Bind mount the current directory
docker run -d -v "$(pwd)":/app alpine sleep 3600
# Bind mount with --mount (fails if the host path is missing)
docker run -d --mount type=bind,source="$(pwd)",target=/app alpine sleep 3600
# Temporary in-memory filesystem
docker run -d --mount type=tmpfs,target=/tmp,tmpfs-size=64m alpine sleep 3600
# tmpfs with the short flag
docker run -d --tmpfs /tmp alpine sleep 3600
# Reuse volumes from another container
docker run -d --volumes-from web alpine sleep 3600
# Anonymous volume (random name)
docker run -d -v /data alpine sleep 3600
```

## 🧪 Prove Data Persists

```bash
# Write a file into the volume
docker run --rm -v myvol:/data alpine sh -c "echo hello > /data/test.txt"
# Read it back from a brand-new container
docker run --rm -v myvol:/data alpine cat /data/test.txt
# List the volume contents
docker run --rm -v myvol:/data alpine ls -la /data
```

## 📦 Backup and Restore

```bash
# Back up a volume to a tar.gz in the current directory
docker run --rm -v myvol:/data -v "$(pwd)":/backup alpine tar czf /backup/myvol.tar.gz -C /data .
# Restore a backup into a volume
docker run --rm -v myvol:/data -v "$(pwd)":/backup alpine tar xzf /backup/myvol.tar.gz -C /data
# Copy one volume into another
docker run --rm -v oldvol:/from -v newvol:/to alpine cp -a /from/. /to/
```

## 🧹 Remove

```bash
# Remove a volume (must not be in use)
docker volume rm myvol
# Remove several volumes
docker volume rm vol1 vol2
# Force remove
docker volume rm -f myvol
# Remove all unused anonymous volumes
docker volume prune
# Remove all unused volumes, named ones too
docker volume prune --all
# Prune without confirmation
docker volume prune -f
# Prune only volumes with a label
docker volume prune --filter "label=env=dev"
# Remove a container together with its anonymous volumes
docker rm -v web
```

---

⬅️ Previous: [04 — Networks](04-networks.md) · [Index](README.md) · ➡️ Next: [06 — Dockerfile and Build](06-dockerfile-build.md)
