# Docker Compose Cheat Sheet

A quick reference for Docker Compose.

---

## What is Docker Compose?

Docker Compose is a tool for defining and running multi-container applications. It uses a YAML file (usually `docker-compose.yml` or `compose.yaml`) to configure application services, networks, and volumes, allowing you to orchestrate them all with a single command.

```text
 compose.yaml ──► docker compose up -d ──► [ Frontend Container ]
                                       ──► [ Backend Container ]
                                       ──► [ Database Container ]
```

---

## Basic Workflow

```bash
# Start all services in the background
docker compose up -d

# View live logs for all services
docker compose logs -f

# Stop and remove all services and networks
docker compose down
```

---

## Common Commands

### Start Services

```bash
docker compose up -d
```

Creates and starts all containers defined in `compose.yaml` in the background (detached mode). Add `--build` to force rebuild images before starting.

### Stop and Remove

```bash
docker compose down
```

Stops running containers and removes containers, default networks, and anonymous volumes. Use `-v` to also remove named volumes (like databases).

### Build Images

```bash
docker compose build
```

Builds or rebuilds services defined in the compose file.

---

## Common Operations

### View Logs

```bash
docker compose logs -f [service_name]
```

Follow logs for all services. Provide a `service_name` to only view logs for a specific service.

### Execute Command in Running Container

```bash
docker compose exec <service_name> sh
```

Opens an interactive shell inside a currently running service container.

### Check Container Status

```bash
docker compose ps
```

Lists all running containers associated with the current project and their port mappings.

### Run a One-Off Command

```bash
docker compose run --rm <service_name> <command>
```

Starts a brand new container for the service, runs the command, and removes the container when done. Useful for database migrations or one-off scripts.

---

## Examples

### Force Rebuild and Start

```bash
docker compose up -d --build
```

### Run with a Specific Compose File

```bash
docker compose -f docker-compose.prod.yml up -d
```

### Scale a Service

```bash
docker compose up -d --scale <service_name>=3
```

Starts 3 identical instances of a specific service (requires a load balancer if exposing ports).

---

## Useful Options (Global)

*Note: These go BEFORE the subcommand (e.g., `docker compose -f file.yml up`)*

| Option   | Description |
| -------- | ----------- |
| `-f`     | Specify an alternate compose file. Can be used multiple times to merge overriding files. |
| `-p`     | Specify a custom project name (defaults to the current directory name). |
| `--env-file` | Specify an alternate environment file. |

---

## Typical Workflow

```text
Write compose.yaml
  │
  ▼
docker compose up -d (Start environment)
  │
  ▼
docker compose logs -f (Monitor application)
  │
  ▼
docker compose down (Clean up environment)
```

---

## Common Mistakes

❌ **Mistake:** Using `up` without `-d` and then pressing `Ctrl+C`, unintentionally stopping the entire stack.

```bash
docker compose up
```

✅ **Correct:** Run in detached mode, then tail logs if you want to watch the output.

```bash
docker compose up -d
docker compose logs -f
```

❌ **Mistake:** Restarting services with `up -d` but forgetting that Dockerfile changes aren't automatically rebuilt.

```bash
docker compose up -d
```

✅ **Correct:** Use `--build` to ensure the latest image is built from the source code.

```bash
docker compose up -d --build
```

---

## Commands You'll Use Most

```bash
docker compose up -d
docker compose down
docker compose logs -f
docker compose ps
docker compose exec <service_name> sh
docker compose build
```
