# Docker Basic Commands Lab

## Objective

In this lab, you will learn how to:

- Verify Docker installation
- Search and download Docker images
- Run containers in interactive and detached modes
- Access container terminals
- Manage container lifecycle
- Remove containers and images

---

# Verify Docker Version

Check the installed Docker version:

```bash
docker version
```

---

# View System-Wide Docker Information

Display Docker daemon and system information:

```bash
docker info
```

---

# Search Docker Images

Search for images available on Docker Hub:

```bash
docker search <image-name>
```

Example:

```bash
docker search ubuntu
```

---

# Download Docker Images

Pull images from Docker Hub:

```bash
docker pull ubuntu:20.04
docker pull ubuntu:22.04
docker pull centos:8
docker pull nginx
```

Verify downloaded images:

```bash
docker images
```

---

# View Downloaded Images

List all downloaded images:

```bash
docker image ls
```

or

```bash
docker images
```

Example Output:

```text
REPOSITORY   TAG      IMAGE ID       CREATED
ubuntu       22.04    xxxxxxxxxxxx   2 weeks ago
nginx        latest   xxxxxxxxxxxx   5 days ago
```

---

# Run a Container in Interactive Mode

Launch an NGINX container:

```bash
docker run -it --name=mynginx nginx
```

### Exit Options

To stop and exit the container:

```bash
exit
```

To leave the container without stopping it:

```text
Ctrl + P + Q
```

---

# Run a Container in Detached Mode

Start a container in the background:

```bash
docker run -it -d --name=httpd-container httpd
```

### Explanation

| Option | Description |
|----------|-------------|
| `-i` | Interactive mode |
| `-t` | Terminal access |
| `-d` | Detached mode (background) |
| `--name` | Assign a custom container name |

---

# View Running Containers

Display currently running containers:

```bash
docker ps
```

Example Output:

```text
CONTAINER ID   IMAGE   STATUS
abc123         httpd   Up 2 minutes
```

---

# View All Containers

Show running, stopped, and exited containers:

```bash
docker ps -a
```

---

# View the Latest Container

Display the most recently created container:

```bash
docker ps -l
```

---

# Access Container Terminal

Connect to a running container:

```bash
docker exec -it <container-name> /bin/bash
```

Example:

```bash
docker exec -it httpd-container /bin/bash
```

---

# Stop a Container

Stop a running container:

```bash
docker stop httpd-container
```

---

# Start a Container

Start a stopped container:

```bash
docker start httpd-container
```

---

# Restart a Container

Restart a container:

```bash
docker restart httpd-container
```

---

# Kill a Container

Forcefully stop a container:

```bash
docker kill httpd-container
```

---

# Remove a Container

First stop the container:

```bash
docker stop nginx-container
```

Remove the container:

```bash
docker rm nginx-container
```

---

# Force Remove a Running Container

Delete a running container without stopping it manually:

```bash
docker rm -f nginx-container
```

---

# Remove Docker Images

Delete an image by name:

```bash
docker rmi ubuntu:20.04
```

Delete an image by Image ID:

```bash
docker rmi 54c9d81cbb44
```

---

# Useful Cleanup Commands

Remove all stopped containers:

```bash
docker container prune
```

Remove unused images:

```bash
docker image prune
```

Remove unused volumes:

```bash
docker volume prune
```

Remove everything unused:

```bash
docker system prune -a
```

---

# Verification Commands

Check Docker Version:

```bash
docker version
```

List Images:

```bash
docker images
```

List Running Containers:

```bash
docker ps
```

List All Containers:

```bash
docker ps -a
```

Docker System Information:

```bash
docker info
```

---

# Key Takeaways

- Docker images are templates used to create containers.
- Containers are lightweight, isolated runtime environments.
- `docker pull` downloads images from Docker Hub.
- `docker run` creates and starts containers.
- `docker exec` provides terminal access to running containers.
- `docker ps` displays container status.
- `docker rm` removes containers.
- `docker rmi` removes images.
- `docker system prune -a` helps clean unused Docker resources.
