# 🚀 GitLab CI/CD + Docker + Kubernetes + Argo CD (GitOps)

## 📌 Project Overview

This project demonstrates a complete GitOps workflow using:

* GitLab Repository
* GitLab CI/CD
* Docker
* Docker Hub
* Kubernetes (K3s)
* Argo CD

The goal is to automatically build, store, and deploy a Python Flask application into Kubernetes whenever code changes are pushed to GitLab.

---

# 🏗️ Architecture

```text
Developer
    ↓
Git Push
    ↓
GitLab Repository
    ↓
GitLab CI/CD Pipeline
    ↓
Docker Build
    ↓
Docker Hub
    ↓
Kubernetes Manifest Update
    ↓
Argo CD
    ↓
Kubernetes Cluster
    ↓
Running Application
```

---

# 📁 Project Structure

```text
cicdfulldev/
│
├── app/
│   ├── app.py
│   ├── requirements.txt
│   └── Dockerfile
│
├── gitlab-k8s-demo/
│   ├── python-app-deployment.yaml
│   └── python-app-svc.yaml
│
└── .gitlab-ci.yml
```

---

# Step 1: Create Flask Application

## app/app.py

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Welcome to DevOps Lab"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

### Purpose

```text
Creates a Flask web application.

URL:
http://<IP>:5000

Output:
Welcome to DevOps Lab
```

---

# Step 2: Create Requirements File

## app/requirements.txt

```text
flask
```

### Purpose

```text
Installs Flask dependency inside Docker container.
```

---

# Step 3: Create Dockerfile

## app/Dockerfile

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY . .

RUN pip install -r requirements.txt

EXPOSE 5000

CMD ["python","app.py"]
```

### Purpose

```text
1. Downloads Python image
2. Creates working directory
3. Copies application files
4. Installs Flask
5. Exposes port 5000
6. Starts Flask application
```

---

# Step 4: Create GitLab Repository

Create a project in GitLab:

```text
Project Name:
cicdfulldev

Visibility:
Private
```

Clone repository:

```bash
git clone https://gitlab.com/<username>/cicdfulldev.git

cd cicdfulldev
```

---

# Step 5: Configure GitLab Variables

Navigate:

```text
Project
↓
Settings
↓
CI/CD
↓
Variables
```

Create variables:

| Variable        | Description                  |
| --------------- | ---------------------------- |
| DOCKER_USERNAME | Docker Hub Username          |
| DOCKER_PASSWORD | Docker Hub PAT               |
| GITLAB_USERNAME | GitLab Username              |
| GITLAB_TOKEN    | GitLab Personal Access Token |

### Purpose

```text
Stores secrets securely.

Avoids exposing credentials in code.
```

---

# Step 6: Create GitLab CI/CD Pipeline

## .gitlab-ci.yml

```yaml
stages:
  - build

variables:
  IMAGE_NAME: my_python_app

docker_build_job:
  stage: build
  image: docker:24

  services:
    - docker:24-dind

  variables:
    DOCKER_TLS_CERTDIR: ""

  script:
    - echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin

    - docker build -t $DOCKER_USERNAME/$IMAGE_NAME:$CI_COMMIT_SHORT_SHA ./app

    - docker push $DOCKER_USERNAME/$IMAGE_NAME:$CI_COMMIT_SHORT_SHA
```

### Purpose

```text
Git Push
↓
Pipeline Triggered
↓
Docker Image Build
↓
Docker Hub Push
```

---

# Step 7: Push Code to GitLab

```bash
git add .

git commit -m "Initial Commit"

git push origin main
```

### Purpose

```text
Triggers GitLab CI/CD Pipeline automatically.
```

---

# Step 8: Verify Docker Hub

After successful pipeline:

```text
Docker Hub
↓
Repositories
↓
my_python_app
```

Example tags:

```text
ecd3ea09
51879cea
d764b93e
```

---

# Step 9: Create Kubernetes Deployment

## gitlab-k8s-demo/python-app-deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: my-python-app-deployment

spec:
  replicas: 2

  selector:
    matchLabels:
      app: myapp

  template:
    metadata:
      labels:
        app: myapp

    spec:
      containers:
      - name: my-python-app-container

        image: sabareeshwaran6999/my_python_app:ecd3ea09

        imagePullPolicy: Always

        ports:
        - containerPort: 5000
```

### Purpose

```text
Deployment
↓
Creates Pods
↓
Pods Pull Image From Docker Hub
↓
Application Starts
```

