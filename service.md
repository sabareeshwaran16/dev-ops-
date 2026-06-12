# Kubernetes Services Lab

## Objective

In this lab, you will learn:

* Kubernetes Services
* ClusterIP Service
* NodePort Service
* LoadBalancer Service
* MetalLB Installation and Configuration
* Endpoints
* Troubleshooting Service Issues
* Scaling Applications

---

# Cluster Details

| Node                 | IP Address      |
| -------------------- | --------------- |
| k3s-master.sece.com  | 192.168.218.129 |
| k3s-worker1.sece.com | 192.168.218.130 |
| k3s-worker2.sece.com | 192.168.218.131 |

---

# What is a Service?

A Service provides a stable network endpoint for accessing Pods.

Without a Service:

```
User → Pod
```

Problem:

* Pod IPs change when Pods restart.

With a Service:

```
User → Service → Pod
```

Benefits:

* Stable IP
* Load Balancing
* Service Discovery
* External Access

Check Services:

```bash
kubectl get svc
```

---

# ClusterIP Service

## Definition

ClusterIP is the default Service type.

It exposes an application only inside the Kubernetes cluster.

---

## Create Deployment

```bash
kubectl create deployment nginx --image=nginx
```

---

## Expose Deployment

```bash
kubectl expose deployment nginx --port=80
```

---

## Verify Service

```bash
kubectl get svc
```

Example:

```text
NAME         TYPE        CLUSTER-IP
nginx        ClusterIP   10.43.10.20
```

---

## Check Endpoints

```bash
kubectl get endpoints
```

Example:

```text
nginx    10.42.0.12:80
```

---

## Traffic Flow

```
Pod
 ↑
 |
ClusterIP Service
```

Only Pods inside the cluster can access the service.

---

# NodePort Service

## Definition

NodePort exposes an application outside the cluster using:

* Node IP
* NodePort

---

## Create Deployment

```bash
kubectl create deployment nginx --image=nginx
```

---

## Expose as NodePort

```bash
kubectl expose deployment nginx --type=NodePort --port=80
```

---

## Verify

```bash
kubectl get svc
```

Example:

```text
NAME    TYPE       PORT(S)
nginx   NodePort  80:30080/TCP
```

---

## Access Application

```text
http://192.168.218.129:30080
```

or

```text
http://192.168.218.130:30080
```

or

```text
http://192.168.218.131:30080
```

---

## Traffic Flow

```
Browser
   |
NodeIP:30080
   |
NodePort Service
   |
Pod
```

---

## Verify Endpoints

```bash
kubectl get endpoints
```

```bash
kubectl describe svc nginx
```

---

# MetalLB

## What is MetalLB?

MetalLB provides External IP addresses for LoadBalancer Services in on-premises Kubernetes clusters.

Without MetalLB:

```text
EXTERNAL-IP = <pending>
```

With MetalLB:

```text
EXTERNAL-IP = 192.168.218.241
```

---

# Install MetalLB

```bash
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.15.3/config/manifests/metallb-native.yaml
```

---

## Verify Installation

```bash
kubectl get pods -n metallb-system
```

---

# Troubleshooting MetalLB

## Error 1

```text
failed calling webhook
no endpoints available for service "metallb-webhook-service"
```

### Cause

MetalLB controller is not running.

---

## Error 2

```text
secret "memberlist" not found
```

### Fix

Create memberlist secret:

```bash
kubectl create secret generic memberlist \
-n metallb-system \
--from-literal=secretkey="$(openssl rand -base64 128)"
```

Restart MetalLB:

```bash
kubectl delete pod --all -n metallb-system
```

Verify:

```bash
kubectl get pods -n metallb-system
```

Expected:

```text
controller      1/1 Running
speaker         1/1 Running
speaker         1/1 Running
```

---

# Configure IPAddressPool

## Create IP Pool

```bash
vi ippool.yaml
```

```yaml
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: first-pool
  namespace: metallb-system

spec:
  addresses:
  - 192.168.218.240-192.168.218.250
```

Apply:

```bash
kubectl apply -f ippool.yaml
```

Verify:

```bash
kubectl get ipaddresspool -n metallb-system
```

---

# Configure L2Advertisement

Create:

```bash
vi l2ad.yaml
```

```yaml
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: example
  namespace: metallb-system
```

Apply:

```bash
kubectl apply -f l2ad.yaml
```

Verify:

```bash
kubectl get l2advertisement -n metallb-system
```

---

# LoadBalancer Service

## Definition

LoadBalancer Service exposes an application using an External IP.

---

## Create Service

```bash
kubectl expose deployment nginx --type=LoadBalancer --port=80
```

---

## Existing Service Error

```text
services "nginx" already exists
```

### Fix

Delete old Service:

```bash
kubectl delete svc nginx
```

Recreate:

```bash
kubectl expose deployment nginx --type=LoadBalancer --port=80
```

---

## Verify

```bash
kubectl get svc
```

Example:

```text
NAME    TYPE           EXTERNAL-IP
nginx   LoadBalancer   192.168.218.241
```

---

## Access Application

```text
http://192.168.218.241
```

---

## Traffic Flow

```
Browser
   |
192.168.218.241
   |
LoadBalancer Service
   |
Pod
```

---

# Endpoints

## Definition

Endpoints show which Pods are connected to a Service.

Check:

```bash
kubectl get endpoints
```

Example:

```text
nginx    10.42.2.40:80
```

Flow:

```
Service
   |
Endpoint
   |
Pod
```

---

# No Endpoints Issue

Example:

```text
php-service
```

No IP displayed.

---

## Cause

Service selector does not match Pod labels.

---

## Verify Service

```bash
kubectl describe svc php-service
```

---

## Verify Pod Labels

```bash
kubectl get pods --show-labels
```

---

## Fix

Pod Label:

```yaml
app: php
```

Service Selector:

```yaml
selector:
  app: php
```

Both must match exactly.

---

# Scaling Applications

## Scale Up

```bash
kubectl scale deployment nginx --replicas=3
```

---

## Scale Down

```bash
kubectl scale deployment nginx --replicas=1
```

---

## Verify

```bash
kubectl get pods
```

---

# Useful Commands

```bash
kubectl get pods

kubectl get svc

kubectl get endpoints

kubectl get nodes

kubectl describe pod <pod-name>

kubectl describe svc <service-name>

kubectl logs <pod-name>

kubectl get events --sort-by=.lastTimestamp

kubectl get pods --show-labels
```

---

# Quick Summary

| Service Type | Access         |
| ------------ | -------------- |
| ClusterIP    | Inside Cluster |
| NodePort     | NodeIP + Port  |
| LoadBalancer | External IP    |

---

# Key Concepts

| Component    | Purpose                                  |
| ------------ | ---------------------------------------- |
| Service      | Provides stable access to Pods           |
| ClusterIP    | Internal communication                   |
| NodePort     | External access via Node IP              |
| LoadBalancer | External access via External IP          |
| Endpoints    | Connected backend Pods                   |
| MetalLB      | Assigns External IPs in on-prem clusters |
