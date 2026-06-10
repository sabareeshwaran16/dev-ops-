# Kubernetes Storage Volumes (EmptyDir, HostPath, PV & PVC)

## 1. EmptyDir Volume

### Definition

An `emptyDir` volume is created when a Pod starts and deleted when the Pod is deleted.

### YAML Example

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: emptydir-pod

spec:
  containers:

  # First container
  - name: container1
    image: busybox

    # Keep container running
    command: ["/bin/sh","-c","sleep 3600"]

    volumeMounts:
    - name: shared-volume       # Volume name
      mountPath: /data          # Mount inside container

  # Second container
  - name: container2
    image: busybox
    command: ["/bin/sh","-c","sleep 3600"]

    volumeMounts:
    - name: shared-volume       # Same volume
      mountPath: /shared        # Different mount path

  volumes:
  - name: shared-volume         # Volume definition
    emptyDir: {}                # Create temporary volume
```

---

## 2. HostPath Volume

### Definition

A `hostPath` volume mounts a directory from the worker node into a Pod.

### YAML Example

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: hostpath-nginx

spec:
  containers:
  - name: nginx
    image: nginx

    volumeMounts:
    - name: host-volume

      # Mount node directory inside container
      mountPath: /usr/share/nginx/html

  volumes:
  - name: host-volume

    hostPath:
      path: /data               # Directory on worker node

      # Ensure path exists as a directory
      type: Directory
```

### Worker Node Preparation

```bash
sudo mkdir -p /data
echo "Hello from Worker Node" > /data/index.html
```

---

## 3. PersistentVolume (PV)

### Definition

A PersistentVolume is the actual storage resource available to the cluster.

### PV YAML

```yaml
apiVersion: v1
kind: PersistentVolume

metadata:
  name: my-pv

spec:

  # Total storage available
  capacity:
    storage: 1Gi

  # Access mode
  accessModes:
  - ReadWriteOnce

  # What happens when PVC is deleted
  persistentVolumeReclaimPolicy: Retain

  # Storage location
  hostPath:
    path: /mnt/data
```

### Create Storage Directory

```bash
sudo mkdir -p /mnt/data
```

---

## 4. PersistentVolumeClaim (PVC)

### Definition

A PVC is a request for storage.

### PVC YAML

```yaml
apiVersion: v1
kind: PersistentVolumeClaim

metadata:
  name: my-pvc

spec:

  # Required access mode
  accessModes:
  - ReadWriteOnce

  resources:
    requests:

      # Request 1Gi storage
      storage: 1Gi
```

---

## 5. Pod Using PVC

### YAML Example

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: pvc-pod

spec:
  containers:
  - name: nginx
    image: nginx

    volumeMounts:
    - name: storage

      # Mount PVC inside container
      mountPath: /data

  volumes:
  - name: storage

    persistentVolumeClaim:

      # PVC name to use
      claimName: my-pvc
```

---

## Useful Commands

### Create Resources

```bash
kubectl apply -f emptydir.yaml
kubectl apply -f hostpath.yaml
kubectl apply -f pv.yaml
kubectl apply -f pvc.yaml
kubectl apply -f pod.yaml
```

### Verify

```bash
kubectl get pods
kubectl get pv
kubectl get pvc
```

### Detailed Information

```bash
kubectl describe pod emptydir-pod
kubectl describe pod hostpath-nginx
kubectl describe pv my-pv
kubectl describe pvc my-pvc
```

---

## Architecture

### EmptyDir

```text
Pod
├── Container1
├── Container2
└── EmptyDir
```

### HostPath

```text
Worker Node
└── /data
      │
      ▼
Pod
└── /usr/share/nginx/html
```

### PV + PVC

```text
Pod
 ↓
PVC
 ↓
PV
 ↓
Disk / NFS / Cloud Storage
```

---

## Comparison

| Feature               | EmptyDir       | HostPath      | PV/PVC          |
| --------------------- | -------------- | ------------- | --------------- |
| Persistent Storage    | No             | Yes           | Yes             |
| Survives Pod Deletion | No             | Yes           | Yes             |
| Shared Storage        | Within Pod     | Node Based    | Cluster Storage |
| Database Suitable     | No             | No            | Yes             |
| Production Use        | Temporary Data | Special Cases | Recommended     |

---

## Memory Trick

```text
emptyDir = Temporary Pod Storage

hostPath = Worker Node Storage

PV = Actual Storage

PVC = Storage Request

Pod → PVC → PV → Storage
```
