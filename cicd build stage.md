# GitLab CI/CD Lab: Build and Push Docker Image to Docker Hub

## Overview

This lab demonstrates how to use GitLab CI/CD to automatically build a Docker image and push it to Docker Hub whenever code is pushed to a GitLab repository.

### Key Concepts

**GitLab CI/CD**

* Automates software build and delivery workflows.
* Executes pipelines whenever code is pushed to GitLab.

**GitLab Runner**

* Executes CI/CD jobs.
* Reads instructions from `.gitlab-ci.yml`.

**Docker Image**

* A packaged application with all required dependencies.
* Built using a Dockerfile.

**Docker Hub**

* Public container registry.
* Stores and distributes Docker images.

---

## Architecture

```text
+-------------------+
|    Developer      |
+---------+---------+
          |
          | git push
          v
+-------------------+
| GitLab Repository |
+---------+---------+
          |
          |
          v
+-------------------+
| GitLab Pipeline   |
+---------+---------+
          |
          |
          v
+-------------------+
|  GitLab Runner    |
+---------+---------+
          |
          |
          +------> Docker Build
          |
          +------> Docker Login
          |
          +------> Docker Tag
          |
          +------> Docker Push
          |
          v
+-------------------+
|    Docker Hub     |
+-------------------+
```

---

## Objectives

* Create a sample web application
* Create a Dockerfile
* Build a Docker image locally
* Create a GitLab repository
* Configure GitLab CI/CD Variables
* Create a GitLab Pipeline
* Build Docker image automatically
* Push Docker image to Docker Hub
* Verify image availability
* Pull image in KillerCoda
* Run the container

---

## Lab Workflow

### 1. Create Project Directory

```bash
mkdir cicd_demo
cd cicd_demo
```

---

### 2. Create Application File

Create:

```bash
vi index.html
```

Add:

```html
<h1>Hello from GitLab CI/CD</h1>
```

---

### 3. Create Dockerfile

Create:

```bash
vi Dockerfile
```

Add:

```dockerfile
FROM nginx:latest

COPY index.html /usr/share/nginx/html/index.html
```

---

### 4. Build Docker Image Locally

```bash
docker build -t webapp:v1 .
```

Verify:

```bash
docker images
```

Expected:

```text
REPOSITORY   TAG
webapp       v1
```

---

### 5. Run Container Locally

```bash
docker run -d -p 8080:80 webapp:v1
```

Verify:

```bash
curl localhost:8080
```

Expected:

```html
<h1>Hello from GitLab CI/CD</h1>
```

---

### 6. Create GitLab Repository

Create a new project in GitLab.

Example:

```text
cicd-demo
```

---

### 7. Initialize Git Repository

```bash
git init
```

Add files:

```bash
git add .
```

Commit:

```bash
git commit -m "Initial Commit"
```

Add remote repository:

```bash
git remote add origin <GITLAB_REPOSITORY_URL>
```

Push code:

```bash
git branch -M main
git push -u origin main
```

---

### 8. Configure GitLab CI/CD Variables

Navigate:

```text
Project
 └── Settings
      └── CI/CD
           └── Variables
```

Create:

#### DOCKER_USERNAME

```text
sabareeshwaran6999
```

#### DOCKER_PASSWORD

```text
Docker Hub Access Token
```

---

### 9. Create GitLab Pipeline

Create:

```bash
vi .gitlab-ci.yml
```

Add:

```yaml
image: docker:latest

services:
  - docker:dind

stages:
  - build

build-image:
  stage: build

  script:
    - docker build -t webapp:v1 .
    - docker images
    - docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
    - docker tag webapp:v1 sabareeshwaran6999/cicd_demo:v1
    - docker push sabareeshwaran6999/cicd_demo:v1
```

---

### 10. Commit Pipeline Configuration

```bash
git add .
git commit -m "Added GitLab CI/CD Pipeline"
git push
```

---

### 11. Verify Pipeline Execution

Navigate:

```text
Build
 └── Pipelines
```

Expected:

```text
Passed
```

Pipeline executes:

```text
docker build
docker images
docker login
docker tag
docker push
```

---

### 12. Verify Docker Hub Repository

Repository:

```text
sabareeshwaran6999/cicd_demo
```

Expected Tag:

```text
v1
```

---

### 13. Pull Image in KillerCoda

```bash
docker pull sabareeshwaran6999/cicd_demo:v1
```

Verify:

```bash
docker images
```

Expected:

```text
REPOSITORY                     TAG
sabareeshwaran6999/cicd_demo   v1
```

---

### 14. Run Container in KillerCoda

```bash
docker run -d \
--name cicd-demo \
-p 8080:80 \
sabareeshwaran6999/cicd_demo:v1
```

Verify:

```bash
docker ps
```

Expected:

```text
cicd-demo
```

---

### 15. Access Application

```bash
curl localhost:8080
```

Expected:

```html
<h1>Hello from GitLab CI/CD</h1>
```

---

## Cleanup

Stop container:

```bash
docker stop cicd-demo
```

Remove container:

```bash
docker rm cicd-demo
```

Remove image:

```bash
docker rmi sabareeshwaran6999/cicd_demo:v1
```

---

## Learning Outcomes

After completing this lab, you will understand:

* GitLab CI/CD
* GitLab Runner
* Dockerfile
* Docker Image Build
* Docker Image Tagging
* Docker Hub Authentication
* Docker Image Push
* Docker Image Pull
* Container Execution
* End-to-End CI Workflow

---

## Repository Structure

```text
cicd_demo/
│
├── index.html
├── Dockerfile
├── .gitlab-ci.yml
└── README.md
```
