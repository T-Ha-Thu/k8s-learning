Day 13 : Static Pods, Manual Scheduling, Labels, and Selectors in Kubernetes  
Hello #LinkedInCommunity and #TheCloudOpsCommunity ,  
  
Today I want to share the Day 13 of #40DaysOfKubernetes journey with #Piyushsachdeva  
Below are the lecture of Day 13 learning.  

What is Static Pod?   
A Static Pod in Kubernetes is a special kind of pod that is managed directly by the kubelet on a specific node, rather than by the Kubernetes API server.  
Example of Yaml File  
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-static
  namespace: default
spec:
  containers:
  - name: nginx
    image: nginx:alpine
    ports:
    - containerPort: 80
```
You would save this file to /etc/kubernetes/manifests/nginx-static.yaml on a node. The kubelet automatically notices it and runs the pod.  
  
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  
What is Manual Scheduling?  
In Kubernetes, manual scheduling refers to the process where you explicitly assign a pod to a specific node, instead of letting the Kubernetes scheduler automatically decide which node the pod should run on.  
Example of Yaml File  
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-manual
spec:
  nodeName: node1
  containers:
  - name: nginx
    image: nginx:alpine
    ports:
    - containerPort: 80
```
Here, nodeName: node1 tells Kubernetes to run this pod only on the node named node1.    
The pod will not be scheduled by the Kubernetes scheduler; kubelet on that node takes care of it.  
  
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  
What is Labels and Selector?  
In Kubernetes, labels and selectors are closely related concepts used to organize, identify, and manage resources like Pods, Services, and Deployments. Let’s break it down clearly.  
Example of Yaml File  
```yaml
metadata:
  name: pod1
  labels:
    app: nginx
    env: production
```
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  
LAB  
### Step by Step ###  
Install Cluster with [Day06 Video](https://github.com/GGWPEZGAMER/day06)  
1) Create a pod and try to schedule it manually without the scheduler.  
Check First Cluster is running or not  
```bash
kubectl cluster-info
```
To get the cluster name
```bash
kubectl get nodes
```
To get inside the cluster
```bash
docker exec -it cka-cluster1-control-plane bash
```
Move the kube-scheduler.yaml to /tmp  
```bash
cd /etc/kubernetes/manifests
```
```bash
ls -lah
```
```bash
mv kube-scheduler.yaml /tmp
```
```bash
exit
```
```bash
kubectl get pod -n kube-system | grep scheduler
```
if scheduler is moved , reply nothing  
```bash
kubectl run nginx --image=nginx
```
```bash
kubectl get pods
```
It will show like this  
<img width="423" height="140" alt="nginx" src="https://github.com/user-attachments/assets/865cfb61-e62d-41cb-bada-18db236412fa" />  
```bash
docker exec -it cka-cluster1-control-plane bash
```
```bash
cd /etc/kubernetes/manifests
```
```bash
mv /tmp/kube-scheduler.yaml .
```
<img width="440" height="116" alt="ggwp" src="https://github.com/user-attachments/assets/88d4741b-c860-41fe-ad85-16644c814a80" /> 
2) Create 3 pods with the name as pod1, pod2 and pod3 based on the nginx image and use labels as env:test, env:dev and env:prod for each of these pods respectively.  
vim pod1.yaml  
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2025-12-03T04:33:12Z"
  labels:
    run: nginx
    env: test
  name: pod1
  namespace: default
  resourceVersion: "1464"
  uid: dd409f04-a765-4ad8-a610-7000948ed55a
spec:
  containers:
  - image: nginx
    name: pod1
  nodeName: cka-cluster1-control-plane
```
```bash
kubectl create -f pod1.yaml
```
  
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  
vim pod2.yaml  
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2025-12-03T04:33:12Z"
  labels:
    run: nginx
    env: dev
  name: pod2
  namespace: default
  resourceVersion: "1464"
  uid: dd409f04-a765-4ad8-a610-7000948ed55a
spec:
  containers:
  - image: nginx
    name: pod2
  nodeName: cka-cluster1-control-plane
```
```bash
kubectl create -f pod2.yaml
```  
  
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  
vim pod3.yaml  
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2025-12-03T04:33:12Z"
  labels:
    run: nginx
    env: prod
  name: pod3
  namespace: default
  resourceVersion: "1464"
  uid: dd409f04-a765-4ad8-a610-7000948ed55a
spec:
  containers:
  - image: nginx
    name: pod3
  nodeName: cka-cluster1-control-plane
```
```bash
kubectl create -f pod3.yaml
```
```bash
kubectl get pods
```
  
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  
3) Then using the kubectl commands, filter the pods that have labels dev and prod.  
```bash
k get pods -l 'env in (dev,prod)' --show-labels
```
  
Check out the today's lecture from Original Mentor #Piyushsachdeva  
Youtube - https://youtu.be/6eGf7_VSbrQ?si=Ma_NyE1g-7dNw-1Y  
Github - https://github.com/piyushsachdeva/CKA-2024/blob/main/Resources/Day13/readme.md  
Special thanks to Piyush sachdeva &The CloudOps Community for the guidance!  
  
#Kubernetes #Docker # DevOps #TechLearning #Containerization #CloudOps  
#piyushsachdeva #TheCloudOpsCommunity  



