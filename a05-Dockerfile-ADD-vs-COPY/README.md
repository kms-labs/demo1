# Learn with ADD vs COPY

---

## Introduction

In this guide, you will:

- Create an Nginx Dockerfile using `nginx:alpine-slim`.
- Add labels and use `COPY`/`ADD` instructions.
- Build the Docker image.
---

## Step 1: Review App-Files Folder and Tar the Files

```bash
# Change directory
cd $(git rev-parse --show-toplevel)/a05-Dockerfile-ADD-vs-COPY/App-Files

# Create a tar.gz archive of the files
tar -czvf static_files.tar.gz index.html file1.html file2.html file3.html file4.html file5.html

# Move the tar.gz file to the Dockerfiles directory
mv static_files.tar.gz ../Dockerfiles
```

---

## Step 2: Create Dockerfile and Copy Customized Files

- **Folder:** Dockerfiles

Create a `Dockerfile` with the following content:

```dockerfile
# Use nginx:alpine-slim as base Docker Image
FROM nginx:alpine-slim

# OCI Labels
LABEL org.opencontainers.image.authors="KMS Healthcare"
LABEL org.opencontainers.image.title="Demo: COPY vs ADD Instructions in Dockerfile"
LABEL org.opencontainers.image.description="A Dockerfile demo illustrating the differences between COPY and ADD instructions, including copying files and extracting tarballs."
LABEL org.opencontainers.image.version="1.0"

# Using COPY to copy a local file
COPY copy-file.html /usr/share/nginx/html

# Using ADD to copy a file and extract a tarball
ADD static_files.tar.gz /usr/share/nginx/html
```

---

## Step 3: Build Docker Image and Run It

```bash
# List Static Files from Docker Image
docker run --rm -i nginx:alpine-slim ls -laR /usr/share/nginx/htm
```

```bash
# Change directory
cd $(git rev-parse --show-toplevel)/a05-Dockerfile-ADD-vs-COPY/Dockerfiles

# Build the Docker image
docker build -t demo5-dockerfile-add-vs-copy:v1 .

# Run Docker Container and Verify
docker run --name my-add-vs-copy-demo -p 8080:80 -d demo5-dockerfile-add-vs-copy:v1

# List Static Files from Docker Container
docker exec -it my-add-vs-copy-demo ls -lrta /usr/share/nginx/html
```

Access Application http://localhost:8080

---

## Step 4: Stop and Remove Container and Images

```bash
# Stop and remove the container
docker rm -f my-add-vs-copy-demo

# Remove the Docker images
docker rmi demo5-dockerfile-add-vs-copy:v1
```

---

## COPY vs ADD in Dockerfile

Both commands copy files from the host to a Docker image, but they differ in behavior and use cases:

**COPY**
- Transfers files/directories from the build context.
- Simple and fast; ideal for local files.

**ADD**
- Includes all `COPY` functionality plus:
  - Auto-extracts `tar` archives.
  - Supports fetching files from URLs.
- Versatile but may introduce security risks and unexpected behavior.

### Best Practice:
- Use `COPY` whenever possible for local files.
- Reserve `ADD` for cases where you need to extract a tarball or download from a URL.

### Comparison Table:

| Feature                   | `COPY`                                   | `ADD`                                          |
|---------------------------|------------------------------------------|------------------------------------------------|
| **File Transfer**         | Copies files from build context          | Copies files from build context                |
| **Tar Archive Extraction**| No                                       | Yes (automatically extracts `.tar` files)      |
| **URL Support**           | No                                       | Yes (can fetch files from the web)             |
| **Performance**           | Faster, less overhead                    | Can be slower if using additional features     |
| **Security**              | Safer for local file transfers           | Can introduce risks when fetching from URLs    |
| **Use Case**              | Preferred for all local file copies      | Use only for tar extraction or URL fetching    |

---

## Conclusion

You successfully:

- Created a Dockerfile with `nginx:alpine-slim` as the base image.
- Used `COPY` for file/directory transfers and `ADD` for additional features like extracting files and handling URLs.

---
