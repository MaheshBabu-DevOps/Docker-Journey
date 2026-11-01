# 🟣 Install Docker Engine on CentOS Stream

Official page: https://docs.docker.com/engine/install/centos/

**Supported:** CentOS Stream 10 and CentOS Stream 9 (a maintained version).

```bash
# Check your CentOS version
cat /etc/centos-release
```

**Prerequisite:** the `centos-extras` repository must be enabled. It is enabled by default; turn it back on if you disabled it.

> The install does **not** start Docker, and it creates a `docker` group with no users. Use `sudo` until you do the [post-install steps](post-install.md).

---

## 1️⃣ Remove Old Packages

```bash
sudo dnf remove docker \
                docker-client \
                docker-client-latest \
                docker-common \
                docker-latest \
                docker-latest-logrotate \
                docker-logrotate \
                docker-engine
```

dnf may say none are installed. Data in `/var/lib/docker/` is not removed.

---

## 2️⃣ Set Up the Repository

```bash
sudo dnf -y install dnf-plugins-core
sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

---

## 3️⃣ Install Docker Engine

```bash
# Latest version
sudo dnf install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

If dnf asks you to accept a GPG key, confirm the fingerprint is `060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35` before accepting.

A **specific version** instead:

```bash
# List available versions
dnf list docker-ce --showduplicates | sort -r
# Install one, replacing <VERSION_STRING> with the version from the second column
sudo dnf install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io docker-buildx-plugin docker-compose-plugin
```

---

## 4️⃣ Start and Enable the Service

```bash
# Start now and on every boot
sudo systemctl enable --now docker
```

## 5️⃣ Verify

```bash
sudo docker run hello-world
```

---

## 🧹 Uninstall

```bash
sudo dnf remove docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras
# Delete ALL images, containers, and volumes (cannot be undone)
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

---

⬅️ [Install index](README.md) · ➡️ Next: [Post-installation steps](post-install.md)
