# CI/CD Pipeline for Python Flask Application using GitLab CI and Docker Hub

## Overview

This project demonstrates a basic CI/CD pipeline using:

* Python Flask Application
* Docker
* GitLab CI/CD
* Docker Hub

The pipeline automatically:

1. Clones the source code from GitLab
2. Builds a Docker image
3. Tags the image
4. Pushes the image to Docker Hub

---

## Project Structure

```text
.
├── app.py
├── requirements.txt
├── Dockerfile
└── .gitlab-ci.yml
```

---

## Flask Application

### app.py

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return "Hello from Flask CI/CD Pipeline!"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

---

## Requirements

### requirements.txt

```text
Flask==2.3.2
```

---

## Dockerfile

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]
```

---

## Docker Build Process

Build the Docker image:

```bash
docker build -t python-app:v1 .
```

View images:

```bash
docker images
```

Tag image for Docker Hub:

```bash
docker tag python-app:v1 <dockerhub-username>/cicdpython:v1
```

Example:

```bash
docker tag python-app:v1 sabareeshwaran6999/cicdpython:v1
```

---

## Docker Hub Login

Login using Docker Hub credentials:

```bash
docker login
```

Or:

```bash
docker login -u <dockerhub-username>
```

---

## Push Image to Docker Hub

```bash
docker push <dockerhub-username>/cicdpython:v1
```

Example:

```bash
docker push sabareeshwaran6999/cicdpython:v1
```

---

## GitLab CI/CD Pipeline

### .gitlab-ci.yml

```yaml
stages:
  - build

docker_build_job:
  stage: build

  image: docker

  services:
    - docker:dind

  script:
    - docker build -t python-app:v1 .
    - docker tag python-app:v1 $DOCKER_USERNAME/cicdpython:v1
    - echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
    - docker push $DOCKER_USERNAME/cicdpython:v1
```

---

## GitLab CI/CD Variables

Navigate to:

```text
GitLab Project
→ Settings
→ CI/CD
→ Variables
```

Add the following variables:

| Variable        | Value                    |
| --------------- | ------------------------ |
| DOCKER_USERNAME | Your Docker Hub Username |
| DOCKER_PASSWORD | Docker Hub Access Token  |

---

## Pipeline Workflow

```text
Developer Pushes Code
          │
          ▼
GitLab Repository
          │
          ▼
GitLab Runner
          │
          ▼
Docker Build
          │
          ▼
Docker Image Created
          │
          ▼
Docker Hub Login
          │
          ▼
Docker Image Push
          │
          ▼
Image Stored in Docker Hub
```

---

## Verify Image

Pull image:

```bash
docker pull sabareeshwaran6999/cicdpython:v1
```

Run container:

```bash
docker run -d -p 5000:5000 --name python-app sabareeshwaran6999/cicdpython:v1
```

Check running container:

```bash
docker ps
```

Check logs:

```bash
docker logs python-app
```

---

## Kubernetes Deployment (Optional)

### Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: python-app

spec:
  replicas: 1

  selector:
    matchLabels:
      app: python-app

  template:
    metadata:
      labels:
        app: python-app

    spec:
      containers:
      - name: python-app
        image: sabareeshwaran6999/cicdpython:v1

        ports:
        - containerPort: 5000
```

Apply deployment:

```bash
kubectl apply -f deployment.yaml
```

Expose deployment:

```bash
kubectl expose deployment python-app \
--type=NodePort \
--port=5000 \
--target-port=5000
```

Check resources:

```bash
kubectl get pods
kubectl get svc
```

---

## Concepts Learned

### CI (Continuous Integration)

* Automated build process
* Code integration
* Pipeline execution
* Docker image creation

### CD (Continuous Delivery)

* Docker image publishing
* Artifact management
* Deployment readiness

### Docker

* Dockerfile
* Image creation
* Image tagging
* Docker Hub integration

### GitLab CI/CD

* GitLab Runner
* Pipeline stages
* CI/CD variables
* Automated workflows

---

## Outcome

Successfully built a Python Flask application, containerized it using Docker, automated image creation through GitLab CI/CD, and published the image to Docker Hub for deployment in Kubernetes or any container platform.
