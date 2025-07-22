# Docker: A Comprehensive Guide

This document provides a detailed explanation of Docker, including key terminologies, essential commands with explanations, and a practical example to demonstrate Docker usage.

## 1. Docker Terminologies

### Docker
Docker is an open-source platform that uses containerization to package applications and their dependencies into portable, lightweight containers. Containers run consistently across different environments, simplifying development, testing, and deployment.

### Key Terms
- **Container**: A lightweight, standalone executable package that includes an application and its dependencies (e.g., libraries, runtime). Containers share the host OS kernel, making them more efficient than virtual machines.
- **Image**: A read-only template used to create containers. Images are built from a series of layers (instructions) defined in a `Dockerfile`. Example: `nginx:latest` is an image for the Nginx web server.
- **Dockerfile**: A text file containing instructions to build a Docker image. It specifies the base image, application code, dependencies, and runtime configurations.
- **Registry**: A storage and distribution system for Docker images. Docker Hub is the default public registry, but private registries (e.g., AWS ECR) are also common.
- **Repository**: A collection of Docker images with the same name but different tags (e.g., `nginx:latest`, `nginx:1.21`). Repositories are stored in registries.
- **Container Orchestration**: Tools like Docker Compose or Kubernetes that manage multiple containers, handling tasks like scaling, networking, and load balancing.
- **Docker Daemon**: The background service (`dockerd`) that manages Docker objects like images, containers, and volumes on the host machine.
- **Docker Client**: The command-line interface (`docker`) that interacts with the Docker daemon to execute commands.

## 2. Essential Docker Commands and Explanations

Below are key Docker commands, their syntax, and explanations of their functionality.

### Image Management
- **`docker pull <image>:<tag>`**
  - **Explanation**: Downloads an image from a registry (e.g., Docker Hub) to your local machine.
  - **Example**:
    ```bash
    docker pull nginx:latest
    ```
    Pulls the latest Nginx image.

- **`docker images`**
  - **Explanation**: Lists all images stored locally, showing their repository, tag, ID, and size.
  - **Example**:
    ```bash
    docker images
    ```
    Output:
    ```
    REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
    nginx        latest    605c77e624dd   2 weeks ago   141MB
    ```

- **`docker build -t <image-name>:<tag> .`**
  - **Explanation**: Builds an image from a `Dockerfile` in the current directory (`.`). The `-t` flag assigns a name and tag to the image.
  - **Example**:
    ```bash
    docker build -t my-app:1.0 .
    ```
    Builds an image named `my-app:1.0`.

### Container Management
- **`docker run [options] <image>:<tag>`**
  - **Explanation**: Creates and starts a container from an image. Common options include:
    - `-d`: Run in detached mode (background).
    - `-p <host-port>:<container-port>`: Map ports between host and container.
    - `--name <name>`: Assign a name to the container.
  - **Example**:
    ```bash
    docker run -d -p 8080:80 --name my-nginx nginx:latest
    ```
    Runs an Nginx container in the background, mapping port 8080 (host) to 80 (container).

- **`docker ps`**
  - **Explanation**: Lists running containers. Use `docker ps -a` to include stopped containers.
  - **Example**:
    ```bash
    docker ps
    ```
    Output:
    ```
    CONTAINER ID   IMAGE          COMMAND                  NAMES
    a1b2c3d4e5f6   nginx:latest   "/docker-entrypoint.…"   my-nginx
    ```

- **`docker stop <container-id/name>`**
  - **Explanation**: Stops a running container gracefully.
  - **Example**:
    ```bash
    docker stop my-nginx
    ```
    Stops the `my-nginx` container.

- **`docker rm <container-id/name>`**
  - **Explanation**: Removes a stopped container.
  - **Example**:
    ```bash
    docker rm my-nginx
    ```
    Deletes the `my-nginx` container.

- **`docker exec -it <container-id/name> <command>`**
  - **Explanation**: Runs a command inside a running container. The `-it` flags enable interactive mode with a terminal.
  - **Example**:
    ```bash
    docker exec -it my-nginx bash
    ```
    Opens a bash shell inside the `my-nginx` container.

### Volume and Network Management
- **`docker volume create <volume-name>`**
  - **Explanation**: Creates a volume for persistent data storage, independent of the container lifecycle.
  - **Example**:
    ```bash
    docker volume create my-data
    ```

- **`docker run -v <volume-name>:<container-path>`**
  - **Explanation**: Mounts a volume to a path inside the container for persistent storage.
  - **Example**:
    ```bash
    docker run -v my-data:/app/data my-app:1.0
    ```
    Mounts `my-data` volume to `/app/data` in the container.

- **`docker network ls`**
  - **Explanation**: Lists available Docker networks (e.g., bridge, host).
  - **Example**:
    ```bash
    docker network ls
    ```

### Cleanup
- **`docker system prune`**
  - **Explanation**: Removes unused containers, networks, and dangling images to free up space.
  - **Example**:
    ```bash
    docker system prune -a
    ```
    Removes all unused resources, including images not used by any container.

- **`docker rmi <image-id/name>`**
  - **Explanation**: Deletes a specific image from the local machine.
  - **Example**:
    ```bash
    docker rmi nginx:latest
    ```

## 3. Example: Running a Python Web App with Docker

This example demonstrates how to create, build, and run a simple Python Flask web application using Docker.

### Step 1: Create Project Files
Create a directory named `my-flask-app` with the following structure:
```
my-flask-app/
├── Dockerfile
├── app.py
└── requirements.txt
```

#### `Dockerfile`
```dockerfile
# Use official Python image as base
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy requirements file
COPY requirements.txt .

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY app.py .

# Expose port 5000
EXPOSE 5000

# Command to run the app
CMD ["python", "app.py"]
```

#### `requirements.txt`
```text
flask==2.0.1
```

#### `app.py`
```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return 'Hello, Docker!'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

### Step 2: Build the Docker Image
Navigate to the `my-flask-app` directory and build the image:
```bash
cd my-flask-app
docker build -t my-flask-app:1.0 .
```

### Step 3: Run the Container
Run the container, mapping port 5000 on the host to 5000 in the container:
```bash
docker run -d -p 5000:5000 --name flask-container my-flask-app:1.0
```

### Step 4: Test the Application
Open a browser and visit `http://localhost:5000`. You should see:
```
Hello, Docker!
```

### Step 5: Stop and Clean Up
Stop and remove the container:
```bash
docker stop flask-container
docker rm flask-container
```

Remove the image (if no longer needed):
```bash
docker rmi my-flask-app:1.0
```

## 4. Additional Notes
- **Best Practices**:
  - Use specific image tags (e.g., `python:3.9-slim`) instead of `latest` for reproducibility.
  - Minimize image size by using lightweight base images and cleaning up unnecessary files in the `Dockerfile`.
  - Use `.dockerignore` to exclude unnecessary files (e.g., `.git`, `node_modules`) from the build context.
- **Docker Compose**: For multi-container applications, use `docker-compose.yml` to define and manage services, networks, and volumes.
- **Security**: Avoid running containers as root (`USER` in `Dockerfile`) and scan images for vulnerabilities using tools like `docker scan`.

This guide covers the essentials of Docker, providing a foundation for containerizing applications and managing them effectively.