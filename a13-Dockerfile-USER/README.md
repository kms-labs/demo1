# Learn with USER

---

## Introduction

In this guide, you will:

- Create a Flask app displaying the current user and group.
- Build a Dockerfile using `USER` to run as a non-root user.
- Build and verify the image runs under the non-root user.

---

## Step 1: Create Sample Python Application and Dockerfile

### Create a Sample Python App

**File Name:** `app.py`

```python
from flask import Flask
import os
import pwd
import grp

app = Flask(__name__)

@app.route('/')
def hello_world():
    # Get the current user's ID and name
    user_id = os.getuid()
    user_name = pwd.getpwuid(user_id).pw_name

    # Get the current group's ID and name
    group_id = os.getgid()
    group_name = grp.getgrgid(group_id).gr_name

    # Return a response displaying both the user and the group
    return f'Hello from user: {user_name} (UID: {user_id}) and group: {group_name} (GID: {group_id})!'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

This Flask application will display the current user and group when accessed.

### Create Dockerfile with USER Instruction

**Dockerfile**

```dockerfile
# Use the official Python image as the base image
# This image comes with Python pre-installed
FROM python

# Set the working directory inside the container to /usr/src/app
# All subsequent commands will be run from this directory
WORKDIR /usr/src/app

# Copy the contents of the current directory on the host (where the Dockerfile is located) to /usr/src/app in the container
# using pattern matching COPY command
COPY *.py .

# Install the Flask package using pip
# The --no-cache-dir option ensures no cache is used, reducing the image size
RUN pip install --no-cache-dir flask

# Explicitly set the USER environment variable for the non-root user
ENV USERNAME=mypythonuser
ENV USER_UID=1001
ENV USER_GID=1001

# Create a new group called 'mypythonuser' and a non-root user 'mypythonuser' within this group
RUN groupadd --gid ${USER_GID} ${USERNAME} \
    && useradd --uid $USER_UID --gid ${USER_GID} --create-home ${USERNAME}

# Set ownership of the /usr/src/app directory to the non-root user 'mypythonuser'
# This ensures that 'mypythonuser' has the necessary permissions to access the app directory
RUN chown -R ${USERNAME}:${USERNAME} /usr/src/app

# Switch to the non-root user 'mypythonuser' so that the application does not run as root
USER ${USERNAME}

# Command to run the Python application
# This command starts the Flask app when the container starts
CMD ["python", "app.py"]

# Expose port 5000 to the host, so the Flask app is accessible externally
EXPOSE 5000
```

**Explanation:**

- `FROM python:alpine`: Uses the Python image.
- `WORKDIR /usr/src/app`: Sets the working directory inside the container.
- `COPY app.py .`: Copies the application code into the container.
- `RUN pip install --no-cache-dir flask`: Installs Flask without caching to keep the image size small.
- `ENV USER=mypythonuser` and `ENV GROUP=mypythongroup`: Sets environment variables for the user and group.
- `RUN addgroup -S ${GROUP} && adduser -S ${USER} -G ${GROUP}`: Creates a new system group and user.
- `RUN chown -R ${USER}:${GROUP} /usr/src/app`: Changes ownership of the application directory.
- `USER ${USER}`: Switches to the non-root user.
- `EXPOSE 5000`: Exposes port 5000 for the Flask app.
- `CMD ["python", "app.py"]`: Starts the Flask application.

---

## Step 2: Build Docker Image and Run It

```bash
# Change directory
cd $(git rev-parse --show-toplevel)/a13-Dockerfile-USER/Dockerfiles

# Build the Docker image
docker build -t demo13-dockerfile-user:v1 .

# Run the Docker Container
docker run --name my-user-demo -p 5000:5000 -d demo13-dockerfile-user:v1
```

Access the application in your browser http://localhost:5000

```
Expected Output in Browser:
Hello from user: mypythonuser (UID: 1001) and group: mypythonuser (GID: 1001)!
```

**Verify User and Group Inside the Container:**

```bash
# Connect to the container
docker exec -it my-user-demo /bin/bash
# Observation:
# You should see you have logged into container using non-root user "mypythonuser"

# Inside the container, list files and their ownership
ls -la
# Observation:
# app.py should have the user as mypythonuser and group as mypythongroup

# Expected Output:
# total 8
# -rw-r--r--    1 mypythonuser mypythongroup     629 Oct 13 12:00 app.py

# Check environment variables
env

# Look for USER and GROUP variables
# USER=mypythonuser
# GROUP=mypythongroup

# Exit the container shell
exit
```

---
## Step-3: How do you connect to container with root user?
```bash
# Connect to Container with Root User
docker exec --user root -it my-user-demo /bin/bash

# Note:
# - You will be logged in to Docker container with `root` user
```

---

## Step 4: Stop and Remove Container and Images

```bash
# Stop and remove the container
docker rm -f my-user-demo

# Remove the Docker images from local machine
docker rmi demo13-dockerfile-user:v1
```

---

## Conclusion

You have successfully:

- Created a Flask app showing the current user and group.
- Built a Dockerfile with `USER` to run as non-root.
- Verified the app runs under the non-root user.

---

## Additional Notes

**USER:**
- Sets the user/UID and optional group/GID for subsequent instructions in the Dockerfile.

**Security Best Practices:**
- Run apps as non-root to enhance security and limit potential damage.

**Environment Variables:**
- Set with `ENV`, available during build and runtime, accessible in app code or shell.

---
