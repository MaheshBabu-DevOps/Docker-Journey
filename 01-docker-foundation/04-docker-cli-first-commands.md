# ⌨️ 04 — Docker CLI: First Commands

This topic covers the commands you need on day one. Later phases go deeper: images in Phase 2, containers in Phase 3.

> Sample outputs below are examples. IDs, sizes, dates, and versions on your machine will differ.

General syntax:

```
docker [OPTIONS] COMMAND [ARGUMENTS]
```

---

## 1️⃣ Information Commands

### `docker --version`
Short client version.

```bash
docker --version
```
```
Docker version 27.x.x, build xxxxxxx
```

### `docker version`
Detailed version of both **Client** and **Server (daemon)**. If the Server section is missing or errors, the daemon is not running or you lack permission.

```bash
docker version
```

### `docker info`
System-wide details: number of containers and images, storage driver, OS, resources.

```bash
docker info
```

### `docker --help` / `docker <command> --help`
Lists all commands, or the options of one command.

```bash
docker --help
docker run --help
```

---

## 2️⃣ Working with Images

### `docker pull` — download an image

```bash
docker pull nginx
```

Downloads `nginx:latest` from Docker Hub. To choose a version, add a **tag**:

```bash
docker pull nginx:1.27
```

### `docker images` — list local images

```bash
docker images
```
```
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
nginx        latest    xxxxxxxxxxxx   2 weeks ago    192MB
hello-world  latest    xxxxxxxxxxxx   10 months ago  13.3kB
```

| Column | Meaning |
|--------|---------|
| REPOSITORY | Image name |
| TAG | Version label (`latest` if you did not choose one) |
| IMAGE ID | Unique ID of the image |
| SIZE | Disk size |

### `docker rmi` — remove an image

```bash
docker rmi nginx
```

An image cannot be removed while a container (even a stopped one) still uses it. Remove the container first.

---

## 3️⃣ Working with Containers

### `docker run` — create and start a container

```bash
docker run hello-world
```

`docker run` = pull the image if needed + create a container + start it.

Common options:

| Option | Meaning |
|--------|---------|
| `-d` | Run in the background (**d**etached) |
| `--name <name>` | Give the container a name |
| `-p <host>:<container>` | Publish a port: map a host port to a container port |
| `-it` | Interactive terminal (used to get a shell inside) |
| `--rm` | Delete the container automatically when it stops |

Examples:

```bash
# Run Nginx in the background, named "web", on host port 8080
docker run -d --name web -p 8080:80 nginx

# Open an interactive shell in a temporary Ubuntu container
docker run -it --rm ubuntu bash
```

Type `exit` to leave the Ubuntu shell. Because of `--rm`, the container is removed on exit.

### `docker ps` — list running containers

```bash
docker ps
```
```
CONTAINER ID   IMAGE   COMMAND                  CREATED         STATUS         PORTS                  NAMES
xxxxxxxxxxxx   nginx   "/docker-entrypoint.…"   10 seconds ago  Up 9 seconds   0.0.0.0:8080->80/tcp   web
```

### `docker ps -a` — list all containers (including stopped)

```bash
docker ps -a
```

### `docker stop` — stop a running container

```bash
docker stop web
```

### `docker start` — start a stopped container

```bash
docker start web
```

### `docker rm` — remove a stopped container

```bash
docker rm web
```

To stop and remove in one step, use `docker rm -f web`.

> You can use the container **name** or the container **ID** (the first few characters are enough).

---

## 🧪 Hands-on: Run Nginx and Open It in the Browser

**1. Start the container:**

```bash
docker run -d --name web -p 8080:80 nginx
```

**2. Confirm it is running:**

```bash
docker ps
```

**3. Open it:** go to http://localhost:8080 in your browser, or:

```bash
curl http://localhost:8080
```

You should see the **"Welcome to nginx!"** page.

**4. Stop and clean up:**

```bash
docker stop web
docker rm web
docker rmi nginx
```

**5. Verify nothing is left:**

```bash
docker ps -a
docker images
```

### What just happened?

1. `docker run` found no local `nginx` image, so the daemon pulled it from Docker Hub.
2. It created a container named `web` from that image.
3. `-p 8080:80` forwarded your host's port 8080 to port 80 inside the container, where Nginx listens.
4. `-d` kept it running in the background so your terminal stayed free.

---

## 🐍 Example: Build, Ship, Run a Flask App

This shows the full **Dockerfile ➜ build ➜ push ➜ run** flow with a tiny app. Phase 2 and Phase 6 explain each step in detail.

**1. `app.py`**

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello from Docker!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

`host="0.0.0.0"` is required, otherwise the app only listens inside the container and the published port will not work.

**2. `Dockerfile`**

```dockerfile
FROM python:3.12-slim

WORKDIR /app
COPY app.py .
RUN pip install flask

CMD ["python", "app.py"]
```

**3. Build and run**

```bash
docker build -t flask-app .
docker run -d --name flask_container -p 5000:5000 flask-app
```

**4. Access:** http://localhost:5000

**5. Ship it (optional, needs a Docker Hub account)**

```bash
docker tag flask-app <your-dockerhub-username>/flask-app
docker push <your-dockerhub-username>/flask-app
```

Anyone can now run your app anywhere with `docker run -p 5000:5000 <your-dockerhub-username>/flask-app`.

| Step | Command | Result |
|------|---------|--------|
| Build | `docker build -t flask-app .` | Creates an image with the app |
| Ship | `docker push <user>/flask-app` | Uploads it to Docker Hub |
| Run | `docker run -p 5000:5000 flask-app` | App live at `localhost:5000` |

---

## 📋 Quick Reference

| Command | Purpose |
|---------|---------|
| `docker --version` | Show client version |
| `docker version` | Show client and server versions |
| `docker info` | Show system-wide information |
| `docker pull <image>` | Download an image |
| `docker images` | List local images |
| `docker rmi <image>` | Remove an image |
| `docker run <image>` | Create and start a container |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers |
| `docker stop <name>` | Stop a container |
| `docker start <name>` | Start a stopped container |
| `docker rm <name>` | Remove a container |

---

## ✏️ Practice

1. Run `hello-world`, then find its container with `docker ps -a`.
2. Run Nginx on port `9090` instead of `8080`.
3. Run a container named `test` from `ubuntu`, exit it, then restart it with `docker start`.
4. Remove all the containers and images you created.

---

## ✅ Summary

- Information: `docker version`, `docker info`, `--help`
- Images: `pull`, `images`, `rmi`
- Containers: `run`, `ps`, `stop`, `start`, `rm`
- `docker run` is the key command: pull, create, and start in one step.

⬅️ Previous: [03 — Installation on Ubuntu](03-installation-ubuntu.md)
➡️ Next: [05 — How Docker Uses Linux Internals](05-linux-internals-for-docker.md)
