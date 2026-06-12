# 🧪 Kubernetes ConfigMap Lab

## 🎯 Objective

Learn how to:

* Create a ConfigMap
* Inject configuration into Pods
* Verify configuration inside running containers
* Update ConfigMaps
* Understand ConfigMap behavior

---

# What is a ConfigMap?

A ConfigMap is a Kubernetes object used to store non-confidential configuration data.

Instead of hardcoding configuration values inside application code, we store them in a ConfigMap.

Examples:

* Environment Name
* Application Mode
* Log Level
* API Endpoints
* Feature Flags

---

# Architecture

```text
ConfigMap
    │
    ▼
Deployment
    │
    ▼
Pod
    │
    ▼
Environment Variables / Files
```

---

# Step 1: Create ConfigMap

Create a file:

```bash
vi configmap.yaml
```

## configmap.yaml

```yaml
apiVersion: v1
kind: ConfigMap

metadata:
  name: app-config

data:

  # Application color
  APP_COLOR: "blue"

  # Application environment
  APP_MODE: "production"

  # Logging level
  LOG_LEVEL: "debug"
```

---

## Apply ConfigMap

```bash
kubectl apply -f configmap.yaml
```

---

## Verify ConfigMap

```bash
kubectl get configmap
```

Expected:

```text
NAME         DATA   AGE
app-config   3      10s
```

Detailed view:

```bash
kubectl describe configmap app-config
```

---

# Step 2: Create Deployment

Create file:

```bash
vi deployment.yaml
```

## deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: configmap-demo

spec:

  # Number of pod replicas
  replicas: 2

  selector:
    matchLabels:
      app: configmap-demo

  template:
    metadata:
      labels:
        app: configmap-demo

    spec:
      containers:

      - name: app-container

        # Busybox image
        image: busybox

        # Keep container alive
        command:
        - sh
        - -c
        - |
          while true
          do
            env
            sleep 3600
          done

        env:

        # Read APP_COLOR from ConfigMap
        - name: APP_COLOR
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: APP_COLOR

        # Read APP_MODE from ConfigMap
        - name: APP_MODE
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: APP_MODE

        # Read LOG_LEVEL from ConfigMap
        - name: LOG_LEVEL
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: LOG_LEVEL
```

---

## Deploy

```bash
kubectl apply -f deployment.yaml
```

---

## Verify Deployment

```bash
kubectl get deployment
```

```bash
kubectl get pods
```

Expected:

```text
NAME                              READY
configmap-demo-xxxxxxxxxx-abcde   1/1
configmap-demo-xxxxxxxxxx-fghij   1/1
```

---

# Step 3: Validate Inside Pod

Get pod name:

```bash
kubectl get pods
```

Enter pod:

```bash
kubectl exec -it <pod-name> -- sh
```

---

## Verify APP Variables

```bash
env | grep APP
```

Expected:

```text
APP_COLOR=blue
APP_MODE=production
```

---

## Verify LOG Variable

```bash
env | grep LOG
```

Expected:

```text
LOG_LEVEL=debug
```

Exit:

```bash
exit
```

---

# Step 4: Update ConfigMap

Edit ConfigMap:

```bash
kubectl edit configmap app-config
```

Change:

```yaml
APP_COLOR: "green"
```

Save and exit.

---

# Verify ConfigMap

```bash
kubectl describe configmap app-config
```

Expected:

```text
APP_COLOR=green
```

---

# Important Concept

Environment variables are loaded only when the container starts.

Therefore:

```text
ConfigMap Updated
       │
       ▼
Running Pod
       │
       ▼
NO CHANGE
```

Pods continue using old values.

---

# Restart Deployment

```bash
kubectl rollout restart deployment configmap-demo
```

Watch:

```bash
kubectl get pods -w
```

---

# Validate Again

Enter new pod:

```bash
kubectl exec -it <new-pod-name> -- sh
```

```bash
env | grep APP
```

Expected:

```text
APP_COLOR=green
APP_MODE=production
```

---

# Bonus Lab: Mount ConfigMap as Files

Instead of environment variables, ConfigMap values can be mounted as files.

---

## Deployment Example

```yaml
volumeMounts:

- name: config-volume
  mountPath: /config

volumes:

- name: config-volume
  configMap:
    name: app-config
```

---

# Validate

Enter pod:

```bash
kubectl exec -it <pod-name> -- sh
```

List files:

```bash
ls /config
```

Expected:

```text
APP_COLOR
APP_MODE
LOG_LEVEL
```

View values:

```bash
cat /config/APP_COLOR
```

Output:

```text
blue
```

```bash
cat /config/APP_MODE
```

Output:

```text
production
```

```bash
cat /config/LOG_LEVEL
```

Output:

```text
debug
```

---

# Environment Variables vs Mounted Files

| Feature            | Environment Variables | Mounted Files |
| ------------------ | --------------------- | ------------- |
| Easy Access        | Yes                   | Yes           |
| Pod Restart Needed | Yes                   | No            |
| Dynamic Updates    | No                    | Yes           |
| Most Common        | Yes                   | Yes           |

---

# Useful Commands

```bash
kubectl get configmap

kubectl describe configmap app-config

kubectl get deployment

kubectl get pods

kubectl logs <pod-name>

kubectl rollout restart deployment configmap-demo

kubectl delete deployment configmap-demo

kubectl delete configmap app-config
```

---

# Key Learning

* ConfigMap stores non-sensitive configuration.
* ConfigMap can be consumed as environment variables.
* ConfigMap can be mounted as files.
* Environment variables require pod restart after updates.
* Mounted files update automatically.
* ConfigMaps help separate configuration from application code.

---

# Memory Trick

```text
ConfigMap
    │
    ├── Environment Variable
    │        (Restart Required)
    │
    └── Mounted File
             (Auto Updated)
```
