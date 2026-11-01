# 🐳 01 — What is Docker · Why Docker · Docker vs VM

## 🐧 Quick Linux Background

Docker is built on Linux, so a little Linux helps.

**Linux = Kernel + Tools**

- **Linux kernel:** the core that talks to the hardware (CPU, RAM, disk, network).
- **Userland tools:** Bash, system utilities, libraries, the things we use on top of the kernel.

A **distribution (distro)** is an operating system built from the Linux kernel plus supporting libraries and software.

| Family | Description | Examples |
|--------|-------------|----------|
| Debian | Stable, community-driven | Debian, Ubuntu, Linux Mint, Kali Linux |
| RHEL | Enterprise-focused (paid support) | RHEL, CentOS, Fedora, AlmaLinux, Rocky Linux |
| Arch | Rolling release, minimal base | Arch Linux, Manjaro, EndeavourOS |
| SUSE | European enterprise distros | openSUSE, SUSE Linux Enterprise |
| Slackware | One of the oldest, highly customizable | Slackware, Salix OS |

**Linux architecture**

```
+-------------------------+
|   User Applications     |   <- bash, Python, Docker ...
+-------------------------+
|     System Libraries    |   <- glibc, openssl ...
+-------------------------+
|       Linux Kernel      |   <- processes, memory, filesystems, networking, security
+-------------------------+
|        Hardware         |   <- CPU, RAM, disk, network
+-------------------------+
```

This guide uses **Ubuntu** (Debian family). Containers use the host's kernel, which is why the kernel matters here.

---

## 📌 What is Docker?

Docker is an open-source platform that packages an application together with everything it needs to run (code, runtime, libraries, system tools, configuration) into a single unit called a **container**.

That container runs the same way on your laptop, on a teammate's machine, on a test server, and in production — as long as Docker is installed.

### What is a container?

A container is an isolated process running on the host machine. It has its own filesystem, its own network, and its own process list, but it **shares the host's Linux kernel**. That is why containers are small and start in seconds.

---

## 🚢 Build · Ship · Run

Docker's idea in three words:

| Step | What you do | Example |
|------|-------------|---------|
| **Build** | Package your app and everything it needs into an image | `docker build -t myapp .` |
| **Ship** | Upload the image to a registry such as Docker Hub | `docker push <username>/myapp` |
| **Run** | Anyone downloads the image and runs it as a container, anywhere | `docker run <username>/myapp` |

**Analogy:** build = packing your lunchbox, ship = sending it to a friend, run = your friend opens it and eats, with no cooking needed.

```
Dockerfile  ➜  docker build  ➜  docker push  ➜  docker run
Source code    Create image     Share image     Run container
```

---

## ❓ Why Docker?

### The classic problem

> "It works on my machine!"

Without Docker, an app depends on whatever is installed on the machine: the OS version, the Node/Python/Java version, system libraries, environment variables. Change any of them and the app can break.

### How Docker solves it

| Problem without Docker | How Docker solves it |
|------------------------|----------------------|
| "Works on my machine" | The same image runs identically everywhere |
| Slow, manual environment setup | One command (`docker run`) starts the whole environment |
| Dependency conflicts (App A needs Python 3.8, App B needs 3.12) | Each container carries its own dependencies, fully isolated |
| Heavy resource use of full VMs | Containers share the host kernel, so they use far less CPU and RAM |
| Hard to scale and deploy | Containers start in seconds and are easy to replicate |
| Onboarding new developers takes days | `git clone` + `docker compose up` and they are ready |

### Where Docker is used

- Local development environments
- Microservices
- CI/CD pipelines (build, test, deploy in clean containers)
- Cloud deployment (AWS ECS/EKS, Kubernetes, etc.)

---

## ⚖️ Docker vs Virtual Machine

A **virtual machine (VM)** emulates a full computer, including its own complete operating system, on top of a **hypervisor**.
A **container** shares the host OS kernel and only packages the app and its dependencies.

```
   Virtual Machines                      Docker Containers

 ┌───────┐ ┌───────┐ ┌───────┐         ┌───────┐ ┌───────┐ ┌───────┐
 │  App  │ │  App  │ │  App  │         │  App  │ │  App  │ │  App  │
 │ Bins/ │ │ Bins/ │ │ Bins/ │         │ Bins/ │ │ Bins/ │ │ Bins/ │
 │ Libs  │ │ Libs  │ │ Libs  │         │ Libs  │ │ Libs  │ │ Libs  │
 │ Guest │ │ Guest │ │ Guest │         └───────┘ └───────┘ └───────┘
 │  OS   │ │  OS   │ │  OS   │         ┌───────────────────────────┐
 └───────┘ └───────┘ └───────┘         │       Docker Engine       │
 ┌───────────────────────────┐         ├───────────────────────────┤
 │        Hypervisor         │         │          Host OS          │
 ├───────────────────────────┤         ├───────────────────────────┤
 │          Host OS          │         │      Infrastructure       │
 ├───────────────────────────┤         └───────────────────────────┘
 │      Infrastructure       │
 └───────────────────────────┘
```

| Feature | Docker Container | Virtual Machine |
|---------|------------------|-----------------|
| Isolation level | Process-level (shares host kernel) | Hardware-level (own kernel) |
| Guest OS | None — uses host kernel | Full OS per VM |
| Size | Megabytes | Gigabytes |
| Startup time | Seconds | Minutes |
| Resource usage | Light | Heavy |
| Performance | Near-native | Slower (hypervisor overhead) |
| Portability | Very high (image runs anywhere Docker runs) | Lower (large disk images) |
| Security isolation | Good, but weaker than a VM (shared kernel) | Stronger (separate kernel) |
| Best for | Microservices, CI/CD, fast scaling | Running different OSes, strong isolation |

**Analogy:** Docker is an apartment in a building (shared walls and resources). A VM is an independent house (everything separate).

> 💡 Containers and VMs are not enemies. In production, containers often run *inside* VMs (for example, on AWS EC2 instances).

---

## 🧠 Key Terms

| Term | Meaning |
|------|---------|
| **Image** | A read-only template (app + dependencies) used to create containers |
| **Container** | A running instance of an image |
| **Dockerfile** | A text file with instructions to build an image |
| **Registry** | A place to store and share images (e.g. Docker Hub) |
| **Docker Engine** | The software (daemon + CLI) that builds and runs containers |

**Analogy:** an image is like a recipe, and a container is the dish cooked from it. You can cook many dishes from one recipe.

---

## ✅ Summary

- Docker packages an app and its dependencies into a portable **container**.
- It removes "works on my machine" problems and speeds up setup and deployment.
- Containers share the host kernel, so they are lighter and faster than VMs.
- Image = template, container = running instance.

➡️ Next: [02 — Docker Architecture](02-docker-architecture.md)
