# 🐳 Docker & Cloud Notes

Welcome to my comprehensive notes on Docker and Cloud setup. This guide covers the fundamentals of Docker, its core concepts, installation, and essential commands.

---

## 📖 Introduction to Docker

Docker is a platform that lets you package an application along with everything it needs to run—code, runtime, system libraries, and settings—into a single unit that runs consistently on any machine. It solves the classic *"it works on my machine"* problem. 

Before Docker, an app built on a local laptop might behave differently on a production server due to differences in OS versions, libraries, or configurations. Docker fixes this by bundling everything the app needs into an isolated package called a **container**, which is a lighter-weight alternative to running full virtual machines.

### 🖼️ Image vs. Container

* **Docker Image**: A read-only blueprint or template for your application. It contains the application code, a stripped-down OS layer, runtime (like Node.js or Python), libraries, and configuration files. Think of it like a recipe or a snapshot—it doesn't run by itself; it just describes what should exist.
* **Docker Container**: A running instance of an image. If the image is the recipe, the container is the actual dish you cooked from it. You can start, stop, move, or delete a container, and you can spin up multiple containers from the same image (e.g., 5 containers running the same web app image to handle more traffic).

---

## ☁️ Cloud Setup & Installation

### AWS EC2 Quick Setup
1. Launch an EC2 instance.
2. Configure the security group to allow all traffic (for testing purposes).
3. Select an instance profile (a role with SSM permission) so you don't have to manually verify keys.
4. Start the instance and connect using **Session Manager**.

