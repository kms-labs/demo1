# Learn with container Ports

---

## Introduction

In this guide, you will:

1. Use Docker container ports with different flags.
2. Learn `-p` vs. `-P` for container ports.
3. Publish specific and ephemeral ports.
4. Manage multi-port containers.

---

## Demo 1: Understanding Docker Container Ports with `-p` Flag

### Step 1: Create Dockerfile and Custom `index.html`

- **Directory:** `01-DockerFiles-Single-Port`

**Create a `Dockerfile`:**

```dockerfile
FROM nginx:alpine-slim
COPY index.html /usr/share/nginx/html
```

**Create `index.html`:**

```html
<!DOCTYPE html>
<html>
  <body style='background-color:rgb(206, 141, 147);'>
    <h1>Welcome to KMS-Healthcare Demo - Docker Ports HOST_PORT, CONTAINER_PORT</h1>
    <p>Learn technology through practical, real-world demos.</p>
    <p>Application Version: V1</p>
  </body>
</html>
```

### Step 2: Build Docker Image - Nginx Single Port

```bash
# Change directory
cd $(git rev-parse --show-toplevel)/a14-Docker-PORTS/01-DockerFiles-Single-Port

# Build the Docker image
docker build -t demo14-docker-singleport:v1 .
```

### Step 3: Publishing Specific Host Port

```bash
# Run Docker Container with specific host port
docker run --name my-ports-demo1 -p 8090:80 -d demo14-docker-singleport:v1

# List Docker Containers
docker ps --format "table {{.Image}}\t{{.Names}}\t{{.Status}}\t{{.ID}}\t{{.Ports}}"

# Observation:
# - The container's port 80 is mapped to host port 8090.
# - You can access the Nginx server on `localhost:8090`.
```

Access the application in your browser http://localhost:8090

### Step 4: Publishing Ephemeral Ports

```bash
# Run Docker Container with ephemeral host port
docker run --name my-ports-demo2 -p 80 -d demo14-docker-singleport:v1

# List Docker Containers
docker ps --format "table {{.Image}}\t{{.Names}}\t{{.Status}}\t{{.ID}}\t{{.Ports}}"

# Example Output:
# IMAGE                       NAMES            STATUS         CONTAINER ID   PORTS
# demo14-docker-singleport:v1   my-ports-demo2   Up 10 seconds   abcdef123456   0.0.0.0:XXXXX->80/tcp

# Observation:
# - Docker assigns a random ephemeral port on the host, mapped to container port 80.
# - Replace `XXXXX` with the actual port number displayed in `docker ps`.
```

Access the application using browser http://localhost:XXXXX

---

## Demo 2: Multi-Port Nginx with `-P` Flag

### Step 1: Create Dockerfile for Multi-Port Nginx

- **Directory:** `02-DockerFiles-Multi-Port`

**Create a `Dockerfile`:**

```dockerfile
# Use nginx:alpine-slim as base Docker Image
FROM nginx:alpine-slim

# Set environment variables for configuration
ENV NGINX_PORT1=8080
ENV NGINX_PORT2=8081

# OCI Labels
LABEL org.opencontainers.image.authors="KMS Healthcare"
LABEL org.opencontainers.image.title="Demo: Docker Ports usage"
LABEL org.opencontainers.image.description="A Dockerfile demo illustrating the use of Docker Ports"
LABEL org.opencontainers.image.version="1.0"

# Custom Labels
LABEL nginx_port1=${NGINX_PORT1}
LABEL nginx_port2=${NGINX_PORT2}

# Install curl
RUN apk --no-cache add curl

# Create directories for serving content
RUN mkdir -p /usr/share/nginx/html/app1 /usr/share/nginx/html/app2

# Copy content to the respective directories
COPY app1/index.html /usr/share/nginx/html/app1
COPY app2/index.html /usr/share/nginx/html/app2
COPY index.html /usr/share/nginx/html

# Copy custom NGINX configuration file
COPY my_custom_nginx.conf /etc/nginx/conf.d/my_custom_nginx.conf

# Expose ports
EXPOSE $NGINX_PORT1 $NGINX_PORT2 80
```

### Step 2: Create Custom Nginx Configuration

**Create `my_custom_nginx.conf`:**

```conf
server {
    listen 8080;
    server_name localhost;

    location / {
        root /usr/share/nginx/html/app1;
        index index.html;
    }

    error_page 404 /404.html;
    location = /404.html {
        root /usr/share/nginx/html/app1;
    }
}

server {
    listen 8081;
    server_name localhost;

    location / {
        root /usr/share/nginx/html/app2;
        index index.html;
    }

    error_page 404 /404.html;
    location = /404.html {
        root /usr/share/nginx/html/app2;
    }
}
```

