# Kubernetes Ingress Lab Exercises

## Lab 1: Create NGINX Deployment and Service

### Objective

Create an NGINX Deployment with 3 replicas and expose it using a ClusterIP Service.

### nginx-deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
      - name: nginx
        image: nginx:1.25

        ports:
        - containerPort: 80
```

### nginx-service.yaml

```yaml
apiVersion: v1
kind: Service

metadata:
  name: nginx-service

spec:
  selector:
    app: nginx

  ports:
  - port: 80
    targetPort: 80

  type: ClusterIP
```

### Tasks

```bash
kubectl apply -f nginx-deployment.yaml

kubectl apply -f nginx-service.yaml

kubectl get deploy

kubectl get pods

kubectl get svc
```

### Validation

```bash
kubectl get endpoints nginx-service
```

Expected:

* Service is mapped to all NGINX pods.

---

## Lab 2: Create PHPMyAdmin Deployment and Service

### Objective

Create PHPMyAdmin Deployment with 3 replicas and expose it using ClusterIP Service.

### php-deploy.yaml

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: php

spec:
  replicas: 3

  selector:
    matchLabels:
      app: php

  template:
    metadata:
      labels:
        app: php

    spec:
      containers:
      - name: php
        image: phpmyadmin/phpmyadmin

        ports:
        - containerPort: 80
```

### php-svc.yaml

```yaml
apiVersion: v1
kind: Service

metadata:
  name: php-service

spec:
  selector:
    app: php

  ports:
  - port: 80
    targetPort: 80

  type: ClusterIP
```

### Tasks

```bash
kubectl apply -f php-deploy.yaml

kubectl apply -f php-svc.yaml

kubectl get deploy

kubectl get svc
```

### Validation

```bash
kubectl get endpoints php-service
```

Expected:

* Service is mapped to all PHPMyAdmin pods.

---

## Lab 3: Create Path-Based Ingress

### Objective

Route traffic using URL paths.

### path-based-ingress.yaml

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress

metadata:
  name: path-based-ingress

spec:
  ingressClassName: nginx

  rules:
  - host: sebarees.com

    http:
      paths:

      - path: /nginx
        pathType: Prefix

        backend:
          service:
            name: nginx-service

            port:
              number: 80

      - path: /php
        pathType: Prefix

        backend:
          service:
            name: php-service

            port:
              number: 80
```

### Tasks

```bash
kubectl apply -f path-based-ingress.yaml

kubectl get ingress

kubectl describe ingress path-based-ingress
```

### Validation

```bash
curl http://sebarees.com/nginx

curl http://sebarees.com/php
```

Expected:

* /nginx opens NGINX page.
* /php opens PHPMyAdmin page.

---

## Lab 4: Configure Host File

### Objective

Map domain name to Ingress Controller IP.

### Tasks

Find Ingress Controller IP:

```bash
kubectl get svc -n ingress-nginx
```

Example:

```text
192.168.218.242
```

Edit hosts file:

```bash
sudo vi /etc/hosts
```

Add:

```text
192.168.218.242 sebarees.com
```

### Validation

```bash
ping sebarees.com
```

Expected:

```text
PING sebarees.com (192.168.218.242)
```

---

## Lab 5: Create Host-Based Ingress

### Objective

Route traffic using hostnames.

### host-based-ingress.yaml

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress

metadata:
  name: host-based-ingress

spec:
  ingressClassName: nginx

  rules:

  - host: nginx.sebarees.com
    http:
      paths:
      - path: /
        pathType: Prefix

        backend:
          service:
            name: nginx-service

            port:
              number: 80

  - host: php.sebarees.com
    http:
      paths:
      - path: /
        pathType: Prefix

        backend:
          service:
            name: php-service

            port:
              number: 80
```

### Tasks

```bash
kubectl apply -f host-based-ingress.yaml

kubectl get ingress
```

Update hosts file:

```text
192.168.218.242 nginx.sebarees.com
192.168.218.242 php.sebarees.com
```

### Validation

```bash
curl nginx.sebarees.com

curl php.sebarees.com
```

Expected:

* nginx.sebarees.com opens NGINX.
* php.sebarees.com opens PHPMyAdmin.

---

## Lab 6: Create Kubernetes Secret

### Objective

Store MySQL password securely.

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

Expected:

* Secret created successfully.
* Password stored securely.

---

## Lab 7: Delete Resources

### Objective

Clean up all resources.

### Tasks

```bash
kubectl delete ingress --all

kubectl delete svc --all

kubectl delete deploy --all

kubectl delete secret mysql-secret
```

### Validation

```bash
kubectl get all

kubectl get ingress

kubectl get secret
```

Expected:

* Resources removed successfully.

---

## Common Verification Commands

```bash
kubectl get deploy

kubectl get pods

kubectl get svc

kubectl get ingress

kubectl get endpoints

kubectl get secret

kubectl describe ingress <ingress-name>

kubectl describe svc <service-name>

kubectl describe secret <secret-name>
```

## Learning Outcomes

* Created Deployments
* Exposed applications using Services
* Configured Path-Based Ingress
* Configured Host-Based Ingress
* Created Kubernetes Secrets
* Tested Ingress Controller Routing
* Verified Service Endpoints
* Practiced Kubernetes Resource Management

```
```
