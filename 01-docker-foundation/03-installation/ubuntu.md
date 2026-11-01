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

### Check that the service is running

```bash
# Status (press q to exit)
sudo systemctl status docker
# Start it only if it is not running
sudo systemctl start docker
```

### Verify

```bash
sudo docker run hello-world
```

You should see "Hello from Docker!". That means the client reached the daemon, the daemon pulled the image from Docker Hub, created a container, and streamed the output back.

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
