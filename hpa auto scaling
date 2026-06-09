# Kubernetes Horizontal Pod Autoscaler (HPA) Lab Exercises

## Lab 1: Create NGINX Deployment with Resource Limits

### Objective

Create an NGINX Deployment with CPU and Memory requests/limits that can be monitored and scaled by HPA.

### nginx-deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx

spec:
  replicas: 1

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

        resources:
          requests:
            cpu: 100m
            memory: 128Mi

          limits:
            cpu: 500m
            memory: 512Mi
```

### Tasks

```bash
kubectl apply -f nginx-deployment.yaml

kubectl get deploy

kubectl get pods
```

### Validation

```bash
kubectl describe deploy nginx
```

Expected:

* Deployment created successfully.
* Resource requests and limits are visible.

---

## Lab 2: Expose Deployment Using Service

### Objective

Create a ClusterIP Service for the NGINX Deployment.

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
kubectl apply -f nginx-service.yaml

kubectl get svc
```

### Validation

```bash
kubectl get endpoints nginx-service
```

Expected:

* Service mapped to NGINX Pod.

---

## Lab 3: Create Horizontal Pod Autoscaler

### Objective

Automatically scale pods based on CPU and Memory utilization.

### nginx-hpa.yaml

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler

metadata:
  name: nginx-hpa

spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx

  minReplicas: 1
  maxReplicas: 10

  metrics:

  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50

  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 50
```

### Tasks

```bash
kubectl apply -f nginx-hpa.yaml

kubectl get hpa
```

### Validation

```bash
kubectl describe hpa nginx-hpa
```

Expected:

* HPA created successfully.
* Min replicas = 1
* Max replicas = 10

---

## Lab 4: Verify Metrics Server

### Objective

Verify CPU and Memory metrics are available.

### Tasks

```bash
kubectl top nodes

kubectl top pods
```

### Validation

Expected:

```text
NAME            CPU(cores)   MEMORY(bytes)
worker-node     120m         1100Mi
```

---

## Lab 5: Generate CPU Load

### Objective

Increase CPU utilization and trigger HPA scaling.

### Tasks

Get Pod Name:

```bash
kubectl get pods
```

Access Pod:

```bash
kubectl exec -it <pod-name> -- /bin/bash
```

Generate CPU Load:

```bash
while true
do
  dd if=/dev/zero of=/dev/null
done
```

Monitor:

```bash
kubectl top pods

kubectl get hpa -w
```

### Validation

Expected:

```text
TARGETS
120%/50%
```

* CPU exceeds threshold.
* HPA begins scaling.

---

## Lab 6: Validate Scale Up

### Objective

Verify that HPA increases the number of Pods.

### Tasks

```bash
kubectl get pods -w
```

```bash
kubectl get deploy
```

### Validation

Expected:

```text
NAME    READY
nginx   3/3
```

* Deployment scaled from 1 Pod to multiple Pods.

---

## Lab 7: Generate Memory Load

### Objective

Increase memory utilization and trigger scaling.

### Tasks

Access Pod:

```bash
kubectl exec -it <pod-name> -- /bin/bash
```

Generate Memory Load:

```bash
dd if=/dev/zero of=/tmp/memfile bs=1M count=400
```

Verify:

```bash
ls -lh /tmp/memfile
```

Monitor:

```bash
kubectl top pods

kubectl get hpa -w
```

### Validation

Expected:

```text
CPU: 30%
MEMORY: 85%
```

* Memory exceeds threshold.
* HPA scales deployment.

---

## Lab 8: Stop Load

### Objective

Remove CPU and Memory load.

### Tasks

Stop CPU Load:

```bash
Ctrl + C
```

Delete Memory File:

```bash
rm -f /tmp/memfile
```

### Validation

```bash
kubectl top pods
```

Expected:

* CPU and Memory usage decrease.

---

## Lab 9: Validate Scale Down

### Objective

Verify HPA reduces Pods after load decreases.

### Tasks

```bash
kubectl get hpa -w

kubectl get pods -w
```

### Validation

Expected:

```text
3 Pods
↓
2 Pods
↓
1 Pod
```

* HPA returns to minimum replica count.

---

## Lab 10: Delete Resources

### Objective

Clean up HPA Lab resources.

### Tasks

```bash
kubectl delete hpa nginx-hpa

kubectl delete svc nginx-service

kubectl delete deploy nginx
```

### Validation

```bash
kubectl get all
```

Expected:

* All HPA lab resources removed.

---

## Common Verification Commands

```bash
kubectl get deploy

kubectl get pods

kubectl get svc

kubectl get hpa

kubectl top nodes

kubectl top pods

kubectl describe hpa nginx-hpa

kubectl describe deploy nginx

kubectl get events --sort-by=.metadata.creationTimestamp
```

## Learning Outcomes

* Created Deployment with Resource Requests and Limits
* Exposed Application Using Service
* Configured Horizontal Pod Autoscaler
* Monitored CPU and Memory Metrics
* Generated CPU Load Using dd
* Generated Memory Load Using dd
* Validated Automatic Scale Up
* Validated Automatic Scale Down
* Practiced Kubernetes Resource Monitoring and Autoscalinghpa
