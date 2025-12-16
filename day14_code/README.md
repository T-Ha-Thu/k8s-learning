# **Day 14 : Taints and Tolerations in Kubernetes**
Hello #LinkedInCommunity and #TheCloudOpsCommunity ,

Today I want to share the Day 14 of #40DaysOfKubernetes journey with #Piyushsachdeva
Below are the lecture of Day 14 learning.

What is Taints?  
  
A taint is a rule on a Node that says “Do NOT schedule any Pods here unless they have special permission.”  
It is like putting a “No Entry” sign on the node.  
  
Real Life Example  
  
Think of a company:  
- You have a special room for GPU machines  
- Normal workers are NOT allowed to enter  
- Only employees with a special pass can enter  
Node Taint = No entry sign  
Pod Toleration = Special pass  
----------------------------------------------------------------------------------------------------------------------------------------------------------  
What is Tolerations?  

A toleration is a setting on a Pod that says “I am allowed to run on a node with this taint.”  
If a node has a taint (block), a pod normally cannot run there.  
A toleration gives the pod permission to bypass the block.  

Real Life Example  

Imagine a building:  
- One room has a sign: “Only Staff with Special Badge Allowed” (This is a Taint on the node)  
- If you have the special badge, you can enter (This is a Toleration on the pod)  
----------------------------------------------------------------------------------------------------------------------------------------------------------
LAB  
### Step by Step ###  
Install Cluster with [Day06 Video](https://github.com/GGWPEZGAMER/day06)  
1) Taint both of your worker nodes as below  
worker01--> gpu=true:NoSchedule , worker02--> gpu=false:NoSchedule
```bash
kubectl get nodes
```
```bash
kubectl taint node cka-cluster2-worker gpu=true:NoSchedule
kubectl taint node cka-cluster2-worker2 gpu=false:NoSchedule
```
```bash
kubectl describe node cka-cluster2-worker | grep Taints
kubectl describe node cka-cluster2-worker2 | grep Taints
kubectl describe node cka-cluster2-control-plane | grep Taints
```
2) Create a new pod with the image nginx and see why it's not getting scheduled on worker nodes and control plane nodes.  
```bash
kubectl run nginx --image=nginx
```
  
<img width="577" height="93" alt="1" src="https://github.com/user-attachments/assets/4e41d67a-f97f-4823-be32-67b4c360705e" />  
  
```bash
kubectl describe pod/nginx
```
  
<img width="1592" height="115" alt="2" src="https://github.com/user-attachments/assets/a122ed72-79aa-4db4-a5ff-2e2ce1236d02" />  
  
3) Create a toleration on the pod gpu=true:NoSchedule to match with the taint on worker01  
```bash
 kubectl run nginx --image=nginx --dry-run=client -o yaml > nginx.yaml
```
  
Added the below in the nginx.yaml under the restartPolicy: Always  
  
<img width="300" height="125" alt="Screenshot 2025-12-08 151251" src="https://github.com/user-attachments/assets/6ffdca32-efe1-4210-bd2c-d3b1b1f98447" />  
  
```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  tolerations:
  - key: "gpu"
    operator: "Equal"
    value: "true"
    effect: "NoSchedule"
status: {}
```
```bash
kubectl delete pod/nginx
```
```bash
kubectl apply -f nginx.yaml
```
```bash
kubectl get pod
```
  
<img width="1036" height="65" alt="3" src="https://github.com/user-attachments/assets/3785f4ae-a024-4182-9729-7df27f15d9bb" />   
  
4) Delete the taint on the control plane node  
```bash
kubectl taint nodes cka-cluster2-control-plane node-role.kubernetes.io/control-plane-
```
5) Create a new pod with the image redis , it should be scheduled on control plane node  
```bash
kubectl run redis --image=redis
```
  
<img width="1097" height="88" alt="4" src="https://github.com/user-attachments/assets/b0b4f1ec-461f-4f19-88ca-ad1a49c5ac09" />  
  
6) Add the taint back on the control plane node(the one that was removed)  
```bash
kubectl taint nodes cka-cluster2-control-plane node-role.kubernetes.io/control-plane:NoSchedule
```
  
Check out the today's lecture from Original Mentor #Piyushsachdeva  
Youtube - https://youtu.be/nwoS2tK2s6Q?si=K7HxDh2sPX8ZdKZv  
Github - https://github.com/piyushsachdeva/CKA-2024/blob/main/Resources/Day14/readme.md  
Special thanks to Piyush sachdeva &The CloudOps Community for the guidance!  
  
#Kubernetes #Docker # DevOps #TechLearning #Containerization #CloudOps  
#piyushsachdeva #TheCloudOpsCommunity  