---

# Step 10: Create Kubernetes Service

## gitlab-k8s-demo/python-app-svc.yaml

```yaml
apiVersion: v1
kind: Service

metadata:
  name: myapp-service

spec:
  selector:
    app: myapp

  ports:
    - port: 80
      targetPort: 5000

  type: NodePort
```

### Purpose

```text
Exposes application outside cluster.
```

---

# Step 11: Install Argo CD

Create namespace:

```bash
kubectl create namespace argocd
```

Install Argo CD:

```bash
kubectl apply -n argocd \
-f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Verify:

```bash
kubectl get pods -n argocd
```

Expected:

```text
argocd-server
argocd-repo-server
argocd-application-controller

Running
```

---

# Step 12: Expose Argo CD UI

Convert Argo CD service:

```bash
kubectl patch svc argocd-server \
-n argocd \
-p '{"spec":{"type":"LoadBalancer"}}'
```

Verify:

```bash
kubectl get svc -n argocd
```

Example:

```text
argocd-server   LoadBalancer

EXTERNAL-IP:
192.168.218.241
```

Access:

```text
https://192.168.218.241
```

---

# Step 13: Generate Argo CD Admin Password

Run:

```bash
kubectl -n argocd get secret argocd-initial-admin-secret \
-o jsonpath="{.data.password}" | base64 -d
```

Example Output:

```text
OPGqTcIVPDBOdEs3
```

Login:

```text
Username:
admin

Password:
OPGqTcIVPDBOdEs3
```

---

# Step 14: Create GitLab Personal Access Token

GitLab:

```text
Profile
↓
Preferences
↓
Access Tokens
```

Create Token:

```text
Name:
argocd-token

Scopes:
✔ api
✔ read_repository
```

Copy token.

---

# Step 15: Connect GitLab Repository to Argo CD

Navigate:

```text
Argo CD
↓
Settings
↓
Repositories
↓
Connect Repo
```

Choose:

```text
HTTPS
```

Repository URL:

```text
https://gitlab.com/<group>/<project>.git
```

Username:

```text
oauth2
```

Password:

```text
GitLab PAT
```

Click:

```text
Connect
```

Status:

```text
Successful
```

---

# Step 16: Create Argo CD Application

Navigate:

```text
Applications
↓
New App
```

Application Name:

```text
python-app
```

Project:

```text
default
```

Repository:

```text
https://gitlab.com/<group>/<project>.git
```

Revision:

```text
HEAD
```

Path:

```text
gitlab-k8s-demo
```

Cluster:

```text
https://kubernetes.default.svc
```

Namespace:

```text
default
```

Sync Policy:

```text
Automatic
```

Click:

```text
Create
```

---

# Step 17: Verify Deployment

Check Deployment:

```bash
kubectl get deployment
```

Check Pods:

```bash
kubectl get pods
```

Check Service:

```bash
kubectl get svc
```

---

# Step 18: Access Application

Get NodePort:

```bash
kubectl get svc
```

Example:

```text
myapp-service

80:30654/TCP
```

Get Node IP:

```bash
kubectl get nodes -o wide
```

Example:

```text
192.168.218.129
```

Access:

```text
http://192.168.218.129:30654
```

Output:

```text
Welcome to DevOps Lab
```

---

# Problem Faced

Pods showed:

```text
ImagePullBackOff
```

Reason:

```yaml
image: my_python_app:latest
```

But Docker Hub had:

```text
ecd3ea09
51879cea
d764b93e
```

No latest tag existed.

---

# Solution

Updated Deployment:

```yaml
image: sabareeshwaran6999/my_python_app:ecd3ea09
```

Pods became:

```text
Running
```

---

# Final Workflow

```text
Developer
↓
Git Push
↓
GitLab CI/CD
↓
Docker Build
↓
Docker Hub
↓
Argo CD
↓
Kubernetes Deployment
↓
Pods Running
↓
Service Exposed
↓
Application Accessible
```

---

# Interview Explanation

"I developed a Flask application and containerized it using Docker. The source code was stored in GitLab. Using GitLab CI/CD, every code push automatically built a Docker image and pushed it to Docker Hub. Kubernetes Deployment pulled the image and created Pods. A NodePort Service exposed the application. Argo CD was connected to the GitLab repository and continuously monitored Kubernetes manifests. Whenever changes were pushed, Argo CD synchronized the cluster and deployed the latest version automatically, implementing a complete GitOps workflow."
