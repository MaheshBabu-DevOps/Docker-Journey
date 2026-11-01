# 🔴 Install Docker Engine on Debian

Official page: https://docs.docker.com/engine/install/debian/

**Supported:** Debian 13 (Trixie) and Debian 12 (Bookworm). Architectures: x86_64 (amd64), arm64, armhf (arm/v7), ppc64le.

```bash
# Check your Debian version
cat /etc/os-release
```

> The `docker` group has no users by default, so you need `sudo` until you do the [post-install steps](post-install.md).

---

## 1️⃣ Remove Conflicting Packages

```bash
sudo apt remove $(dpkg --get-selections docker.io docker-compose docker-doc docker-buildx podman-docker containerd runc | cut -f1)
```

apt may say none are installed. Data in `/var/lib/docker/` is not removed.

---

## 2️⃣ Set Up the apt Repository

```bash
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/debian
Suites: $(. /etc/os-release && echo "$VERSION_CODENAME")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
```

---

## 3️⃣ Install Docker

```bash
# Latest version
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

A **specific version** instead:

```bash
# List available versions
apt list --all-versions docker-ce
# Pick one and install it (example string)
VERSION_STRING=5:29.8.2-1~debian.12~bookworm
sudo apt install docker-ce=$VERSION_STRING docker-ce-cli=$VERSION_STRING containerd.io docker-buildx-plugin docker-compose-plugin
```

---

## 4️⃣ Start and Verify

```bash
# Check the service (press q to exit)
sudo systemctl status docker
# Start it only if it is not running
sudo systemctl start docker
# Test container
sudo docker run hello-world
```

**Upgrade:** repeat step 3 with the new version.

---

## 🧹 Uninstall

```bash
sudo apt purge docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras
# Delete ALL images, containers, and volumes (cannot be undone)
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
# Remove the apt source and key
sudo rm /etc/apt/sources.list.d/docker.sources
sudo rm /etc/apt/keyrings/docker.asc
```

> The convenience script is not recommended for production. It cannot upgrade installs and may jump major versions. See [the index page](README.md#-quick-install-convenience-script-testing-only).

---

⬅️ [Install index](README.md) · ➡️ Next: [Post-installation steps](post-install.md)
