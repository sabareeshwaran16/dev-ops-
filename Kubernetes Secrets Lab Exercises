# Kubernetes Secrets Lab Exercises

## Lab 1: Create a Generic Secret Using YAML

### Objective

Create a Secret to store a MySQL root password.

### mysql-secret.yaml

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysql-secret

type: Opaque

stringData:
  MYSQL_ROOT_PASSWORD: abcd
```

### Tasks

```bash
kubectl apply -f mysql-secret.yaml

kubectl get secret

kubectl describe secret mysql-secret
```

### Validation

```bash
kubectl get secret mysql-secret
```

Expected:

```text
NAME           TYPE     DATA
mysql-secret   Opaque   1
```

---

# Lab 2: View Secret Details

### Objective

Verify Secret creation.

### Tasks

```bash
kubectl describe secret mysql-secret
```

### Validation

Expected:

```text
Name:         mysql-secret
Type:         Opaque

Data
====
MYSQL_ROOT_PASSWORD: 4 bytes
```

---

# Lab 3: View Secret in YAML Format

### Objective

Check how Kubernetes stores Secret data.

### Tasks

```bash
kubectl get secret mysql-secret -o yaml
```

### Validation

Expected:

```yaml
data:
  MYSQL_ROOT_PASSWORD: YWJjZA==
```

Note:

```text
abcd -> YWJjZA==
```

(Base64 Encoded)

---

# Lab 4: Decode Secret Value

### Objective

Decode Base64 encoded Secret.

### Tasks

```bash
echo YWJjZA== | base64 -d
```

### Validation

Expected:

```text
abcd
```

---

# Lab 5: Create Secret Using Command Line

### Objective

Create Secret without YAML.

### Tasks

```bash
kubectl create secret generic mysql-secret-cli \
--from-literal=MYSQL_ROOT_PASSWORD=abcd
```

### Validation

```bash
kubectl get secret
```

Expected:

```text
mysql-secret
mysql-secret-cli
```

---

# Lab 6: Use Secret in Deployment

### Objective

Inject Secret into a Pod as an Environment Variable.

### mysql-deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: mysql-deploy

spec:
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
        image: mysql:latest

        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: MYSQL_ROOT_PASSWORD

        ports:
        - containerPort: 3306
```

### Tasks

```bash
kubectl apply -f mysql-deployment.yaml

kubectl get deploy

kubectl get pods
```

### Validation

```bash
kubectl describe pod <pod-name>
```

Expected:

```text
MYSQL_ROOT_PASSWORD loaded from Secret
```

---

# Lab 7: Create Multiple Secrets

### Objective

Store username and password.

### db-secret.yaml

```yaml
apiVersion: v1
kind: Secret

metadata:
  name: db-secret

type: Opaque

stringData:
  MYSQL_USER: admin
  MYSQL_PASSWORD: abcd
```

### Tasks

```bash
kubectl apply -f db-secret.yaml

kubectl get secret
```

### Validation

```bash
kubectl describe secret db-secret
```

Expected:

```text
MYSQL_USER
MYSQL_PASSWORD
```

---

# Lab 8: Delete Secret

### Objective

Remove Secret from Cluster.

### Tasks

```bash
kubectl delete secret mysql-secret

kubectl delete secret mysql-secret-cli

kubectl delete secret db-secret
```

### Validation

```bash
kubectl get secret
```

Expected:

```text
No resources found
```

---

# Common Verification Commands

```bash
kubectl get secret

kubectl describe secret <secret-name>

kubectl get secret <secret-name> -o yaml

kubectl get pods

kubectl describe pod <pod-name>
```

---

# Learning Outcomes

* Created Kubernetes Secrets
* Stored MySQL Password Securely
* Viewed Secret Details
* Understood Base64 Encoding
* Created Secrets Using YAML
* Created Secrets Using CLI
* Injected Secrets into Deployments
* Managed Multiple Secret Keys
* Deleted Secrets Safely

```
```
