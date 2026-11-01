# 🟠 Install Docker Engine on Ubuntu

Official page: https://docs.docker.com/engine/install/ubuntu/

**Supported:** Ubuntu 26.10, 26.04 LTS, 24.04 LTS, 22.04 LTS (64-bit). Architectures: x86_64 (amd64), arm64, armhf, ppc64le, s390x.
Derivatives such as Linux Mint are not officially supported, but these steps often work.

```bash
# Check your Ubuntu version
lsb_release -a
```

---

## 1️⃣ Remove Conflicting Packages

Unofficial packages can clash with Docker's own. Remove them (apt may say none are installed, which is fine):

```bash
sudo apt remove $(dpkg --get-selections docker.io docker-compose docker-compose-v2 docker-doc docker-buildx podman-docker containerd runc | cut -f1)
```

> Images, containers, and volumes in `/var/lib/docker/` are **not** removed by this.

---

## 2️⃣ Method 1: apt Repository (Recommended)

### Set up the repository

```bash
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
```

| Piece | Purpose |
|-------|---------|
| `ca-certificates`, `curl` | Download files over HTTPS |
| GPG key (`docker.asc`) | Lets apt verify packages really come from Docker |
| `docker.sources` | Tells apt where Docker's packages live |

### Install Docker

```bash
# Latest version
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

To install a **specific version** instead:

```bash
# List available versions
apt list --all-versions docker-ce
# Pick one from the second column and install it (example string)
VERSION_STRING=5:29.8.2-1~ubuntu.24.04~noble
sudo apt install docker-ce=$VERSION_STRING docker-ce-cli=$VERSION_STRING containerd.io docker-buildx-plugin docker-compose-plugin
```

### 🔍 Check That the Docker Service Is Running

```bash
# Show the service status (press q to exit)
sudo systemctl status docker
# Short check: prints "active" if it is running
systemctl is-active docker
```

Expected output of `systemctl status docker` (shortened, example):

```text
● docker.service - Docker Application Container Engine
     Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; preset: enabled)
     Active: active (running) since Sun 2026-11-01 23:50:12 IST; 5min ago
   Main PID: 1234 (dockerd)
```

| Line | Meaning |
|------|---------|
| `Active: active (running)` | The daemon is running. This is what you want. |
| `Active: inactive (dead)` | Docker is stopped. Start it (next step). |
| `Loaded: ... enabled` | Docker will start automatically at boot |

**If Docker is stopped, start it:**

```bash
sudo systemctl start docker
```

**Enable Docker at system boot:**

```bash
sudo systemctl enable docker
# Check that it is enabled: prints "enabled"
systemctl is-enabled docker
```

**Verify Docker is working:**

```bash
docker info
```

`docker info` prints details from the daemon, so it also proves the client can reach it. Look for these lines:

```text
Server:
 Containers: 0
 Images: 0
 Server Version: <version>
 Storage Driver: overlay2
```

If you see `permission denied`, add `sudo` (`sudo docker info`) until you do the [post-install steps](post-install.md). If you see `Cannot connect to the Docker daemon`, the service is not running. Go back to the start command above.

**Other service commands you will use:**

```bash
# Stop the daemon
sudo systemctl stop docker
# Restart the daemon (after changing /etc/docker/daemon.json)
sudo systemctl restart docker
# Do not start at boot
sudo systemctl disable docker
# Show the daemon logs if it will not start
sudo journalctl -u docker.service -n 50
```

### ✅ Verify the Installation

Verify that the installation is successful by running the `hello-world` image:

```bash
sudo docker run hello-world
```

This command downloads a tiny test image, runs it in a container, prints a message, and exits.

**Expected output** (the digest and layer ID on your machine will differ):

```text
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
c1ec31eb5944: Pull complete
Digest: sha256:<digest>
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.
```

**What each part means:**

| Output | Meaning |
|--------|---------|
| `Unable to find image 'hello-world:latest' locally` | The image is not on your machine yet (normal on the first run) |
| `Pulling from library/hello-world` / `Pull complete` | The daemon is downloading the image from Docker Hub |
| `Status: Downloaded newer image` | The download finished |
| `Hello from Docker!` | The container ran and printed its message. **This is your success sign.** |
| Steps 1 to 4 | The same client → daemon → registry → container flow you learned in [topic 02](../02-docker-architecture.md) |

On the second run, the "Unable to find image" and "Pulling" lines are gone, because the image is already stored locally.

**Extra checks (optional):**

```bash
# Client and server versions: you must see BOTH a Client and a Server section
sudo docker version
# The hello-world container ran and exited, so it only shows with -a
sudo docker ps -a
# The image is now stored locally
sudo docker images
# Clean up the test container and image
sudo docker rm $(sudo docker ps -aq --filter ancestor=hello-world)
sudo docker rmi hello-world
```

**If it does not work:**

| What you see | What it means | Fix |
|--------------|---------------|-----|
| `permission denied while trying to connect to the Docker daemon socket` | You ran without `sudo` and are not in the `docker` group yet | Use `sudo`, or do the [post-install steps](post-install.md) |
| `Cannot connect to the Docker daemon ... Is the docker daemon running?` | The daemon is stopped | `sudo systemctl start docker` |
| Hangs on `Pulling from library/hello-world` or says `no such host` | No internet or DNS problem | Check your connection, then try again |
| `docker: command not found` | Docker did not install | Redo the apt repository and install steps above, and read the output for errors |

**Upgrade:** repeat the install step with the new version.

---

## 3️⃣ Method 2: Install from a `.deb` Package (Offline / Air-Gapped)

1. Open https://download.docker.com/linux/ubuntu/dists/ and choose your Ubuntu version.
2. Go to `pool/stable/`, choose your architecture, and download:
   `containerd.io`, `docker-ce`, `docker-ce-cli`, `docker-buildx-plugin`, `docker-compose-plugin` (each is `<package>_<version>_<arch>.deb`).
3. Install them:

```bash
sudo dpkg -i ./containerd.io_<version>_<arch>.deb \
  ./docker-ce_<version>_<arch>.deb \
  ./docker-ce-cli_<version>_<arch>.deb \
  ./docker-buildx-plugin_<version>_<arch>.deb \
  ./docker-compose-plugin_<version>_<arch>.deb
```

4. Verify with `sudo systemctl status docker` and `sudo docker run hello-world`.

You must download newer files for every upgrade.

---

## 4️⃣ Method 3: Convenience Script (Testing Only)

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
# Preview first
sudo sh ./get-docker.sh --dry-run
# Install
sudo sh get-docker.sh
```

Not for production. See the [index page](README.md#-quick-install-convenience-script-testing-only) for the warnings.

---

## 🧹 Uninstall

```bash
# Remove the packages
sudo apt purge docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras
# Delete ALL images, containers, and volumes (cannot be undone)
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
# Remove the apt source and key
sudo rm /etc/apt/sources.list.d/docker.sources
sudo rm /etc/apt/keyrings/docker.asc
```

Config files you edited yourself must be removed by hand.

---

⬅️ [Install index](README.md) · ➡️ Next: [Post-installation steps](post-install.md)
