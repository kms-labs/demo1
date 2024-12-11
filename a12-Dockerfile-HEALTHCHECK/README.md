# Learn with HEALTHCHECK

---

## Introduction

In this guide, you will:

- Create an Nginx Dockerfile with `nginx:alpine-slim`.
- Add a health check using `HEALTHCHECK`.
- Build and test the image.

---

## Step 1: Create Dockerfile and Custom `index.html`

- **Dockerfile HEALTHCHECK Instruction Reference:** [Dockerfile HEALTHCHECK Instruction](https://docs.docker.com/engine/reference/builder/#healthcheck)

- **Directory:** `Dockerfiles`

**Create a `Dockerfile` with the following content:**

```dockerfile
# Use nginx:alpine-slim as base Docker Image
FROM nginx:alpine-slim

# OCI Labels
LABEL org.opencontainers.image.authors="KMS Healthcare"
LABEL org.opencontainers.image.title="Demo: HEALTHCHECK Instruction in Docker"
LABEL org.opencontainers.image.description="A Dockerfile demo illustrating the use of the HEALTHCHECK instruction"
LABEL org.opencontainers.image.version="1.0"

# Install curl (needed for our Healthcheck command)
RUN apk --no-cache add curl

# Using COPY to copy a local file
COPY index.html /usr/share/nginx/html

# The HEALTHCHECK instruction tells Docker how to test a container to check that it's still working
HEALTHCHECK --interval=30s --timeout=30s --start-period=5s --start-interval=5s --retries=3 CMD curl -f http://localhost/ || exit 1
```

**Create a custom `index.html`:**

```html
<!DOCTYPE html>
<html>
  <body style='background-color:rgb(227, 213, 180);'>
    <h1>Welcome to KMS-Healthcare Demo - Dockerfile HEALTHCHECK Instruction</h1>
    <p>Learn technology through practical, real-world demos.</p>
    <p>Application Version: V1</p>
  </body>
</html>
```

---

## Step 2: Build Docker Image and Run It

```bash
# Change directory
cd $(git rev-parse --show-toplevel)/a12-Dockerfile-HEALTHCHECK/Dockerfiles

# Build the Docker image
docker build -t demo12-dockerfile-healthcheck:v1 .

# Inspect the Healthcheck settings of the Docker Image
docker image inspect --format='{{json .Config.Healthcheck}}' demo12-dockerfile-healthcheck:v1

# Run the Docker Container
docker run --name my-healthcheck-demo -p 8080:80 -d demo12-dockerfile-healthcheck:v1

# List Docker Containers
docker ps

# Expected Output:
# CONTAINER ID   IMAGE                             COMMAND                  CREATED          STATUS                    PORTS                  NAMES
# e63e7fe79986   demo12-dockerfile-healthcheck:v1  "/docker-entrypoint.…"   17 seconds ago   Up 15 seconds (healthy)   0.0.0.0:8080->80/tcp   my-healthcheck-demo

# Inspect the health status of the container
docker inspect --format='{{json .State.Health}}' my-healthcheck-demo | jq

# Observations:
# 1. The container should be in a **healthy** state as indicated by the `STATUS` column in `docker ps`.
# 2. The `HEALTHCHECK` instruction periodically checks the health of the application inside the container.
```

Access the application in your browser http://localhost:8080

---

## Step 3: Stop and Remove Container and Images

```bash
# Stop and remove the container
docker rm -f my-healthcheck-demo

# Remove the Docker images from local machine
docker rmi demo12-dockerfile-healthcheck:v1
```

---

## Conclusion

You have successfully:

- Created an Nginx Dockerfile with `HEALTHCHECK`.
- Built and ran the image, verifying health status.

---

## Additional Notes

**HEALTHCHECK:**
- Defines how Docker checks if a container is functioning.
- Useful for monitoring the app's health.

**Syntax:**
```dockerfile
HEALTHCHECK [OPTIONS] CMD command
```

**Options:**
- `--interval=`: Time between checks (default: `30s`).
- `--timeout=`: Max time for a check (default: `30s`).
- `--start-period=`: Init time before checks (default: `0s`).
- `--retries=`: Failures before marking unhealthy (default: `3`).

**Observations:**
- Health statuses: `starting`, `healthy`, `unhealthy`.
- Use `docker inspect` for detailed health info.

---
