# Kubernetes Lab: WordPress with MySQL StatefulSet using local-path StorageClass

## Overview

This lab demonstrates how to deploy a WordPress application with a MySQL database running as a StatefulSet in Kubernetes.

### Key Concepts

**StatefulSet**

* A Kubernetes workload used for stateful applications.
* Provides stable pod names, persistent storage, and ordered deployment.

**Headless Service**

* A Service with `clusterIP: None`.
* Provides direct DNS access to individual StatefulSet pods.

**Persistent Volume Claim (PVC)**

* Requests storage from Kubernetes.
* Ensures data survives pod restarts.

**StorageClass**

* Automatically provisions storage for PVCs.
* This lab uses the default `local-path` StorageClass.

---

## Architecture

```text
+-------------------+
|    WordPress      |
| Deployment        |
+---------+---------+
          |
          |
          v
+-------------------+
|   mysql Service   |
+---------+---------+
          |
          |
          v
+-------------------+
| MySQL StatefulSet |
| mysql-0           |
+---------+---------+
          |
          |
          v
+-------------------+
| PVC (local-path)  |
+-------------------+
```

---

## Objectives

* Create a Namespace
* Create a Secret
* Create a Headless Service
* Deploy MySQL StatefulSet
* Dynamically provision storage using local-path StorageClass
* Verify StatefulSet DNS
* Deploy WordPress
* Expose WordPress using NodePort
* Validate persistence
* Scale StatefulSet replicas

---

## Lab Workflow

### 1. Verify StorageClass

```bash
kubectl get sc
```

Expected:

```text
local-path (default)
```

---

### 2. Create Namespace

```bash
kubectl apply -f namespace.yaml
```

Verify:

```bash
kubectl get ns
```

---

### 3. Create MySQL Secret

Stores the MySQL root password securely.

```bash
kubectl apply -f mysql-secret.yaml
```

Verify:

```bash
kubectl get secret -n wordpress
```

---

### 4. Create Headless Service

Provides stable DNS names for StatefulSet pods.

```bash
kubectl apply -f mysql-headless-svc.yaml
```

Verify:

```bash
kubectl get svc -n wordpress
```

Expected:

```text
mysql   ClusterIP   None
```

---

### 5. Deploy MySQL StatefulSet

Creates:

* mysql-0
* PVC automatically
* Persistent storage

```bash
kubectl apply -f mysql-sts.yaml
```

Verify:

```bash
kubectl get sts -n wordpress
kubectl get pvc -n wordpress
kubectl get pods -n wordpress
```

---

### 6. Verify DNS Resolution

Launch a troubleshooting pod:

```bash
kubectl run ubuntu \
-n wordpress \
-it --rm \
--image=ubuntu:24.04 -- bash
```

Install tools:

```bash
apt update
apt install dnsutils default-mysql-client -y
```

Test DNS:

```bash
nslookup mysql-0.mysql.wordpress.svc.cluster.local
```

---

### 7. Connect to MySQL

```bash
mysql -u root \
-h mysql-0.mysql.wordpress.svc.cluster.local \
-p
```

Verify:

```sql
SHOW DATABASES;
```

Expected:

```text
wordpress
information_schema
mysql
performance_schema
```

---

### 8. Create WordPress PVC

```bash
kubectl apply -f wordpress-pvc.yaml
```

Verify:

```bash
kubectl get pvc -n wordpress
```

---

### 9. Deploy WordPress

```bash
kubectl apply -f wordpress-deploy.yaml
```

Verify:

```bash
kubectl get pods -n wordpress
```

---

### 10. Expose WordPress

```bash
kubectl apply -f wordpress-svc.yaml
```

Verify:

```bash
kubectl get svc -n wordpress
```

---

### 11. Access WordPress

Get Node IP:

```bash
kubectl get nodes -o wide
```

Open:

```text
http://<NodeIP>:30080
```

Complete the WordPress setup.

---

### 12. Verify Persistent Volumes

```bash
kubectl get pvc -n wordpress
```

Expected:

```text
mysql-data-mysql-0
wordpress-pvc
```

---

### 13. Verify MySQL Persistence

Delete the pod:

```bash
kubectl delete pod mysql-0 -n wordpress
```

Wait for recreation:

```bash
kubectl get pods -n wordpress -w
```

Reconnect to MySQL and verify data still exists.

---

### 14. Verify WordPress Persistence

Delete WordPress pod:

```bash
kubectl delete pod -l app=wordpress -n wordpress
```

A new pod is automatically created.

Website data remains available because of PVC.

---

### 15. Scale StatefulSet

Scale MySQL replicas:

```bash
kubectl scale sts mysql --replicas=3 -n wordpress
```

Verify:

```bash
kubectl get pods -n wordpress
```

Expected:

```text
mysql-0
mysql-1
mysql-2
```

Check DNS:

```bash
nslookup mysql-1.mysql.wordpress.svc.cluster.local
nslookup mysql-2.mysql.wordpress.svc.cluster.local
```

Verify PVCs:

```bash
kubectl get pvc -n wordpress
```

Expected:

```text
mysql-data-mysql-0
mysql-data-mysql-1
mysql-data-mysql-2
```

---

## Cleanup

```bash
kubectl delete ns wordpress
```

---

## Learning Outcomes

After completing this lab, you will understand:

* StatefulSet
* Headless Service
* Stable Pod DNS
* Persistent Volume Claims
* StorageClass
* Dynamic Volume Provisioning
* Pod Recovery
* StatefulSet Scaling
* WordPress and MySQL Integration
* Data Persistence in Kubernetes

---

## Repository Structure

```text
wordpress-statefulset-lab/
│
├── namespace.yaml
├── mysql-secret.yaml
├── mysql-headless-svc.yaml
├── mysql-sts.yaml
├── wordpress-pvc.yaml
├── wordpress-deploy.yaml
├── wordpress-svc.yaml
└── README.md
```
