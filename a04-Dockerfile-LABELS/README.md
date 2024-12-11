# Learn with LABEL and `docker inspect`

---

## Introduction

In this guide, you will:

- Add labels to a Docker image.
- Build the image.
- Explore the `docker inspect` command.

---

## Step 1: Create Dockerfile and Customized `index.html`

- **Base Image:** [Nginx Alpine Slim](https://hub.docker.com/_/nginx/tags?page_size=&ordering=&name=alpine-slim)
- **Directory:** `Dockerfiles`

**Create a `Dockerfile`:**

```dockerfile
# Use nginx:alpine-slim as base Docker Image
FROM nginx:alpine-slim

# Custom Labels
LABEL maintainer="KMS Healthcare"
LABEL version="1.0"
LABEL description="A simple Nginx Application"
# OCI Labels
LABEL org.opencontainers.image.authors="KMS Healthcare"
LABEL org.opencontainers.image.title="Nginx Alpine Slim Application"
LABEL org.opencontainers.image.description="A lightweight Nginx application built on Alpine."
LABEL org.opencontainers.image.version="1.0"
LABEL org.opencontainers.image.revision="1234567890abcdef"
LABEL org.opencontainers.image.vendor="KMS-Healthcare Demo"
LABEL org.opencontainers.image.licenses="Apache-2.0"

# Using COPY to copy a local file
COPY index.html /usr/share/nginx/html
```

**Create a simple `index.html`:**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>KMS-Healthcare Demo</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      text-align: center;
      padding: 50px;
      background-color: rgb(227, 213, 180);
    }
    h1 { font-size: 50px; }
    h2 { font-size: 40px; }
    h3 { font-size: 30px; }
    p { font-size: 20px; }
  </style>
</head>
<body>
  <h1>Welcome to KMS-Healthcare Demo</h1>
  <h2>Dockerfile: Nginx Alpine Slim Docker Image with custom LABELS and OCI LABELS</h2>
  <p>Learn technology through practical, real-world demos.</p>
  <p>Application Version: V1</p>
</body>
</html>
```

---

## Step 2: Build Docker Image and Run It

```bash
# Change directory
cd $(git rev-parse --show-toplevel)/a04-Dockerfile-LABELS/Dockerfiles

# Build the Docker image
docker build -t demo4-dockerfile-labels:v1 .

# Run the Docker container
docker run --name mylabels-demo -p 8080:80 -d demo4-dockerfile-labels:v1
```

Access the application in your browser http://localhost:8080

---

<details>
<summary><h2>Step 3: Install jq Package</h2></summary>

`jq` is a lightweight and flexible command-line JSON processor, useful for parsing JSON output from commands like `docker inspect`.

**For macOS:**

```bash
brew install jq
jq --version
```

**For Linux (Ubuntu/Debian):**

```bash
sudo apt-get update
sudo apt-get install jq
jq --version
```

**For Linux (CentOS/RHEL):**

```bash
sudo yum install epel-release
sudo yum install jq
jq --version
```

**For Linux (Fedora):**

```bash
sudo dnf install jq
jq --version
```

**For Other Linux Distributions:**

```bash
wget -O jq https://github.com/jqlang/jq/releases/download/jq-1.7.1/jq-linux-amd64
chmod +x ./jq
sudo mv jq /usr/local/bin
jq --version
```

**For Windows (Using Chocolatey):**

```bash
choco install jq
jq --version
```

**For Windows (Manual Install):**

1. Download the executable from [jq Releases](https://github.com/stedolan/jq/releases).
2. Choose `jq-win64.exe` (or `jq-win32.exe` for 32-bit systems).
3. Rename the downloaded file to `jq.exe`.
4. Move it to a folder in your system's PATH (e.g., `C:\Windows`).
</details>

---

## Step 4: Docker Image Inspect Commands

Use `docker inspect` to retrieve detailed information about Docker images.

```bash
# Inspect the Docker image
docker image inspect demo4-dockerfile-labels:v1

# Get the creation date of the Docker image
docker inspect --format='{{.Created}}' demo4-dockerfile-labels:v1

# Get the Docker image labels (unformatted)
docker image inspect --format='{{json .Config.Labels}}' demo4-dockerfile-labels:v1

# Get the Docker image labels (formatted with jq)
docker image inspect --format='{{json .Config.Labels}}' demo4-dockerfile-labels:v1 | jq
```

---

## Step 5: Docker Container Inspect Commands

Use `docker inspect` to retrieve detailed information about Docker containers.

```bash
# Inspect the Docker container
docker inspect mylabels-demo

# Get the IP address of the container
docker inspect --format='{{.NetworkSettings.IPAddress}}' mylabels-demo

# Inspect container state (running, paused, stopped)
docker inspect --format='{{.State.Status}}' mylabels-demo

# Inspect exposed ports
docker inspect --format='{{json .Config.ExposedPorts}}' mylabels-demo

# Inspect network details of the container (formatted with jq)
docker inspect --format='{{json .NetworkSettings}}' mylabels-demo | jq
```

---

## Step 6: Stop and Remove Container and Images

```bash
# Stop and remove the container
docker rm -f mylabels-demo

# Remove the Docker images
docker rmi demo4-dockerfile-labels:v1
```

---

## Conclusion

You have successfully:

- Created a Dockerfile with labels using `nginx:alpine-slim`.
- Built and ran a labeled Docker image.
- Used `docker inspect` to retrieve image/container details.

---
