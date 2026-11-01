# 🔵 Install Docker Engine on Fedora

Official page: https://docs.docker.com/engine/install/fedora/

**Supported:** Fedora 45, 44, and 43 (a maintained version).

```bash
# Check your Fedora version
cat /etc/fedora-release
```

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
                docker-selinux \
                docker-engine-selinux \
                docker-engine
```

---

## 2️⃣ Set Up the Repository

Fedora uses a different `dnf config-manager` form from RHEL and CentOS:

```bash
sudo dnf config-manager addrepo --from-repofile https://download.docker.com/linux/fedora/docker-ce.repo
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

### 🔧 If the service fails with `failed to find iptables`

Only needed when `journalctl -u docker` shows that error:

```bash
sudo alternatives --set iptables /usr/bin/iptables-nft
sudo systemctl restart docker
```

## 5️⃣ Verify

```bash
sudo docker run hello-world
```

**Upgrade:** repeat the repository and install steps and choose the new version.

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
