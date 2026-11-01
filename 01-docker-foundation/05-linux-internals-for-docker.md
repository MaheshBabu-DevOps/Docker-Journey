# 🐧 05 — How Docker Uses Linux Internals

Docker runs on top of Linux and relies on Linux kernel features to create and manage containers. Containers are lightweight, fast, and isolated because of the kernel, not because Docker emulates a machine.

---

## 🧱 Real-World Docker Architecture on Ubuntu

```
+---------------------------+
|     Docker CLI / API      |  <- You run commands (docker run ...)
+---------------------------+
             |
             v
+---------------------------+
|       Docker Daemon       |  <- dockerd: manages containers, images, networks, volumes
+---------------------------+
             |
             v
+---------------------------+
|     Container Runtime     |  <- containerd + runc (actually runs containers)
+---------------------------+
             |
             v
+--------------------------------------------------------------+
|                 Linux Operating System (Ubuntu)              |
|                                                              |
|  +--------------------------------------------------------+  |
|  |                     Linux Kernel                       |  |
|  |  (namespaces, cgroups, overlayfs, networking, seccomp) |  |
|  +--------------------------------------------------------+  |
|                                                              |
|  +------------+   +------------+   +---------------+         |
|  | Containers |   |   Images   |   |    Volumes    |         |
|  +------------+   +------------+   +---------------+         |
+--------------------------------------------------------------+
             |
             v
+---------------------------+
|         Hardware          |
+---------------------------+
```

### The same stack, bottom-up with examples

```
+----------------------------+
|  App Containers            |  e.g. MySQL, Jenkins, ROS2 — all share the host kernel
+----------------------------+
|  Docker Engine             |  dockerd manages images, containers, volumes, networks
+----------------------------+
|  Ubuntu (host OS)          |  Docker runs on top of this
+----------------------------+
|  Hardware                  |  physical machine or cloud VM
+----------------------------+
```

### Components

| Component | Role |
|-----------|------|
| **Docker Client** | CLI/API that sends commands to the daemon (`dockerd`) |
| **Docker Daemon** | Manages containers, images, networks, volumes; executes commands |
| **Container Runtime** | `containerd` and `runc`, which actually run the containers |
| **Linux Kernel** | Namespaces (isolation), cgroups (resource control), union filesystems |
| **Containers** | Running instances of Docker images (isolated apps) |
| **Images** | Read-only templates used to create containers |
| **Volumes** | Persistent storage that outlives a container |
| **Docker Registry** | Stores and distributes images (e.g. Docker Hub) |
| **Docker Network** | Handles container communication and connectivity |

---

## 💡 Key Linux Kernel Features Docker Uses

| Linux feature | What it does | Analogy |
|---------------|--------------|---------|
| **Namespaces** | Isolate processes, filesystems, users, networks | Each container lives in its own apartment |
| **cgroups** | Limit CPU, memory, and I/O per container | Budgeting resources per tenant |
| **UnionFS / overlay2** | Layered filesystems for images and containers | Transparent layers on a whiteboard |
| **chroot** | Restricts the root directory | Locking a user in their home |
| **seccomp** | Filters which system calls are allowed | A syscall firewall |
| **AppArmor / SELinux** | Mandatory access control | A security guard for each process |
| **Network namespaces** | Virtual network interfaces per container | Own Wi-Fi router per apartment |

---

## 📚 Image Layers

An image is built from read-only layers stacked on top of each other. A running container adds a thin writable layer on top.

```
Layer 5: CMD / ENTRYPOINT / EXPOSE
Layer 4: App code           (COPY . /app)
Layer 3: App dependencies   (pip / npm / maven install)
Layer 2: System packages    (apt install curl, ...)
Layer 1: Base OS            (Ubuntu, Alpine, Debian, ...)
```

Layers are cached and shared between images, so builds and pulls are fast. (Phase 2 and Phase 9 cover this in depth.)

---

## 🔄 Simplified Flow

```
Docker CLI
   |
   v
Docker Daemon (dockerd)
   |
   v
containerd → runc
   |
   v
Linux Kernel features (namespaces, cgroups, overlay2)
   |
   v
Containers (isolated, lightweight environments)
```

---

## 🔍 Behind the Scenes of `docker run hello-world`

1. You run `docker run hello-world`.
2. The Docker CLI sends the command to the Docker Daemon.
3. The daemon uses `containerd` and `runc` to create and run the container.
4. The Linux kernel:
   - applies **namespaces** for isolation,
   - applies **cgroups** for resource limits,
   - uses **UnionFS (overlay2)** for image layering.
5. The container runs inside its own isolated Linux environment.

---

## ✅ Summary

- Docker is a thin management layer over Linux kernel features.
- **Namespaces** isolate, **cgroups** limit, and **overlay2** layers the filesystem.
- `dockerd` → `containerd` → `runc` → kernel is the path every container takes.
- Because containers share the host kernel, they are fast, but also less isolated than VMs.

⬅️ Previous: [04 — Docker CLI: First Commands](04-docker-cli-first-commands.md)
➡️ Next phase: Phase 2 — Docker Images