### Step 3: Create Static Files

**Create `app1/index.html`:**

```html
<!DOCTYPE html>
<html>
  <body style='background-color:rgb(193, 136, 209);'>
    <h1>Welcome to KMS-Healthcare Demo - MultiPort - App1 on Port 8080</h1>
    <p>Learn technology through practical, real-world demos.</p>
    <p>Application Version: V1</p>
  </body>
</html>
```

**Create `app2/index.html`:**

```html
<!DOCTYPE html>
<html>
  <body style='background-color:rgb(136, 193, 209);'>
    <h1>Welcome to KMS-Healthcare Demo - MultiPort - App2 on Port 8081</h1>
    <p>Learn technology through practical, real-world demos.</p>
    <p>Application Version: V1</p>
  </body>
</html>
```

**Create `index.html`:**

```html
<!DOCTYPE html>
<html>
  <body style='background-color:rgb(209, 193, 136);'>
    <h1>Welcome to KMS-Healthcare Demo - MultiPort - App3 on Port 80 from Nginx default.conf</h1>
    <p>Learn technology through practical, real-world demos.</p>
    <p>Application Version: V1</p>
  </body>
</html>
```

### Step 4: Build Docker Image - Nginx Multi Port

```bash
# Change directory
cd $(git rev-parse --show-toplevel)/a14-Docker-PORTS/02-DockerFiles-Multi-Port

# Build the Docker image
docker build -t demo14-docker-multiport:v1 .
```

### Step 5: Publishing All Ports with `-P` Flag

```bash
# Run Docker Container with all ports published
docker run --name my-ports-demo3 -P -d demo14-docker-multiport:v1

# List Docker Containers
docker ps --format "table {{.Image}}\t{{.Names}}\t{{.Status}}\t{{.ID}}\t{{.Ports}}"

# Example Output:
# IMAGE                       NAMES            STATUS         CONTAINER ID   PORTS
# demo14-docker-multiport:v1   my-ports-demo3   Up 10 seconds   abcdef123456   0.0.0.0:XXXXX->80/tcp, 0.0.0.0:YYYYY->8080/tcp, 0.0.0.0:ZZZZZ->8081/tcp
```

```bash
# Access applications using browser
http://localhost:XXXXX   # App3 on port 80
http://localhost:YYYYY   # App1 on port 8080
http://localhost:ZZZZZ   # App2 on port 8081

# Access applications using curl
curl http://localhost:XXXXX
curl http://localhost:YYYYY
curl http://localhost:ZZZZZ
```

**Observation:**

- The `-P` flag publishes all exposed ports to random host ports.
- Replace `XXXXX`, `YYYYY`, and `ZZZZZ` with the actual port numbers displayed in `docker ps`.

**Verify Inside the Container:**

```bash
# Connect to the container
docker exec -it my-ports-demo3 /bin/sh

# Inside the container, run:
cd /etc/nginx/conf.d
ls
# Output should show 'my_custom_nginx.conf'

# Check listening ports
netstat -lntp

# Verify Nginx is listening on ports 80, 8080, and 8081

# Test applications internally
curl http://localhost
curl http://localhost:8080
curl http://localhost:8081

# Exit the container shell
exit
```

## Clean-Up

```bash
# Stop and remove all running containers
docker rm -f $(docker ps -aq -f "name=demo")

# Remove Docker Images
docker rmi demo14-docker-singleport:v1
docker rmi demo14-docker-multiport:v1
```

---

## Conclusion

You have successfully:

- Used Docker container ports with various flags.
- Mapped specific host ports and assigned ephemeral ports with `-p`.
- Published all exposed ports dynamically with `-P`.
- Built and ran single-port and multi-port applications.

---

## Additional Notes

- **`-p` or `--publish` Flag:**
  - Maps a container port to a specific host port.
  - Syntax: `-p [host_ip:]host_port:container_port`.

- **Ephemeral Ports:**
  - Docker assigns a random high port on the host to map to the container port with `-p container_port`.

- **`-P` or `--publish-all` Flag:**
  - Publishes all exposed ports to random host ports.
  - Useful for testing or avoiding port conflicts.

- **Exposing Ports in Dockerfile:**
  - `EXPOSE` informs Docker of runtime ports, but does not publish them; use `-p` or `-P` with `docker run`.

---
