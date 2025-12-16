# **Day 8 : Kubernetes Deployment, Replication Controller and ReplicaSet Explained**  
  
Hello #LinkedInCommunity and #TheCloudOpsCommunity ,  
Today I want to share the Day 8 of #40DaysOfKubernetes journey with #Piyushsachdeva .  
Below are the lecture of Day 8 learning.  
  
In this exercise, you will create a Deployment with multiple replicas.  
After inspecting the Deployment, you will update its Pod template.   
You will also be able to use the rollout history to roll back to a previous revision.  
  
Installing KIND step by step  
Installing From Release Binaries  
On Linux:  
```bash
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.30.0/kind-linux-amd64
```
```bash
chmod +x ./kind
```
```bash
sudo mv ./kind /usr/local/bin/kind
```
Creating Cluster  
```bash
kind create cluster --image kindest/node:v1.29.12@sha256:62c0672ba99a4afd7396512848d6fc382906b8f33349ae68fb1dbfe549f70dec --name cka-cluster1
```
Reference link - https://kind.sigs.k8s.io/docs/user/quick-start/#installing-from-release-binaries  
Kind image link - https://github.com/kubernetes-sigs/kind/releases  
To check created cluster information  
```bash
kubectl cluster-info --context kind-cka-cluster1
```
To check nodes  
```bash
kubectl get nodes
```
Create a new Replicaset based on the nginx image with 3 replicas  
```bash
kubectl explain rc
```
```bash
vim nginx.yaml
```
```yaml
apiVersion: v1
kind: ReplicaSet
metadata:
  name: nginx-replicas
  labels:
    env: demo
spec:
  template:
    metadata:
      labels:
        env: demo
      name: nginx-replicas
    spec:    
      containers:
      - image: nginx
        name: nginx-replicas
  replicas: 4
  selector:
    matchLabels:
      env: demo
```
```bash
kubectl apply -f nginx.yaml
```
```bash
kubectl get pods
```
```bash
kubectl get rs
```
```bash
kubectl describe pod nginx-replicas
```
Changing replicas number 3 to 4 from yaml  
```bash
kubectl delete rs/nginx-rs
```
```bash
kubectl edit rs/nginx-rs
```
Update the replicas to 6 from the command line  
```bash
kubectl scale --replicas=6 rs/nginx-rs
```
Creating Deployment  
```bash
vim deployment.yaml
```
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
  labels: 
    env: demo
    tier: backend
spec:
  template:
    metadata:
      labels:
        app: v1
        env: demo
      name: nginx
    spec:    
      containers:
      - image: nginx:1.23.0
        name: nginx
  replicas: 4
  selector:
    matchLabels:
      env: demo
```
```bash
kubectl apply -f deployment.yaml
```
```bash
kubectl get deploy/nginx
```
Changing nginx image for deployment  
```bash
kubectl set image deploy/nginx \ nginx=nginx:1.23.4
```
To rollout history  
```bash
kubectl rollout history deploy/nginx
```
To rollback   
```bash
kubectl rollout undo deploy/nginx
```
Generating YAML file  
```bash
kubectl create deploy deploy/nginx-new --dry-run=client -o yaml > deploy.yaml
```
Check out the today's lecture from Original Mentor #Piyushsachdeva  
Youtube - https://youtu.be/oe2zjRb51F0?si=ZPmGR3L2PzxYiaW6  
Github - https://github.com/piyushsachdeva/CKA-2024/blob/main/Resources/Day08/task.md  
  
#Kubernetes #Docker # DevOps #TechLearning #Containerization #CloudOps  
#piyushsachdeva #TheCloudOpsCommunity  
