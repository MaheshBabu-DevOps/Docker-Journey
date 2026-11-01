# 🛠️ 03 — Installing Docker Engine on Linux (All Distributions)

This guide follows the official Docker documentation: https://docs.docker.com/engine/install/

Docker's supported versions change over time. Before you install, check the official page for your platform to see the current list.

## 🧭 Docker Engine vs Docker Desktop

| | Docker Engine | Docker Desktop |
|---|---|---|
| What it is | The daemon + CLI, installed from packages | An app that bundles Docker Engine, a GUI, and extras |
| Where | Linux servers and desktops | Windows, macOS, Linux desktops |
| Install guide | **This folder** | https://docs.docker.com/desktop/ |

This folder covers **Docker Engine on Linux**. On Windows or macOS, install Docker Desktop.

---

## 1️⃣ Step 0: Find Out Which Linux You Have

```bash
# Distribution name and version
cat /etc/os-release
# Same information in a short form (Debian/Ubuntu family)
lsb_release -a
# Your CPU architecture (x86_64 = amd64, aarch64 = arm64)
uname -m
# Your Linux kernel version
uname -r
```

Look at the `ID=` and `VERSION_ID=` lines, then pick your guide:

## 2️⃣ Pick Your Guide

| Your system | Guide | Package manager | Supported versions (per Docker docs) |
|-------------|-------|-----------------|--------------------------------------|
| Ubuntu | [ubuntu.md](ubuntu.md) | `apt` | 26.10, 26.04 LTS, 24.04 LTS, 22.04 LTS |
| Debian | [debian.md](debian.md) | `apt` | 13 (Trixie), 12 (Bookworm) |
| RHEL | [rhel.md](rhel.md) | `dnf` | 8, 9, 10 |
| CentOS Stream | [centos.md](centos.md) | `dnf` | Stream 9, Stream 10 |
| Fedora | [fedora.md](fedora.md) | `dnf` | 45, 44, 43 |
| Raspberry Pi OS (32-bit) | [raspberry-pi-os.md](raspberry-pi-os.md) | `apt` | 12 (Bookworm), 11 (Bullseye) |
| Any other Linux | [binaries.md](binaries.md) | none (static binaries) | 64-bit, kernel 3.10+ |

> **Raspberry Pi with a 64-bit OS?** Use the [Debian guide](debian.md) instead.

### CPU architectures

| Platform | x86_64 / amd64 | arm64 / aarch64 | arm (32-bit) | ppc64le | s390x |
|----------|:-:|:-:|:-:|:-:|:-:|
| CentOS | ✅ | ✅ | | ✅ | |
| Debian | ✅ | ✅ | ✅ | ✅ | |
| Fedora | ✅ | ✅ | | ✅ | |
| Raspberry Pi OS (32-bit) | | | ⚠️ (see guide) | | |
| RHEL | ✅ | ✅ | | | ✅ |
| Ubuntu | ✅ | ✅ | ✅ | ✅ | ✅ |
| Binaries | ✅ | ✅ | ✅ | | |

### Derivative distributions (not officially supported)

Docker does not test or verify installs on derivatives. The steps for the parent distro often work, but you are on your own if they don't.

| Your system | Try this guide | Note |
|-------------|----------------|------|
| Linux Mint, Pop!_OS, elementary OS | [Ubuntu](ubuntu.md) | Use the Ubuntu codename (check `UBUNTU_CODENAME` in `/etc/os-release`) |
| Kali Linux, other Debian-based | [Debian](debian.md) | May work |
| AlmaLinux, Rocky Linux, Oracle Linux | [RHEL](rhel.md) | May work |
| Arch, openSUSE, Alpine, others | [binaries.md](binaries.md) or your distro's own `docker` package | Docker publishes no packages for these |

---

## 3️⃣ What Every Install Does (the Pattern)

All the package-based guides follow the same five steps. Only the commands differ.

| Step | What happens | Why |
|------|--------------|-----|
| 1. Remove old packages | Uninstall unofficial `docker.io`, `podman-docker`, old `containerd` and so on | Avoids conflicts with Docker's official packages |
| 2. Add Docker's repository | Add Docker's signing key and package source | Gets the latest official packages and updates |
| 3. Install | `docker-ce`, `docker-ce-cli`, `containerd.io`, `docker-buildx-plugin`, `docker-compose-plugin` | The engine, CLI, runtime, and plugins |
| 4. Start the service | `systemctl` (automatic on Debian-family, manual on RPM-family) | The daemon must be running |
| 5. Verify | `sudo docker run hello-world` | Confirms client → daemon → registry → container works |

What the five packages are:

| Package | What it is |
|---------|-----------|
| `docker-ce` | Docker Engine (the daemon) |
| `docker-ce-cli` | The `docker` command-line client |
| `containerd.io` | Container runtime |
| `docker-buildx-plugin` | Extended image builds (`docker buildx`) |
| `docker-compose-plugin` | Docker Compose v2 (`docker compose`) |

After installing, do the [post-installation steps](post-install.md) to run Docker without `sudo`.

---

## ⚡ Quick Install: Convenience Script (Testing Only)

Docker offers one script that detects your distro and installs Docker.

```bash
# Download the script
curl -fsSL https://get.docker.com -o get-docker.sh
# Preview what it will do, without changing anything
sudo sh ./get-docker.sh --dry-run
# Run the install
sudo sh get-docker.sh
```

⚠️ **Not for production.** The official docs say it is meant for development and testing. It needs root, installs dependencies without asking, installs the latest stable version, and is not designed for upgrading an existing install. After using it, upgrade through your package manager. On Debian-family systems the service starts automatically; on RPM-family systems start it yourself with `sudo systemctl enable --now docker`.

---

## 🔥 Firewall Note (All Distros)

- Ports you publish with Docker (`-p 8080:80`) **bypass `ufw` and `firewalld` rules**. Do not rely on them to block container ports.
- Docker supports only `iptables-nft` and `iptables-legacy`. Firewall rules made directly with `nft` are not supported.
- To add your own rules, use `iptables` or `ip6tables` and put them in the `DOCKER-USER` chain.

---

## 🧯 Common Errors (All Distros)

| Error | Cause | Fix |
|-------|-------|-----|
| `permission denied while trying to connect to the Docker daemon socket` | Your user is not in the `docker` group | Follow [post-install](post-install.md), then log out and in |
| `Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?` | The daemon is not running | `sudo systemctl start docker` |
| `docker: command not found` | Docker is not installed, or the install failed | Redo the install step and read its output |
| `E: Unable to locate package docker-ce` (apt) | The repository was not added correctly | Redo the repository step, then `sudo apt update` |
| `NO_PUBKEY` / GPG error (apt) | The signing key is missing or unreadable | Redo the key download, including `chmod a+r` |
| `No match for argument: docker-ce` (dnf) | The repository was not added | Redo the repository step |
| `failed to find iptables` (Fedora) | Wrong iptables backend | See the [Fedora guide](fedora.md) |
| `WARNING: Error loading config file ... permission denied` | You ran `docker` with `sudo` earlier | See [post-install](post-install.md) |

---

## ✅ Summary

1. Find your distro with `cat /etc/os-release`.
2. Open the matching guide: Ubuntu, Debian, RHEL, CentOS, Fedora, Raspberry Pi OS, or binaries.
3. Remove old packages → add Docker's repo → install → start → verify.
4. Do the [post-install steps](post-install.md).

⬅️ Previous: [02 — Docker Architecture](../02-docker-architecture.md)

➡️ Next: [04 — Docker CLI: First Commands](../04-docker-cli-first-commands.md)
