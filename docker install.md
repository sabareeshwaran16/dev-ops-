# Docker Installation on Ubuntu 24.04

## Objective

Install Docker Engine, Docker CLI, Containerd, Buildx, and Docker Compose Plugin on Ubuntu 24.04.

---

# Step 1: Remove Existing Docker Packages

Remove any old Docker packages if they exist:

```bash
apt remove docker docker-engine docker.io containerd runc -y
```

---

# Step 2: Update Package Repository

Update the package index:

```bash
apt update
```

---

# Step 3: Install Prerequisite Packages

Install required packages:

```bash
apt install apt-transport-https ca-certificates curl software-properties-common lsb-release -y
```

---

# Step 4: Add Docker Official GPG Key

Create the keyrings directory:

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

Download Docker's GPG key:

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

Set proper permissions:

```bash
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

---

# Step 5: Add Docker Repository

Add Docker's official repository:

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

---

# Step 6: Verify Repository Configuration

Check Docker package availability:

```bash
apt-cache policy docker-ce
```

Update package index again:

```bash
sudo apt update
```

---

# Step 7: Install Docker

Install Docker Engine and related components:

```bash
apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Installed Components:

- Docker Engine
- Docker CLI
- Containerd
- Docker Buildx
- Docker Compose Plugin

---

# Step 8: Verify Docker Service

Check Docker service status:

```bash
systemctl status docker
```

Enable Docker to start automatically on boot:

```bash
sudo systemctl enable docker
```

Start Docker service:

```bash
sudo systemctl start docker
```

---

# Step 9: Verify Installation

Check Docker version:

```bash
docker --version
```

Example Output:

```text
Docker version 28.x.x
```

Check Containerd version:

```bash
containerd --version
```

View Docker system information:

```bash
docker info
```

---

# Step 10: Test Docker Installation

Run a test container:

```bash
docker run hello-world
```

Expected Output:

```text
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

---

# Optional: Run Docker Without sudo

Add your user to the Docker group:

```bash
sudo usermod -aG docker $USER
```

Apply the changes:

```bash
newgrp docker
```

Verify:

```bash
docker ps
```

You should now be able to run Docker commands without `sudo`.

---

# Useful Commands

Check Docker Version:

```bash
docker --version
```

Check Running Containers:

```bash
docker ps
```

List Docker Images:

```bash
docker images
```

Check Docker Service:

```bash
systemctl status docker
```

Restart Docker:

```bash
systemctl restart docker
```

---

# Verification Checklist

- [ ] Docker repository added successfully
- [ ] Docker packages installed
- [ ] Docker service running
- [ ] Docker enabled on boot
- [ ] `docker --version` working
- [ ] `docker info` working
- [ ] `docker run hello-world` successful

---

# Conclusion

Docker has been successfully installed on Ubuntu 24.04. The system is now ready to build, run, and manage containers using Docker Engine and Docker Compose.
