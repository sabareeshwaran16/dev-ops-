# Python CI/CD Demo Project

## Overview

This project demonstrates a simple Continuous Integration (CI) pipeline using GitLab CI/CD, Docker, and Docker Hub.

The pipeline performs the following tasks:

1. Build Validation
2. Automated Testing
3. Docker Image Creation
4. Automatic Versioning
5. Docker Image Push to Docker Hub

---

## Project Structure

```text
python-ci-demo/
│
├── app.py
├── test_app.py
├── requirements.txt
├── Dockerfile
└── .gitlab-ci.yml
```

---

## CI Pipeline Workflow

```text
Developer Pushes Code
          ↓
Build Stage
          ↓
Test Stage
          ↓
Docker Build
          ↓
Automatic Versioning
          ↓
Push to Docker Hub
```

---

## Build Stage

### Purpose

The build stage validates the Python application and checks for syntax errors.

### Command

```bash
python -m py_compile app.py
```

### Example

Valid Python code:

```python
def add(a, b):
    return a + b
```

Invalid Python code:

```python
def add(a, b)
    return a + b
```

Result:

```text
Build Failed
```

The pipeline stops immediately if a syntax error is found.

---

## Test Stage

### Purpose

The test stage verifies application functionality.

### Application File

```python
def add(a, b):
    return a + b
```

### Test File

```python
from app import add

def test_add():
    assert add(2, 3) == 5
```

### Command

```bash
pytest
```

### Output

```text
1 passed
```

If the assertion fails, the pipeline fails.

---

## Docker Build Stage

### Purpose

Packages the application into a Docker image.

### Dockerfile

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY . .

RUN pip install -r requirements.txt

CMD ["python", "app.py"]
```

### Build Command

```bash
docker build -t python-ci-demo .
```

---

## Automatic Versioning

### Purpose

Assigns a unique version to every pipeline run.

### GitLab Variable

```text
CI_PIPELINE_ID
```

### Example

```text
Pipeline Run 1 → Version 1
Pipeline Run 2 → Version 2
Pipeline Run 3 → Version 3
```

### Docker Image Tag

```bash
docker build -t python-ci-demo:$CI_PIPELINE_ID .
```

Generated Images:

```text
python-ci-demo:1
python-ci-demo:2
python-ci-demo:3
```

### Benefits

* Unique image versions
* Easy rollback
* Deployment tracking
* Release management

---

## Docker Push Stage

### Purpose

Pushes the Docker image to Docker Hub.

### Login

```bash
docker login
```

### Push Command

```bash
docker push python-ci-demo:$CI_PIPELINE_ID
```

### Example

```text
sabareeshwaran6999/python-ci-demo:1
sabareeshwaran6999/python-ci-demo:2
sabareeshwaran6999/python-ci-demo:3
```

---

## GitLab CI/CD Configuration

### .gitlab-ci.yml

```yaml
stages:
  - build
  - test
  - docker
  - push

variables:
  IMAGE_NAME: sabareeshwaran6999/python-ci-demo

build:
  stage: build
  image: python:3.11

  script:
    - echo "Build Stage Started"
    - python -m py_compile app.py
    - echo "Build Successful"

test:
  stage: test
  image: python:3.11

  script:
    - pip install -r requirements.txt
    - pytest

docker:
  stage: docker
  image: docker:24

  services:
    - docker:24-dind

  script:
    - echo "Pipeline ID = $CI_PIPELINE_ID"
    - docker build -t $IMAGE_NAME:$CI_PIPELINE_ID .

push:
  stage: push
  image: docker:24

  services:
    - docker:24-dind

  before_script:
    - echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin

  script:
    - docker build -t $IMAGE_NAME:$CI_PIPELINE_ID .
    - docker push $IMAGE_NAME:$CI_PIPELINE_ID
```

---

## GitLab CI/CD Variables

Configure the following variables in GitLab:

```text
Project
 ↓
Settings
 ↓
CI/CD
 ↓
Variables
```

| Variable        | Description             |
| --------------- | ----------------------- |
| DOCKER_USERNAME | Docker Hub Username     |
| DOCKER_PASSWORD | Docker Hub Access Token |

Example:

```text
DOCKER_USERNAME=sabareeshwaran6999
DOCKER_PASSWORD=docker_access_token
```

---

## Docker Hub Verification

After a successful pipeline run:

```text
Docker Hub
    ↓
Repositories
    ↓
python-ci-demo
    ↓
Tags
```

You will see:

```text
1
2
3
4
5
...
```

Each tag represents a unique GitLab pipeline execution.

---

## Concepts Learned

### Continuous Integration (CI)

Automates:

* Build Validation
* Testing
* Docker Packaging
* Versioning
* Image Publishing

### Build

Validates source code syntax.

### Test

Verifies application logic.

### Docker Build

Creates a portable container image.

### Automatic Versioning

Uses:

```text
CI_PIPELINE_ID
```

to generate unique image versions.

### Docker Push

Stores images in Docker Hub for deployment.

---

## Complete CI Flow

```text
Developer Changes Code
          ↓
Git Commit
          ↓
Git Push
          ↓
GitLab Pipeline Triggered
          ↓
Build Validation
          ↓
Automated Testing
          ↓
Docker Image Build
          ↓
Automatic Versioning
          ↓
Docker Push
          ↓
Image Available in Docker Hub
```

## Sample Output

```text
Build Successful
1 passed

Pipeline ID = 101

Docker Image:
sabareeshwaran6999/python-ci-demo:101

Image pushed successfully.
```

This project demonstrates the fundamental CI concepts used in real-world DevOps pipelines before moving to Continuous Deployment (CD) and Kubernetes.
