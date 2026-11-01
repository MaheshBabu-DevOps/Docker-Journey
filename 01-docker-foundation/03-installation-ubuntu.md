# 🛠️ 03 — Installing Docker on Ubuntu

This guide installs **Docker Engine** on Ubuntu using Docker's official apt repository. It follows the official Docker documentation, so check https://docs.docker.com/engine/install/ubuntu/ if anything has changed.

## ✅ Prerequisites

- A supported Ubuntu version (for example 22.04 LTS or 24.04 LTS), 64-bit
- A user account with `sudo` privileges
- Internet access

Check your Ubuntu version:

```bash
lsb_release -a
```

---

## 1️⃣ Remove Old or Conflicting Packages

Unofficial Docker packages from Ubuntu's own repository can conflict with the official ones. Remove them first (it is fine if apt says they are not installed):

```bash
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do
  sudo apt-get remove -y $pkg
done
```

> Images, containers, and volumes in `/var/lib/docker/` are not removed by this.

---

## 2️⃣ Set Up Docker's apt Repository

```bash
# Update the package index and install prerequisites
sudo apt-get update
sudo apt-get install -y ca-certificates curl

# Add Docker's official GPG key
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to apt sources
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Refresh the package index with the new repository
sudo apt-get update
```

What this does:

| Step | Purpose |
|------|---------|
| `ca-certificates`, `curl` | Needed to download files over HTTPS |
| GPG key | Lets apt verify packages really come from Docker |
| `docker.list` | Tells apt where to find Docker packages |

---

## 3️⃣ Install Docker Engine

```bash
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

| Package | What it is |
|---------|-----------|
| `docker-ce` | Docker Engine (the daemon) |
| `docker-ce-cli` | The `docker` command-line client |
| `containerd.io` | Container runtime |
| `docker-buildx-plugin` | Extended image builds (`docker buildx`) |
| `docker-compose-plugin` | Docker Compose v2 (`docker compose`) |

---

## 4️⃣ Verify the Installation

Check that the service is running:

```bash
sudo systemctl status docker
```

You should see `active (running)`. Press `q` to exit.

Run the test container:

```bash
sudo docker run hello-world
```

Expected output (shortened, example):

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

This confirms the client reached the daemon, the daemon pulled the image from Docker Hub, created a container, and streamed its output back.

---

## 5️⃣ Run Docker Without `sudo`

By default only `root` can talk to the Docker socket. Add your user to the `docker` group:

```bash
sudo usermod -aG docker $USER
```

Then **log out and log back in** (or run `newgrp docker` in the current shell) so the group change takes effect. Test:

```bash
docker run hello-world
```

> ⚠️ **Security note:** members of the `docker` group effectively have root-level access to the host. Only add users you trust.

---

## 6️⃣ Start Docker Automatically on Boot

On Ubuntu, Docker is enabled on boot by default after installation. To set it explicitly:

```bash
sudo systemctl enable docker
sudo systemctl enable containerd
```

Useful service commands:

```bash
sudo systemctl start docker     # start the daemon
sudo systemctl stop docker      # stop the daemon
sudo systemctl restart docker   # restart the daemon
```

---

## 7️⃣ Check Versions

```bash
docker --version
docker compose version
```

Example output (your versions will differ):

```
Docker version 27.x.x, build xxxxxxx
Docker Compose version v2.x.x
```

---

## 🧯 Common Errors and Fixes

| Error | Cause | Fix |
|-------|-------|-----|
| `permission denied while trying to connect to the Docker daemon socket` | User not in the `docker` group, or the group change is not applied yet | `sudo usermod -aG docker $USER`, then log out and in |
| `Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?` | The daemon is not running | `sudo systemctl start docker` |
| `E: Unable to locate package docker-ce` | The repository was not added correctly | Redo step 2 and run `sudo apt-get update` |
| `NO_PUBKEY` / GPG error | The GPG key is missing or unreadable | Redo the GPG key commands in step 2, including `chmod a+r` |
| `docker: command not found` | Docker is not installed, or the install failed | Redo step 3 and check for errors |

---

## 🧹 Uninstall (if you ever need to)

```bash
sudo apt-get purge -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

To also delete **all** images, containers, and volumes (this cannot be undone):

```bash
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

---

## ✅ Summary

1. Remove old packages.
2. Add Docker's apt repository and GPG key.
3. Install `docker-ce`, `docker-ce-cli`, `containerd.io`, and the plugins.
4. Verify with `hello-world`.
5. Add your user to the `docker` group to avoid `sudo`.

⬅️ Previous: [02 — Docker Architecture](02-docker-architecture.md)
➡️ Next: [04 — Docker CLI: First Commands](04-docker-cli-first-commands.md)
