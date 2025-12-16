Day 7 :  Pod In Kubernetes Explained | Imperative VS Declarative Way | YAML Tutorial  
  
Hello #LinkedInCommunity and #TheCloudOpsCommunity ,  
Today I want to share the Day 7 of #40DaysOfKubernetes journey with #Piyushsachdeva .Below are the lecture of Day 7 learning.  
  
### Step-by-Step Process ###  
Get Started  
  
1) Creating Kubernetes Cluster in Docker (Kind)  
```bash
kind create cluster --image kindest/node:v1.29.12@sha256:62c0672ba99a4afd7396512848d6fc382906b8f33349ae68fb1dbfe549f70dec --name cka-cluster1
```
reference link - https://kind.sigs.k8s.io/docs/user/quick-start/#installing-from-release-binaries  
  
2) To check Cluster is ready or not  
```bash
kubectl cluster-info --context kind-cka-cluster1
```
3) Creating an nginx pod through kubectl imperative way  
```bash
kubectl run nginx-pod --image=nginx:latest
```
```bash
kubectl get pods
```
```bash
kubectl explain pods
```
4) To create pod with yaml file  
```bash
vim pod.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels: 
    env: demo
    type: frontend
spec:
  containers:
  - name: nginx-container
    image: nginx
    ports:
    - containerPort: 80
```
```bash
kubectl create -f pod.yaml
```
5) To delete pod  
```bash
kubectl delete pod nginx-pod
```
6) To find the error of pod  
```bash
kubectl describe pod nginx-pod
```
7) To edit the pod  
```bash
kubectl edit pod nginx-pod
```
8) To login the pod  
```bash
kubectl exec -it nginx-pod -- sh
```
9) Dry run the pod  
```bash
kubectl run nginx --image=nginx --dry-run=client
```
10) Dry run the pod to get yaml format  
```bash
kubectl run nginx --image=nginx --dry-run=client -o yaml
```
11) Dry run the pod to get json format  
```bash
kubectl run nginx --image=nginx --dry-run=client -o json > pod-new.json
```
12) To get information of the pod  
```bash
kubectl get pods nginx-pod --show-labels
```
13) To get extended information of the pod  
```bash
kubectl get pods -o wide
```
  
Check out the today's lecture from Original Mentor #Piyushsachdeva  
Youtube - https://youtu.be/_f9ql2Y5Xcc?si=G9iib0eK5TF_bU8j  
Github - https://github.com/piyushsachdeva/CKA-2024/blob/main/Resources/Day07/readme.md  
  
#Kubernetes #Docker # DevOps #TechLearning #Containerization #CloudOps  
#piyushsachdeva #TheCloudOpsCommunity  
