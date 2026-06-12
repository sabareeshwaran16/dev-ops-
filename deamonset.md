# Kubernetes DaemonSet and Node Labeling Lab

## Objective

In this lab, you will learn how to:

- Deploy an NGINX DaemonSet
- Verify DaemonSet behavior across cluster nodes
- View node labels
- Add custom labels to nodes
- Understand how labels are used for scheduling workloads

---

# What is a DaemonSet?

A DaemonSet ensures that a copy of a Pod runs on every node (or selected nodes) in a Kubernetes cluster.

### Common Use Cases

- Log Collection (Fluentd)
- Monitoring Agents (Node Exporter)
- Security Agents
- Storage Plugins
- Network Plugins

### DaemonSet Behavior

| Nodes | Pods Created |
|---------|-------------|
| 1 | 1 |
| 2 | 2 |
| 3 | 3 |

Whenever a new node joins the cluster, Kubernetes automatically creates a DaemonSet pod on that node.

---

# Step 1: Verify Cluster Information

Check namespaces:

```bash
kubectl get ns
```

Check pods in kube-system namespace:

```bash
kubectl get po -n kube-system
```

Check existing DaemonSets:

```bash
kubectl get ds -n kube-system
```

---

# Step 2: Create DaemonSet Manifest

Create the YAML file:

```bash
vi daemonset.yaml
```

Paste the following content:

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nginx-daemonset
  labels:
    app: nginx
    env: prod

spec:
  selector:
    matchLabels:
      app: nginx-app

  template:
    metadata:
      labels:
        app: nginx-app

    spec:
      containers:
      - name: nginx-container
        image: nginx:1.27
        ports:
        - containerPort: 80
```

---

# Step 3: Deploy the DaemonSet

Apply the manifest:

```bash
kubectl apply -f daemonset.yaml
```

Verify:

```bash
kubectl get ds
```

Expected output:

```text
NAME              DESIRED   CURRENT   READY
nginx-daemonset   3         3         3
```

---

# Step 4: Verify DaemonSet Pods

View DaemonSet and Pods:

```bash
kubectl get ds,po -o wide
```

Or:

```bash
kubectl get pods -o wide
```

You should see one NGINX pod running on each node.

---

# Step 5: View Cluster Nodes

List all nodes:

```bash
kubectl get node
```

Example:

```text
k3s-master.sece.com
k3s-worker1.sece.com
k3s-worker2.sece.com
```

---

# Step 6: View Existing Node Labels

Display node labels:

```bash
kubectl get node --show-labels
```

---

# Step 7: Add Custom Labels to Nodes

Add a label to Worker-1:

```bash
kubectl label node k3s-worker1.sece.com app=web-app
```

Add a label to Worker-2:

```bash
kubectl label node k3s-worker2.sece.com name=sabaree
```

---

# Step 8: Verify Labels

Check labels:

```bash
kubectl get node --show-labels
```

Example:

```text
k3s-worker1.sece.com  app=web-app
k3s-worker2.sece.com  name=sabaree
```

---

# Why Node Labels?

Node labels help schedule workloads onto specific nodes.

Example:

```yaml
nodeSelector:
  app: web-app
```

Kubernetes will schedule the Pod only on nodes having:

```text
app=web-app
```

---

# Verification Commands

Check DaemonSet:

```bash
kubectl get ds
```

Check Pods:

```bash
kubectl get pods -o wide
```

Check Nodes:

```bash
kubectl get node
```

Check Labels:

```bash
kubectl get node --show-labels
```

---

# Cleanup

Delete the DaemonSet:

```bash
kubectl delete -f daemonset.yaml
```

Or:

```bash
kubectl delete ds nginx-daemonset
```

Verify:

```bash
kubectl get ds
kubectl get pods
```

---

# Key Takeaways

- DaemonSet runs one Pod per node.
- New nodes automatically receive DaemonSet Pods.
- DaemonSets are commonly used for monitoring, logging, networking, and security tools.
- Node labels are key-value pairs attached to nodes.
- Labels help control where workloads are scheduled.
- Labels work with nodeSelector, Node Affinity, and Taints/Tolerations.
