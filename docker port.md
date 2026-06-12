# Docker Container Management Lab

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

---

# View Downloaded Docker Images

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

Create and start an NGINX container:

```bash
docker run -it --name=mynginx nginx
```

### Exit Options

Stop and exit the container:

```bash
exit
```

Detach from the container without stopping it:

```text
Ctrl + P + Q
```

---

# Run a Container in Detached Mode

Start an Apache HTTPD container in the background:

```bash
docker run -it -d --name=httpd-container httpd
```

### Option Explanation

| Option | Description |
|----------|-------------|
| `-i` | Interactive mode |
| `-t` | Allocate terminal |
| `-d` | Detached/background mode |
| `--name` | Assign container name |

---

# View Running Containers

Display currently running containers:

```bash
docker ps
```

---

# View All Containers

Display running, stopped, and exited containers:

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

# Access a Running Container

Open a shell inside a running container:

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

Stop the container first:

```bash
docker stop nginx-container
```

Remove the container:

```bash
docker rm nginx-container
```

---

# Force Remove a Running Container

Remove a running container without stopping it manually:

```bash
docker rm -f nginx-container
```

---

# Remove Docker Images

Remove an image by name:

```bash
docker rmi ubuntu:20.04
```

Remove an image by Image ID:

```bash
docker rmi 54c9d81cbb44
```

---

# Verification Commands

Check Docker Version:

```bash
docker version
```

Check Docker Information:

```bash
docker info
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

---

# Lab Summary

- `docker search` searches images on Docker Hub.
- `docker pull` downloads images.
- `docker run` creates and starts containers.
- `docker exec` provides terminal access to containers.
- `docker ps` displays running containers.
- `docker stop`, `start`, `restart`, and `kill` manage container states.
- `docker rm` removes containers.
- `docker rmi` removes images.

---

# Cleanup Commands

Remove stopped containers:

```bash
docker container prune
```

Remove unused images:

```bash
docker image prune
```

Remove all unused resources:

```bash
docker system prune -a
```