### Installing Docker (Ubuntu)
You can find the official installation guide on the [Docker website](https://docs.docker.com/engine/install/ubuntu/).

```bash
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
```

---

## 💻 Essential Docker Commands

### System & General
* `sudo su` : Switch to superuser.
* `cd /` : Go to the root directory.
* `ping 8.8.8.8` : Check if the instance is connected to the internet.
* `docker version` / `docker -v` / `docker info` : Check Docker version and system info.
* `docker --help` : Display help documentation for Docker.

### Images
* `docker images` : List all images.
* `docker pull [image_name]` : Pull an image from Docker Hub (if not locally present).
* `docker pull --all-tags [image_name]` : Pull all versions (tags) of an image.
* `docker rmi [image_name]` : Delete an image.
* `docker image prune` : Remove unused Docker images to free up space.

### Containers
* `docker ps` : List running containers.
* `docker ps -a` : List all containers (including stopped).
* `docker create --name [name] [image_name]` : Create a container without starting it.
* `docker start [container_name]` : Start a stopped container.
* `docker stop [container_name]` : Stop a running container.
* `docker rm [container_name]` : Delete a container.
* `docker rm -f [container_name]` : Force remove a running container.
* `docker run --name [name] [image_name]` : Pull, create, and start a container in one step.
* `docker run -d --name [name] [image_name]` : Run the container in detached (background) mode.
* `docker run -p 80:80 [image_name]` : Map port 80 of the host to port 80 of the container.
* `ps aux | grep [container_name]` : View processes of a specific container.
* `docker inspect [container_or_image]` : Retrieve low-level info about containers/images in JSON format.
* `docker logs [container_name]` : Retrieve logs of a container.
* `docker logs -f [container_name]` : Display and follow live logs.

### Executing Inside Containers
* `docker exec [options] [container_name] [command]` : Execute a command inside a running container.
  * `-t` : Allocates a terminal to see output but no input window.
  * `-i` : Allocates an input window but no terminal to see output.
  * `-it` : Interactive mode (allocates both terminal and input). Mostly used.
* `docker exec -it [container_name] /bin/bash` : Opens an interactive terminal session inside the container (e.g., you become `root@container_id`). Type `exit` to return to your host terminal.

> **💡 Note on Container Ephemerality**
> Any changes you make inside a running container (e.g., `apt update`, installing packages, creating files) only exist in that container's writable layer. They are not saved into the image and will vanish if the container is removed or if a new container is spun up from the same image.

---

## 🏗️ Dockerfile Structure

A Dockerfile contains instructions that Docker reads line by line to automatically build an image. Each instruction creates a layer in the Docker image.

* **`FROM <image>[:tag]`** : Sets the base image (e.g., `FROM ubuntu`).
* **`RUN <command>`** : Runs one or more commands during the build. (e.g., `RUN apt-get update && apt-get install -y vim git`).
* **`CMD ["executable", "param1", "param2"]`** : Specifies the default command to execute when the container starts. (e.g., `CMD ["python", "app.py"]`). Only one CMD should exist; if multiple are present, only the last one takes effect.
* **`EXPOSE <port>`** : Informs Docker that the container listens on specified ports at runtime. Informational only (doesn't actually publish the port).
* **`COPY <src> <dest>`** : Copies files/directories from the build context (host) into the container's filesystem.
* **`WORKDIR <path>`** : Sets the working directory for subsequent instructions (`RUN`, `CMD`, `COPY`). Can be used multiple times.
* **`ENV <key> <value>`** : Sets environment variables (e.g., `ENV name=Saumya`).
* **`LABEL key="value"`** : Adds metadata to an image.

### Building an Image
```bash
docker build -t myapp .
```
* `-t` : Tags the resulting image with a name.
* `.` : Specifies the build context (current directory).

**Advanced Build Options:**
* `docker build -f files/test.txt -t image3 /imagefiles` : 
  Builds `image3` using `files/test.txt` as the Dockerfile and `/imagefiles` as the build context. The path for `-f` is relative to the build context unless absolute paths are used.

### Inspecting Images
* `docker history <image_name>` : Displays image layers and their details.

---

## 🚀 Publishing Images

Docker Hub is the default container registry. Images follow this naming convention:
`[registry/][username_or_org/]repository[:tag]`
*(For Docker Hub, the registry is implicitly `docker.io`)*

* **`docker login`** : Login to Docker Hub.
* **`docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]`** : Add a new name or alias to an image.
* **`docker push [image_name]`** : Push the tagged image to Docker Hub.

---

## 🌐 Docker Networking

A bridge is a data link layer device that forwards traffic between network segments. In Docker, a bridge network uses a software bridge to allow containers to communicate inside the Docker host. By default, a bridge (`docker0`) is created automatically.

### Network Modes:
1. **Bridge (default)** : Containers connected to the same bridge can communicate. Applies within the same Docker host.
2. **Host** : Removes network isolation between the container and the Docker host.
3. **None** : Completely isolates a container from the host and other containers.
4. **Overlay** : Connects multiple Docker daemons together (used in Swarms).

### Network Commands:
* `ip a` : Display network interfaces and their IP addresses on Linux.
* `docker network ls` : List all available networks.
* `docker network create <name>` : Create a new network.
* `docker network create --subnet 10.0.0.0/16 <name>` : Create a network with a custom subnet.
* `docker network inspect <network_name_or_id>` : Inspect a Docker network.
* `docker run --network <network_name> [image_name]` : Run a new container and connect it to a specific network.
* `docker network connect <network_name> <container_name>` : Connect a running container to a network.
* `docker network disconnect <network_name> <container_name>` : Disconnect a running container from a network.

---

## 📦 Docker Compose

**`docker-compose.yml`**
A YAML file that defines and runs multi-container Docker applications. It defines all the different containers (services), networks, and volumes we need to create in a single file, making it easy to orchestrate complex environments.

### Docker Compose Syntax and Elements
A typical `docker-compose.yml` file is structured with the following key components:

* **`version`**: (Optional in newer Compose specs, but traditionally used to specify the file format version, e.g., `'3.8'`).
* **`services`**: The core block where you define your containers. Each service acts as a separate container.
* **`image`**: Specifies the Docker image to use for the service.
* **`build`**: Instead of an image, tells Compose to build an image from a Dockerfile. You can specify the context (directory) and the Dockerfile name.
* **`ports`**: Maps host ports to container ports (Format: `"HOST:CONTAINER"`).
* **`environment`**: Sets environment variables inside the container.
* **`volumes`**: Mounts host paths or named volumes to the container, used for data persistence.
* **`depends_on`**: Specifies dependencies between services so they start in the correct order (e.g., a web app depends on a database).
* **`networks`**: Defines custom networks for the services to communicate securely.

### Real-World Example: Distributed Task Queue
Here is an example from our `Distributed task queue` project, demonstrating how an API and multiple Celery workers (high, normal, and low priority) are orchestrated:

```yaml
version: '3.8'

services:
  api:
    build:
      context: ./backend
      dockerfile: Dockerfile.api
    ports: 
      - "8000:8000"
    env_file:
      - ./backend/.env

  worker_high:
    build:
      context: ./backend
      dockerfile: Dockerfile.worker
    hostname: high_worker
    command: ["celery", "-A", "src.worker", "worker", "-Q", "high_priority", "--loglevel=info", "--without-gossip", "--without-mingle", "--without-heartbeat"]
    env_file:
      - ./backend/.env

  worker_normal:
    build:
      context: ./backend
      dockerfile: Dockerfile.worker
    hostname: normal_worker
    command: ["celery", "-A", "src.worker", "worker", "-Q", "normal_priority", "--loglevel=info", "--without-gossip", "--without-mingle", "--without-heartbeat"]
    env_file:
      - ./backend/.env

  worker_low:
    build:
      context: ./backend
      dockerfile: Dockerfile.worker
    hostname: low_worker
    command: ["celery", "-A", "src.worker", "worker", "-Q", "low_priority", "--loglevel=info", "--without-gossip", "--without-mingle", "--without-heartbeat"]
    env_file:
      - ./backend/.env        
```

**`docker-compose.prod.yml` (Production Deployment)**
Notice how this file replaces the `build:` block with direct `image:` links to the pre-built images on Docker Hub:

```yaml
version : '3.8'

services:
  api:
    image: saumyashah05/task-queue-api:latest
    ports: 
      - "8000:8000"
    env_file:
      - .env

  worker_high:
    image: saumyashah05/task-queue-worker:latest
    hostname: high_worker
    command: ["celery", "-A", "src.worker", "worker", "-Q", "high_priority", "--concurrency=1", "--loglevel=info", "--without-gossip", "--without-mingle", "--without-heartbeat"]
    env_file:
      - .env

  worker_normal:
    image: saumyashah05/task-queue-worker:latest
    hostname: normal_worker
    command: ["celery", "-A", "src.worker", "worker", "-Q", "normal_priority", "--concurrency=1", "--loglevel=info", "--without-gossip", "--without-mingle", "--without-heartbeat"]
    env_file:
      - .env

  worker_low:
    image: saumyashah05/task-queue-worker:latest
    hostname: low_worker
    command: ["celery", "-A", "src.worker", "worker", "-Q", "low_priority", "--concurrency=1", "--loglevel=info", "--without-gossip", "--without-mingle", "--without-heartbeat"]
    env_file:
      - .env        
```


### Essential Docker Compose Commands
* `docker-compose up` : Build images (if not built) and start all containers defined in the file.
* `docker-compose up -d` : Start all containers in detached (background) mode.
* `docker-compose down` : Stop and remove all containers, networks, and anonymous volumes.
* `docker-compose logs -f` : View and follow logs from all services.
* `docker-compose ps` : List the containers managed by the compose file.

---

## 🔄 CI/CD Pipeline with GitHub Actions

Continuous Integration (CI) and Continuous Deployment (CD) automate the building, testing, and deployment of your code. GitHub Actions is natively integrated into GitHub and uses YAML files to define workflows.

### GitHub Actions Syntax and Concepts

Workflows are placed in your repository under the `.github/workflows/` directory.

* **`name`**: The name of your workflow.
* **`on`**: The event that triggers the workflow (e.g., `push` to the `main` branch, `pull_request`, or a manual `workflow_dispatch`).
* **`jobs`**: A workflow contains one or more jobs. Jobs run in parallel by default but can be chained sequentially.
* **`runs-on`**: Specifies the runner environment (e.g., `ubuntu-latest`).
* **`steps`**: The sequence of tasks that make up a job.
* **`uses`**: Calls an existing, reusable action (like checking out the code or setting up Docker).
* **`run`**: Executes a command-line script.
* **`env`** / **`secrets`**: Used to securely pass API keys and passwords (configured in GitHub Repository Settings -> Secrets).

### Real-World Example: Distributed Task Queue CI/CD Flow
Below is the deployment workflow from our `Distributed task queue` project. It demonstrates a complete pipeline from running tests, to building and pushing Docker images, and finally deploying them to an AWS EC2 instance.

```yaml
name: CI/CD Pipeline

on:
  push:
    branches:
      - main

jobs:
  
  # Job 1 : TEST
  test:
    name: Run Pytest
    runs-on: ubuntu-latest
    env:
      DATABASE_URL: "postgresql+asyncpg://dummy:dummy@localhost:5432/dummy"
      REDIS_URL: "redis://localhost:6379"
    steps:
      - name: Checkout Code(Download all folders in VM)
        uses: actions/checkout@v4
        
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.11"
          
      - name: Install Dependencies
        run: |
          cd backend
          pip install -r requirements.txt

      - name: Run API Tests
        run: |
          cd backend
          python -m pytest tests/test_jobs.py

  # Job 2 : Build and push to docker hub (Only if test job passes)
  build-and-push:
    name: Build docker images
    needs: test
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4
      
      - name: Log in to docker hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
    
      - name: Build and Push API image
        uses: docker/build-push-action@v5
        with:
          context: ./backend
          file: ./backend/Dockerfile.api
          push: true
          tags: ${{ secrets.DOCKERHUB_USERNAME }}/task-queue-api:latest
     
      - name: Build and Push Worker Image
        uses: docker/build-push-action@v5
        with:
          context: ./backend
          file: ./backend/Dockerfile.worker
          push: true
          tags: ${{ secrets.DOCKERHUB_USERNAME }}/task-queue-worker:latest

  # Job 3 : Deploy to AWS EC2
  deploy:
    name: Deploy to AWS EC2
    needs: build-and-push
    runs-on: ubuntu-latest
    steps: 
      - name: SSH into EC2 and Redeploy
        uses: appleboy/ssh-action@v1.0.3
        with:
          host: ${{ secrets.EC2_HOST }}
          username: ${{ secrets.EC2_USERNAME }}
          key: ${{ secrets.EC2_SSH_KEY }}
          script: |
            cd distributed-task-queue
            sudo docker compose pull
            sudo docker compose up -d
```

### Explanation of the Full Flow: From Local Build to Cloud Deployment

In the `Distributed task queue` project, we maintain a clear separation between **development** and **production** environments using two different Compose files: `docker-compose.yml` and `docker-compose.prod.yml`. Here is the complete narrative of how an update flows from your code to the cloud:

1. **Local Development & Building (`docker-compose.yml`)**:
   - During development, we use `docker-compose.yml`. Notice how this file relies heavily on the `build:` directive.
   - When you run `docker-compose up` locally, Docker reads this file, locates `./backend/Dockerfile.api` and `./backend/Dockerfile.worker`, and physically builds the images from your raw source code right on your machine.
   - This allows you to easily test code changes locally by rebuilding the images whenever needed.

2. **Continuous Integration & Delivery (The GitHub Actions Pipeline)**:
   - When you push your finished code to the `main` branch, the CI/CD pipeline (`deploy.yml`) is triggered automatically on GitHub.
   - **Step A (Test)**: A virtual Ubuntu machine spins up, installs your Python dependencies, and runs your Pytest suite to ensure the new code hasn't broken anything.
   - **Step B (Build & Push)**: If the tests pass, GitHub Actions essentially performs what you did locally: it builds the Docker images from the raw code. However, instead of just running them, it tags them (e.g., `task-queue-api:latest`) and **pushes** them to your Docker Hub account. Docker Hub acts as a central storage registry for these finalized, production-ready images.

3. **Cloud Deployment (`docker-compose.prod.yml`)**:
   - On your AWS EC2 cloud server, you don't need the raw source code to build anything. Instead, the server relies on `docker-compose.prod.yml`.
   - If you look at `docker-compose.prod.yml`, it entirely omits the `build:` block. Instead, it uses the `image:` directive, pointing directly to your Docker Hub repository (e.g., `image: saumyashah05/task-queue-api:latest`).
   - **Step C (Deploy)**: The final step in the CI/CD pipeline securely SSHs into your EC2 server and executes the deployment commands:
     1. `sudo docker compose pull`: The cloud server reaches out to Docker Hub and downloads the fresh, pre-built images that GitHub Actions just pushed.
     2. `sudo docker compose up -d`: Docker Compose restarts your running containers (the API and the high, normal, and low priority workers) using the newly downloaded images, spinning them up in the background (`-d`).

**Why this separation matters**: By building the image *once* in the CI/CD pipeline and pushing it to Docker Hub, we ensure that the exact same, tested artifact is deployed to production. The production server doesn't waste CPU or memory compiling code or downloading raw packages; it simply pulls a ready-to-run snapshot and starts it.
