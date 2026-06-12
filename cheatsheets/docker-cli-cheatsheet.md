# Docker CLI Cheat Sheet

A quick reference for the Docker Command Line Interface.

---

## What is the Docker CLI?

The Docker CLI (`docker`) is the primary tool used to interact with the Docker daemon. It allows you to build images, run containers, manage networks and volumes, and clean up resources from your terminal.

```text
 Terminal ──► docker run ──► Docker Daemon ──► Container
```

---

## Basic Workflow

```bash
# Build an image from a Dockerfile
docker build -t my-app .

# Run a container from the image in the background
docker run -d -p 8080:80 my-app

# Check running containers
docker ps
```

---

## Common Commands

### Image Management

```bash
docker build -t <image_name>:<tag> .
```
Builds a new Docker image using the `Dockerfile` in the current directory (`.`).

```bash
docker pull <image_name>
```
Downloads an image from a remote registry (like Docker Hub) to your local machine.

```bash
docker images
```
Lists all locally stored Docker images.

### Container Lifecycle

```bash
docker run -d --name <container_name> <image_name>
```
Creates and starts a new container based on the specified image.

```bash
docker ps
```
Lists currently running containers. Add `-a` to list all containers (including stopped ones).

```bash
docker stop <container_id>
```
Gracefully stops a running container.

---

## Common Operations

### View Logs

```bash
docker logs -f <container_name_or_id>
```
Fetches the logs of a container. The `-f` flag follows the log output live.

### Execute a Command

```bash
docker exec -it <container_name> sh
```
Opens an interactive shell inside an already running container.

### Cleanup Resources

```bash
docker system prune -a
```
Removes all stopped containers, unused networks, dangling images, and optionally all unused images (`-a`).

---

## Examples

### Run a Web Server with Port Mapping

```bash
docker run -d -p 8080:80 nginx
```
Runs Nginx in the background (`-d`), mapping port 80 inside the container to port 8080 on your host machine.

### Run with a Volume Bind Mount

```bash
docker run -v $(pwd)/src:/usr/src/app -w /usr/src/app node:16 index.js
```
Mounts your local `src/` directory to the container, sets the working directory, and executes a script.

---

## Useful Options

*Note: These are flags typically used with the `docker run` command.*

| Option | Description |
|---|---|
| `-d` | Run container in background (detached mode) and print container ID. |
| `-p` | Publish a container's port(s) to the host (`host_port:container_port`). |
| `-v` | Bind mount a volume (`host_path:container_path`). |
| `-e` | Set environment variables (`-e MY_VAR=value`). |
| `--rm` | Automatically remove the container when it exits. |
| `-it` | Keep STDIN open even if not attached, and allocate a pseudo-TTY (interactive). |

---

## Typical Workflow

```text
Write Dockerfile
  │
  ▼
docker build (Create Image)
  │
  ▼
docker run (Start Container)
  │
  ▼
docker logs / docker exec (Debug/Monitor)
```

---

## Common Mistakes

❌ **Mistake:** Running a database container without a volume, resulting in all data being lost when the container is removed.

```bash
docker run -d postgres
```

✅ **Correct:** Attach a named volume to persist the database data securely.

```bash
docker run -d -v pgdata:/var/lib/postgresql/data postgres
```

❌ **Mistake:** Forgetting the `.` at the end of the build command.

```bash
docker build -t my-app
```

✅ **Correct:** Specify the build context (usually `.` for current directory).

```bash
docker build -t my-app .
```

---

## Commands You'll Use Most

```bash
docker run -d -p 80:80 <image>
docker ps -a
docker logs -f <container>
docker exec -it <container> /bin/bash
docker rm -f <container>
docker rmi <image>
```
