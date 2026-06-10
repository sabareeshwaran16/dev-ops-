# Kubernetes Lab: Understanding StatefulSet and Headless Service

## Objective

In this lab, you will learn:

* What a StatefulSet is
* Why Stateful Applications need StatefulSets
* What a Headless Service is
* How StatefulSet and Headless Service work together
* Stable Pod Identity
* Stable DNS Names
* Dynamic PVC Provisioning
* Scaling StatefulSets

---

## Architecture

```text
                Headless Service
                      mysql
                         |
     -----------------------------------------
     |                  |                    |
  mysql-0           mysql-1             mysql-2
     |                  |                    |
 PVC-0              PVC-1               PVC-2
```

---

# What is a Stateful Application?

A Stateful Application stores data and requires:

* Persistent Storage
* Stable Network Identity
* Ordered Deployment
* Ordered Scaling

Examples:

* MySQL
* PostgreSQL
* MongoDB
* Redis
* Kafka

---

# Why Not Use Deployment?

Deployment creates interchangeable pods.

Example:

```text
mysql-7f8c9d
mysql-a92bc3
mysql-f82cd1
```

If a pod restarts:

```text
Old Pod → Deleted
New Pod → Created
```

Pod names change.

For databases this is not desirable because:

* Data must persist
* Identity must remain stable

---

# What is StatefulSet?

StatefulSet is a Kubernetes workload object designed for Stateful Applications.

Example:

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
```

Features:

* Stable Pod Names
* Stable Storage
* Stable DNS
* Ordered Creation
* Ordered Deletion

---

# StatefulSet Pod Naming

When replicas = 3

```text
mysql-0
mysql-1
mysql-2
```

These names never change.

If mysql-0 crashes:

```text
mysql-0 → Deleted
mysql-0 → Recreated
```

Same name is retained.

---

# What is a Headless Service?

A Headless Service is a Service without a ClusterIP.

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql

spec:
  clusterIP: None

  selector:
    app: mysql

  ports:
  - port: 3306
```

Notice:

```yaml
clusterIP: None
```

This makes it a Headless Service.

---

# Why StatefulSet Needs Headless Service

StatefulSet pods require individual DNS names.

Headless Service provides DNS records for each pod.

Example:

```text
mysql-0.mysql
mysql-1.mysql
mysql-2.mysql
```

Format:

```text
<pod-name>.<service-name>
```

---

# Task 1: Create Namespace

namespace.yaml

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: stateful-demo
```

Apply:

```bash
kubectl apply -f namespace.yaml
```

---

# Task 2: Create Headless Service

mysql-headless-svc.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql
  namespace: stateful-demo

spec:
  clusterIP: None

  selector:
    app: mysql

  ports:
  - port: 3306
```

Apply:

```bash
kubectl apply -f mysql-headless-svc.yaml
```

Validate:

```bash
kubectl get svc -n stateful-demo
```

Expected:

```text
NAME    TYPE        CLUSTER-IP
mysql   ClusterIP   None
```

---

# Task 3: Create StatefulSet

mysql-sts.yaml

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
  namespace: stateful-demo

spec:
  serviceName: mysql

  replicas: 1

  selector:
    matchLabels:
      app: mysql

  template:
    metadata:
      labels:
        app: mysql

    spec:
      containers:
      - name: mysql
        image: nginx

        ports:
        - containerPort: 80

        volumeMounts:
        - name: data
          mountPath: /data

  volumeClaimTemplates:
  - metadata:
      name: data

    spec:
      accessModes:
      - ReadWriteOnce

      storageClassName: local-path

      resources:
        requests:
          storage: 1Gi
```

Apply:

```bash
kubectl apply -f mysql-sts.yaml
```

---

# Task 4: Verify StatefulSet

```bash
kubectl get sts -n stateful-demo
```

```bash
kubectl get pods -n stateful-demo
```

Expected:

```text
mysql-0
```

---

# Task 5: Verify PVC Creation

```bash
kubectl get pvc -n stateful-demo
```

Expected:

```text
data-mysql-0
```

Observe:

```text
StatefulSet automatically created PVC
```

using:

```yaml
volumeClaimTemplates
```

---

# Task 6: Verify DNS

Launch Test Pod

```bash
kubectl run testpod \
-n stateful-demo \
-it --rm \
--image=busybox -- sh
```

Inside pod:

```bash
nslookup mysql-0.mysql.stateful-demo.svc.cluster.local
```

Expected:

```text
Name:
mysql-0.mysql.stateful-demo.svc.cluster.local

Address:
<Pod-IP>
```

---

# Task 7: Verify Persistence

Create file:

```bash
kubectl exec -it mysql-0 -n stateful-demo -- sh
```

```bash
echo "hello" > /data/test.txt
```

Exit.

Delete Pod:

```bash
kubectl delete pod mysql-0 -n stateful-demo
```

Wait:

```bash
kubectl get pods -n stateful-demo -w
```

Check file again:

```bash
kubectl exec -it mysql-0 -n stateful-demo -- cat /data/test.txt
```

Expected:

```text
hello
```

Data persists because PVC remains.

---

# Task 8: Scale StatefulSet

Scale:

```bash
kubectl scale sts mysql \
--replicas=3 \
-n stateful-demo
```

Verify:

```bash
kubectl get pods -n stateful-demo
```

Expected:

```text
mysql-0
mysql-1
mysql-2
```

Verify PVCs:

```bash
kubectl get pvc -n stateful-demo
```

Expected:

```text
data-mysql-0
data-mysql-1
data-mysql-2
```

---

# Learning Outcomes

After completing this lab, you will understand:

* Stateful Applications
* StatefulSet
* Headless Service
* Stable DNS
* Stable Pod Identity
* Persistent Volumes
* Dynamic PVC Provisioning
* StatefulSet Scaling
* Pod Recovery and Persistence
