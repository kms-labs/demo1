# Learn with ENTRYPOINT

---

## Introduction

In this guide, you will:

- Create a Dockerfile with `ENTRYPOINT`.
- Build and test a Docker image.
- Learn to:
  - Use and modify `ENTRYPOINT` in the Dockerfile.
  - Append arguments to `ENTRYPOINT`.
  - Override it with the `--entrypoint` flag.

---

## Step 1: Create Dockerfile

- **Directory:** `Dockerfiles`

Create a `Dockerfile` with the following content:

```dockerfile
# Use ubuntu as base Docker Image
FROM ubuntu

# OCI Labels
LABEL org.opencontainers.image.authors="KMS Healthcare"
LABEL org.opencontainers.image.title="Demo: ENTRYPOINT Instruction in Docker"
LABEL org.opencontainers.image.description="A Dockerfile demo illustrating the use of the ENTRYPOINT instruction"
LABEL org.opencontainers.image.version="1.0"

# Always run the echo command as the entrypoint
ENTRYPOINT ["echo", "KMS"]
```

---

## Step 2: Build Docker Image and Run It

### Build the Docker Image

```bash
# Change directory
cd $(git rev-parse --show-toplevel)/a11-Dockerfile-ENTRYPOINT/Dockerfiles

# Build the Docker image
docker build -t demo11-dockerfile-entrypoint:v1 .
```

### Demo 1: Use ENTRYPOINT As-Is

```bash
# Run Docker Container and Verify
docker run --name my-entrypoint-demo1 demo11-dockerfile-entrypoint:v1

# Expected Output:
# KMS

# Observation:
# - The container runs and outputs `KMS`, which is the argument provided in the `ENTRYPOINT` instruction during the Docker image build.
```

### Demo 2: Append Arguments to ENTRYPOINT

```bash
# Run Docker Container and append an additional argument
docker run --name my-entrypoint-demo2 demo11-dockerfile-entrypoint:v1 Healthcare

# Expected Output:
# KMS Healthcare

# Observation:
# - The additional argument `Healthcare` is appended to the `ENTRYPOINT` instruction.
# - The container outputs `KMS Healthcare`.
```

### Demo 3: Override ENTRYPOINT

```bash
# Run Docker Container and override the ENTRYPOINT instruction
docker run --name my-entrypoint-demo3 --entrypoint /bin/sh demo11-dockerfile-entrypoint:v1 -c 'echo "Overridden ENTRYPOINT instruction by KMS Healthcare!"'

# Expected Output:
# Overridden ENTRYPOINT instruction by KMS Healthcare!

# Observation:
# - The `--entrypoint` flag overrides the `ENTRYPOINT` instruction specified in the Dockerfile.
# - The container runs `/bin/sh` with the `-c` option, executing the `echo` command.
# - The container outputs `Overridden ENTRYPOINT instruction by KMS Healthcare!`
```

---

## Step 3: Stop and Remove Containers and Images

```bash
# Stop and Remove the Containers
docker rm -f my-entrypoint-demo1
docker rm -f my-entrypoint-demo2
docker rm -f my-entrypoint-demo3

# Remove the Docker Images
docker rmi demo11-dockerfile-entrypoint:v1
```

---

## Conclusion

You have successfully:

- Created a Dockerfile with `ENTRYPOINT`.
- Built and tested a Docker image.
- Used `ENTRYPOINT` with appended arguments.
- Overridden `ENTRYPOINT` with the `--entrypoint` flag.

---

## Additional Notes

**ENTRYPOINT:**
- Configures a container to run as an executable, ideal for specific commands or apps.

**Appending Arguments:**
- Arguments added with `docker run` are appended to `ENTRYPOINT`.

**Overriding ENTRYPOINT:**
- Use `--entrypoint` in `docker run` to override `ENTRYPOINT`.

**Best Practices:**
- Use `ENTRYPOINT` for defining a container's executable.
- Use `CMD` for default arguments to `ENTRYPOINT`.

---
