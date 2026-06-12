# Kubernetes Services Lab Notes

## Cluster Details

Nodes:

k3s-master.sece.com  → 192.168.218.129

k3s-worker1.sece.com → 192.168.218.130

k3s-worker2.sece.com → 192.168.218.131

---

# Service

Definition:
A Service provides a stable way to access Pods.

Flow:

User → Service → Pod

Check:

kubectl get svc

---

# ClusterIP Service

Definition:
ClusterIP exposes an application only inside the Kubernetes cluster.

Create Deployment:

kubectl create deployment nginx --image=nginx

Expose as ClusterIP:

kubectl expose deployment nginx --port=80

Check:

kubectl get svc

Output:

TYPE = ClusterIP

Check Endpoints:

kubectl get endpoints

Access:

Only from inside cluster.

Flow:

Pod
↑
|
ClusterIP Service

---

# NodePort Service

Definition:
NodePort exposes an application outside the cluster using Node IP and Port.

Create Deployment:

kubectl create deployment nginx --image=nginx

Expose as NodePort:

kubectl expose deployment nginx --type=NodePort --port=80

Check:

kubectl get svc

Example:

nginx NodePort 80:30080/TCP

Access:

http://192.168.218.129:30080

or

http://192.168.218.130:30080

or

http://192.168.218.131:30080

Flow:

Browser
|
NodeIP:30080
|
NodePort Service
|
Pod

Verify:

kubectl get endpoints

kubectl describe svc nginx

---

# MetalLB Installation

Definition:
MetalLB provides External IPs for LoadBalancer services in on-premise Kubernetes.

Install:

kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.15.3/config/manifests/metallb-native.yaml

Verify:

kubectl get pods -n metallb-system

---

# Issue Encountered

Error:

failed calling webhook

no endpoints available for service "metallb-webhook-service"

Reason:

MetalLB controller not running.

---

# Issue Encountered

Error:

secret "memberlist" not found

Fix:

kubectl create secret generic memberlist 
-n metallb-system 
--from-literal=secretkey="$(openssl rand -base64 128)"

Restart:

kubectl delete pod --all -n metallb-system

Verify:

kubectl get pods -n metallb-system

Expected:

controller 1/1 Running

speaker 1/1 Running

speaker 1/1 Running

---

# IPAddressPool

Definition:
Range of IP addresses assigned by MetalLB.

Create:

vi ippool.yaml

apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
name: first-pool
namespace: metallb-system

spec:
addresses:

* 192.168.218.240-192.168.218.250

Apply:

kubectl apply -f ippool.yaml

Verify:

kubectl get ipaddresspool -n metallb-system

---

# L2Advertisement

Definition:
Advertises MetalLB IPs to the local network.

Create:

vi l2ad.yaml

apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
name: example
namespace: metallb-system

Apply:

kubectl apply -f l2ad.yaml

Verify:

kubectl get l2advertisement -n metallb-system

---

# LoadBalancer Service

Definition:
LoadBalancer exposes an application using an External IP.

Expose:

kubectl expose deployment nginx --type=LoadBalancer --port=80

Issue:

services "nginx" already exists

Fix:

kubectl delete svc nginx

kubectl expose deployment nginx --type=LoadBalancer --port=80

Check:

kubectl get svc

Output:

nginx LoadBalancer 192.168.218.241

Access:

http://192.168.218.241

Flow:

Browser
|
192.168.218.241
|
LoadBalancer Service
|
Pod

---

# Endpoints

Definition:
Endpoints show which Pods are connected to a Service.

Check:

kubectl get endpoints

Example:

nginx 10.42.2.40:80

Meaning:

Service
|
V
Pod

---

# No Endpoints Issue

Output:

php-service

No IP displayed.

Reason:

Service selector does not match Pod labels.

Check Service:

kubectl describe svc php-service

Check Labels:

kubectl get pods --show-labels

Fix:

Pod Label:

app=php

Service Selector:

app=php

Both must match.

---

# Scaling

Increase Pods:

kubectl scale deployment nginx --replicas=3

Decrease Pods:

kubectl scale deployment nginx --replicas=1

Verify:

kubectl get pods

---

# Important Commands

kubectl get pods

kubectl get svc

kubectl get endpoints

kubectl get nodes

kubectl describe pod <pod-name>

kubectl describe svc <service-name>

kubectl logs <pod-name>

kubectl get events --sort-by=.lastTimestamp

kubectl get pods --show-labels



ClusterIP    → Inside Cluster

NodePort     → NodeIP + Port

LoadBalancer → External IP

Service      → Access Pods

Endpoints    → Connected Pods

MetalLB      → Gives External IP
