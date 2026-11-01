# 📦 Install Docker Engine from Static Binaries (Any Linux)

Official page: https://docs.docker.com/engine/install/binaries/

## When to Use This

- For **testing or evaluation**, or
- when your Linux has **no official Docker package** (Arch, openSUSE, Alpine, and similar).

If your distribution is supported, use its package guide instead (see the [index](README.md)).

## ⚠️ Trade-offs

- Static binaries do **not** get automatic security updates, and your distribution will not patch their dependencies.
- You must update by hand for each release.
- They may lack some features of the packaged versions.
- Don't turn off SELinux or AppArmor to make Docker run. Configure them properly.

## ✅ Prerequisites

| Requirement | Notes |
|-------------|-------|
| 64-bit system | |
| Linux kernel 3.10 or later | The latest available for your platform is recommended |
| `iptables` 1.4 or later | |
| `git` 1.7 or later | |
| `ps` executable | Usually from the `procps` package |
| XZ Utils 4.9 or later | |
| A properly mounted cgroupfs hierarchy | Not a single all-in-one cgroup mount |

Recommended: SELinux or AppArmor, seccomp profiles, and user namespaces.

```bash
# Check your kernel version and architecture
uname -r
uname -m
# Check iptables and git versions
iptables --version
git --version
```

---

## 1️⃣ Download

Get the `.tgz` for your CPU architecture and the Docker Engine version you want from:

https://download.docker.com/linux/static/stable/

Pick the folder that matches `uname -m`, for example `x86_64` or `aarch64`.

## 2️⃣ Extract

```bash
tar xzvf /path/to/<FILE>.tgz
```

This creates a `docker/` folder containing `dockerd`, `docker`, and the other binaries.

## 3️⃣ Copy to Your PATH (Optional)

```bash
sudo cp docker/* /usr/bin/
```

If you skip this, you must type the full path every time you run `docker` or `dockerd`.

## 4️⃣ Start the Daemon

```bash
sudo dockerd &
```

For extra options, add them to the command or put them in `/etc/docker/daemon.json`.

## 5️⃣ Verify

```bash
sudo docker run hello-world
```

---

## 🔄 Upgrade

Stop any running `dockerd` process, then extract and copy the new version over the old one.

## 🧯 Notes

- Without root you need `sudo`, because the `docker` group has no users. See the [post-install steps](post-install.md).
- These steps start the daemon by hand. They do not set up a systemd service.

---

⬅️ [Install index](README.md) · ➡️ Next: [Post-installation steps](post-install.md)
