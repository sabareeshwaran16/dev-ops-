# MongoDB + Mongo Express on Kubernetes

## Overview

This project demonstrates how to deploy **MongoDB** and **Mongo Express** on a Kubernetes cluster using:

* Namespace
* Secrets
* Persistent Volume Claims (PVC)
* Deployments
* Services (ClusterIP & NodePort)
* Local Path StorageClass

Mongo Express provides a web-based UI to manage MongoDB databases, while MongoDB stores data persistently using Kubernetes storage.

---

## Architecture

```text
Browser
   |
   | NodePort (30081)
   v
Mongo Express Service
   |
   v
Mongo Express Pod
   |
   | ClusterIP Service
   v
MongoDB Service
   |
   v
MongoDB Pod
   |
   v
Persistent Volume Claim
   |
   v
Persistent Volume
```

---

## Components

### 1. Namespace

All resources are deployed inside a dedicated namespace:

```yaml
mongodb
```

---

### 2. Secret

Stores MongoDB credentials securely.

```yaml
mongo-root-username: admin
mongo-root-password: password123
```

---

### 3. Persistent Volume Claim

Provides persistent storage for MongoDB data.

```yaml
storageClassName: local-path
storage: 1Gi
```

---

### 4. MongoDB Deployment

Deploys a MongoDB container and mounts persistent storage.

**Image:**

```yaml
mongo:7
```

**Port:**

```yaml
27017
```

---

### 5. MongoDB Service

Internal service used by Mongo Express.

**Type:**

```yaml
ClusterIP
```

**Port:**

```yaml
27017
```

---

### 6. Mongo Express Deployment

Provides a web interface for managing MongoDB.

**Image:**

```yaml
mongo-express:latest
```

**Port:**

```yaml
8081
```

---

### 7. Mongo Express Service

Exposes Mongo Express externally.

**Type:**

```yaml
NodePort
```

**NodePort:**

```yaml
30081
```

---

## Deployment Steps

### Create Namespace

```bash
kubectl create namespace mongodb
```

---

### Create Secret

```bash
kubectl apply -f mongo-secret.yaml
```

---

### Create PVC

```bash
kubectl apply -f mongodb-pvc.yaml
```

---

### Deploy MongoDB

```bash
kubectl apply -f mongodb-deployment.yaml
kubectl apply -f mongodb-service.yaml
```

---

### Deploy Mongo Express

```bash
kubectl apply -f mongo-express-deployment.yaml
kubectl apply -f mongo-express-service.yaml
```

---

## Verification

Check all resources:

```bash
kubectl get all -n mongodb
```

Check PVC:

```bash
kubectl get pvc -n mongodb
```

Check services:

```bash
kubectl get svc -n mongodb
```

---

## Access Mongo Express

Get node IP:

```bash
kubectl get nodes -o wide
```

Open:

```text
http://<NODE-IP>:30081
```

Example:

```text
http://192.168.1.100:30081
```

---

## MongoDB Authentication

Connect to MongoDB shell:

```bash
kubectl exec -it deployment/mongodb -n mongodb -- mongosh
```

Authenticate:

```javascript
use admin
db.auth("admin","password123")
```

List databases:

```javascript
show dbs
```

---

## Data Persistence Test

Insert sample data:

```javascript
use company

db.employees.insertOne({
  name: "Sabareesh",
  role: "DevOps Engineer"
})
```

Delete MongoDB pod:

```bash
kubectl delete pod -n mongodb -l app=mongodb
```

Verify data still exists:

```javascript
use company
db.employees.find()
```

Since MongoDB uses a Persistent Volume Claim, data remains available even after pod recreation.

---

## Learning Outcomes

This project covers:

* Kubernetes Namespace
* Secrets
* Deployments
* Pods
* Services
* ClusterIP
* NodePort
* Persistent Volumes
* Persistent Volume Claims
* StorageClass
* Internal DNS
* MongoDB Administration
* Stateful Application Concepts

---

## Cleanup

Delete all resources:

```bash
kubectl delete namespace mongodb
```

---

## Author

**Sabareesh**

Kubernetes Stateful Application Lab using MongoDB and Mongo Express.




README.md
architecture.png
mongo-secret.yaml
mongodb-pvc.yaml
mongodb-deployment.yaml
mongodb-service.yaml
mongo-express-deployment.yaml
mongo-express-service.yaml



