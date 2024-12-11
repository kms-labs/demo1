# Learn with ARG

---

## Introduction

In this guide, you will:

- Create an Nginx Dockerfile with a version passed via `ARG`.
- Build images using a default or custom Nginx version (`--build-arg`).
- Run containers and verify the Nginx version.

---

## Step 1: Create Dockerfile with ARG Instruction

- **Directory:** `Dockerfiles`

Create a `Dockerfile` with the following content:

```dockerfile
# Define a build-time argument for the NGINX version
ARG NGINX_VERSION=1.26

# Use nginx:alpine-slim as base Docker Image with specified NGINX_VERSION
FROM nginx:${NGINX_VERSION}-alpine-slim

# OCI Labels
LABEL org.opencontainers.image.authors="KMS Healthcare"
LABEL org.opencontainers.image.title="Demo: Using ARG Instruction"
LABEL org.opencontainers.image.description="A Dockerfile demo illustrating the ARG instruction"
LABEL org.opencontainers.image.version="1.0"

# Copy a custom index.html to the Nginx HTML directory
COPY index.html /usr/share/nginx/html
```

**Create a simple `index.html`:**

```html
<!DOCTYPE html>
<html>
  <body style='background-color:rgb(227, 213, 180);'>
    <h1>Welcome to KMS-Healthcare Demo - ARG Build-time Variables</h1>
    <p>Learn technology through practical, real-world demos.</p>
    <p>Application Version: V1</p>
  </body>
</html>

```

---

## Step 2: Build Docker Images and Run Them

### Build Docker Image with Default ARG Value

```bash
# Change directory
cd $(git rev-parse --show-toplevel)/a07-Dockerfile-ARG/Dockerfiles

# Build the Docker image
docker build -t demo7-dockerfile-arg:v1.26 .

# Run Docker Container and Verify
docker run --name my-arg-demo1 -p 8080:80 -d demo7-dockerfile-arg:v1.26

# Verify Nginx version inside the container
docker exec -it my-arg-demo1 nginx -v

# Expected Output:
# - The Nginx version should be 1.26.
# - The custom index page should display when accessing http://localhost:8080.
```

Access the application in your browser http://localhost:8080

### Build Docker Image by Overriding ARG Value

```bash
# Build the Docker image
docker build --build-arg NGINX_VERSION=1.27 -t demo7-dockerfile-arg:v1.27 .

# Run Docker Container and Verify
docker run --name my-arg-demo2 -p 8081:80 -d demo7-dockerfile-arg:v1.27

# Verify Nginx version inside the container
docker exec -it my-arg-demo2 nginx -v

# Expected Output:
# - The Nginx version should be 1.27.
# - The custom index page should display when accessing http://localhost:8081.
```

Access the application in your browser http://localhost:8081

---

## Step 3: Stop and Remove Containers and Images

```bash
# Stop and remove the containers
docker rm -f my-arg-demo1
docker rm -f my-arg-demo2

# Remove the Docker images from local machine
docker rmi demo7-dockerfile-arg:v1.26
docker rmi demo7-dockerfile-arg:v1.27
```

---

## Conclusion

You have successfully:

- Created a Dockerfile with `ARG` for build-time variables.
- Built images using default and custom `ARG` values (`--build-arg`).
- Ran containers and verified Nginx versions.

---
