# 🍓 Install Docker Engine on Raspberry Pi OS (32-bit)

Official page: https://docs.docker.com/engine/install/raspberry-pi-os/

**Supported:** 32-bit (armhf) Raspberry Pi OS Bookworm 12 and Bullseye 11.

> ⚠️ **64-bit Raspberry Pi OS?** Follow the [Debian guide](debian.md) instead.
>
> ⚠️ **32-bit is ending.** Docker Engine v28 is the last major version with 32-bit (armhf) packages for Raspberry Pi OS. From v29, new major versions no longer provide them.
>
> ⚠️ **ARMv6 devices** (Raspberry Pi 1, Pi Zero, Pi Zero W) are not supported by the official packages.

```bash
# Check that you are on 32-bit ARM (expect armv7l) and which release
uname -m
cat /etc/os-release
```

---

## 1️⃣ Remove Conflicting Packages

```bash
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

---

## 2️⃣ Set Up the apt Repository

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/raspbian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/raspbian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

Note the repository path is `linux/raspbian`, not `linux/debian`.

---

## 3️⃣ Install Docker

```bash
# Latest version
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

A **specific version** instead:

```bash
# List available versions
apt-cache madison docker-ce | awk '{ print $3 }'
# Pick one and install it (example string)
VERSION_STRING=5:29.8.2-1~raspbian.12~bookworm
sudo apt-get install docker-ce=$VERSION_STRING docker-ce-cli=$VERSION_STRING containerd.io docker-buildx-plugin docker-compose-plugin
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

The `docker` group has no users by default, so use `sudo` until you do the [post-install steps](post-install.md).

> The convenience script is for testing only. Upgrade through apt rather than re-running it.

---

## 🧹 Uninstall

```bash
sudo apt-get purge docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras
# Delete ALL images, containers, and volumes (cannot be undone)
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

---

⬅️ [Install index](README.md) · ➡️ Next: [Post-installation steps](post-install.md)
