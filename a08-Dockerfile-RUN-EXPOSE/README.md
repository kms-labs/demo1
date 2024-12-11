# Learn with EXPOSE and RUN

---

## Introduction

In this guide, you will:
- Create an Nginx Dockerfile with `EXPOSE` and `RUN`.
- Add Nginx configs and HTML files for ports `8081`, `8082`, and `8083`.
- Install `curl` using `RUN`.
- Build the image and verify `RUN` and `EXPOSE`.

---

## Step 1: Application Files

### Nginx Configuration Files

- **Directory:** `Dockerfiles/nginx-conf`

**`nginx-8081.conf`:**

```conf
server {
    listen 8081;
    server_name localhost;

    location / {
        root /usr/share/nginx/html;
        index index-8081.html;
    }
}
```

**`nginx-8082.conf`:**

```conf
server {
    listen 8082;
    server_name localhost;

    location / {
        root /usr/share/nginx/html;
        index index-8082.html;
    }
}
```

**`nginx-8083.conf`:**

```conf
server {
    listen 8083;
    server_name localhost;

    location / {
        root /usr/share/nginx/html;
        index index-8083.html;
    }
}
```

### Nginx HTML Files

- **Directory:** `Dockerfiles/nginx-html`

**`index-8081.html`:**

```html
<!DOCTYPE html>
<html>
  <body style='background-color:rgb(135, 215, 159);'>
    <h1>Welcome to KMS-Healthcare Demo - RUN, EXPOSE Dockerfile Instructions</h1>
    <h2>Response from Nginx on port 8081</h2>
    <p>Learn technology through practical, real-world demos.</p>
    <p>Application Version: V1</p>
    <p>EXPOSE: Describe which ports your application is listening on.</p>
    <p>RUN: Execute build commands.</p>
  </body>
</html>
```

**`index-8082.html`:**

```html
<!DOCTYPE html>
<html>
  <body style='background-color:rgb(210, 153, 152);'>
    <h1>Welcome to KMS-Healthcare Demo - RUN, EXPOSE Dockerfile Instructions</h1>
    <h2>Response from Nginx on port 8082</h2>
    <p>Learn technology through practical, real-world demos.</p>
    <p>Application Version: V1</p>
    <p>EXPOSE: Describe which ports your application is listening on.</p>
    <p>RUN: Execute build commands.</p>
  </body>
</html>
```

**`index-8083.html`:**

```html
<!DOCTYPE html>
<html>
  <body style='background-color:rgb(227, 213, 180);'>
    <h1>Welcome to KMS-Healthcare Demo - RUN, EXPOSE Dockerfile Instructions</h1>
    <h2>Response from Nginx on port 8083</h2>
    <p>Learn technology through practical, real-world demos.</p>
    <p>Application Version: V1</p>
    <p>EXPOSE: Describe which ports your application is listening on.</p>
    <p>RUN: Execute build commands.</p>
  </body>
</html>
```

**`index.html`:**

```html
<!DOCTYPE html>
<html>
  <body style='background-color:rgb(157, 182, 216);'>
    <h1>Welcome to KMS-Healthcare Demo - RUN, EXPOSE Dockerfile Instructions</h1>
    <h2>Response from Nginx on port 80</h2>
    <p>Learn technology through practical, real-world demos.</p>
    <p>Application Version: V1</p>
    <p>EXPOSE: Describe which ports your application is listening on.</p>
    <p>RUN: Execute build commands.</p>
  </body>
</html>
```

---

## Step 2: Create Dockerfile

- **Directory:** `Dockerfiles`

Create a `Dockerfile` with the following content:

```dockerfile
# Use nginx:alpine-slim as base Docker Image
FROM nginx:alpine-slim

# OCI Labels
LABEL org.opencontainers.image.authors="KMS Healthcare"
LABEL org.opencontainers.image.title="Demo: Using RUN and EXPOSE Instructions in Dockerfile"
LABEL org.opencontainers.image.description="A Dockerfile demo illustrating the usage of RUN and EXPOSE instructions"
LABEL org.opencontainers.image.version="1.0"

# Copy all Nginx configuration files from nginx-conf directory
COPY nginx-conf/*.conf /etc/nginx/conf.d/

# Copy all HTML files from nginx-html directory
COPY nginx-html/*.html /usr/share/nginx/html/

# Install curl using RUN
RUN apk --no-cache add curl

# Expose the ports 8081, 8082, 8083 (default port 80 already exposed from base nginx image)
EXPOSE 8081 8082 8083
```

---

## Step 3: Build Docker Image and Run It

```bash
# Change directory
cd $(git rev-parse --show-toplevel)/a08-Dockerfile-RUN-EXPOSE/Dockerfiles

# Build the Docker image
docker build -t demo8-dockerfile-expose-run:v1 .

# Run Docker Container and Map Ports
docker run --name my-expose-run-demo -p 8080:80 -p 8081:8081 -p 8082:8082 -p 8083:8083 -d demo8-dockerfile-expose-run:v1

# List Configuration Files from Docker Container
docker exec -it my-expose-run-demo ls /etc/nginx/conf.d

# List HTML Files from Docker Container
docker exec -it my-expose-run-demo ls /usr/share/nginx/html

# Connect to Container Shell
docker exec -it my-expose-run-demo /bin/sh
```

```bash
# Commands to Run inside the Container
curl http://localhost
curl http://localhost:8081
curl http://localhost:8082
curl http://localhost:8083

# Exit the Container Shell
exit

# Observations:
# 1. The `curl` commands inside the Docker container work, which means the `RUN` instruction to install `curl` was successful.
# 2. All Nginx listening ports are accessible inside the Docker container.
```
Access application in browser:
- http://localhost:8080
- http://localhost:8081
- http://localhost:8082
- http://localhost:8083

---

## Step 4: Stop and Remove Container and Images

```bash
# Stop and Remove the Container
docker rm -f my-expose-run-demo

# Remove the Docker Images
docker rmi demo8-dockerfile-expose-run:v1
```

---

## Conclusion

You have successfully:
- Created an Nginx Dockerfile with `EXPOSE` and `RUN`.
- Configured Nginx for multiple ports with custom files.
- Served different HTML pages on each port.
- Installed `curl` in the image using `RUN`.
- Built, ran, and verified the image.

---

## Additional Notes

- **EXPOSE:** Declares container listening ports; use `-p` or `-P` with `docker run` to publish them.
- **RUN:** Executes commands during build, often for installing packages or custom configurations.

---
