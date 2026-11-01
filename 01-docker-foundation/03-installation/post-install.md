# ⚙️ Post-Installation Steps for Linux

Official page: https://docs.docker.com/engine/install/linux-postinstall/

These steps apply to **every** Linux install guide in this folder.

## 1️⃣ Run Docker Without `sudo`

By default only `root` can use the Docker socket. Add your user to the `docker` group:

```bash
# Create the group (some distros already did)
sudo groupadd docker
# Add your user to it
sudo usermod -aG docker $USER
# Apply the new group in the current shell
newgrp docker
```

Log out and back in (or restart the VM) to make it permanent. Then test:

```bash
docker run hello-world
```

> 🔐 **Security:** the `docker` group gives **root-level** power. Anyone in it can control the host. Only add users you trust.

### Alternative: get group access only when you need it

Keep your user out of the group and use a password-protected shell:

```bash
sudo groupadd --force docker
# Make sure your user is not in the group
sudo gpasswd --delete "$USER" docker
# Set a group password
sudo gpasswd docker
# Start a shell that has the docker group (asks for the password)
newgrp docker
# Optional: allow only configured members instead of the shared password
sudo gpasswd --restrict docker
```

The docs say this is **not** a security boundary against malicious code running as your user. Use it only on single-user workstations. For stronger isolation use rootless mode or a virtual machine.

---

## 2️⃣ Fix `permission denied` for `~/.docker`

If you ran Docker with `sudo` earlier, you may see:

```
WARNING: Error loading config file: /home/user/.docker/config.json -
stat /home/user/.docker/config.json: permission denied
```

Fix the ownership:

```bash
sudo chown "$USER":"$USER" /home/"$USER"/.docker -R
sudo chmod g+rwx "$HOME/.docker" -R
```

Or delete `~/.docker`. It is recreated automatically, but custom settings are lost.

---

## 3️⃣ Start Docker on Boot (systemd)

Debian and Ubuntu enable Docker on boot by default. On RPM-based systems you enabled it during install. To set it explicitly:

```bash
# Start on boot
sudo systemctl enable docker.service
sudo systemctl enable containerd.service
# Do not start on boot
sudo systemctl disable docker.service
sudo systemctl disable containerd.service
```

Everyday service commands:

```bash
sudo systemctl status docker
sudo systemctl start docker
sudo systemctl stop docker
sudo systemctl restart docker
```

---

## 4️⃣ Check Your Versions

```bash
docker --version
docker compose version
docker buildx version
```

---

## 5️⃣ Limit Log Size (Recommended)

Docker's default `json-file` log driver writes container logs to disk, and the files can grow until the disk is full. Pick one:

- turn on log rotation for `json-file`,
- switch to the `local` log driver (rotates by default), or
- send logs to a remote log system.

Example `/etc/docker/daemon.json` with rotation:

```json
{
  "log-driver": "json-file",
  "log-opts": { "max-size": "10m", "max-file": "3" }
}
```

Then restart the daemon:

```bash
sudo systemctl restart docker
```

---

## ✅ Summary

1. `sudo usermod -aG docker $USER`, then log out and in.
2. Test with `docker run hello-world` (no `sudo`).
3. Make sure Docker starts on boot.
4. Limit log size so your disk doesn't fill up.

⬅️ [Install index](README.md) · ➡️ Next: [04 — Docker CLI: First Commands](../04-docker-cli-first-commands.md)
