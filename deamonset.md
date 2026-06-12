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


 kubectl get ns
   75  kubectl get kube-system
   76  kubectl get po  kube-system
   77  kubectl get po -n  kube-system
   78  kubectl get ds -n  kube-system
   79  vi daemonset.yaml
   80  55
   81  kubectl apply -f daemonset
   82  kubectl apply -f daemonset.yaml
   83  cat daemonset
   84  kubctl ds
   85  kubctl get ds
   86  kubectl get ds
   87  kubectl get ds,po -o wide

for node name 
90  vi nginx-deployment.yaml
   91  cat nginx-deployment.yaml
   92  kubectl create -f nginx-deployment.yaml
   93  kubectl get node
   94  kubectl get node --show-label
   95  kubectl get node --show-labels
   96  kubectl label node k3s-worker1.sece.com app=web-app
   97  kubectl label node k3s-worker2.sece.com name=sabaree
   98  kubectl get node --show-labels
   99  vi nginx-deployment.yaml
  100  kubectl apply -f nginx-deployment.yaml
  101  kubectl delete deployment.apps/nginx-deployment
  102  kubectl create -f nginx-deployment.yaml
  103  history
  104  cat  nginx-deployment.yaml
  105  history
