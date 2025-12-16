# **Day 15 : Kubernetes Node Affinity Explained | How Node Affinity Works**  
Hello #LinkedInCommunity and #TheCloudOpsCommunity ,  
  
Today I want to share the Day 15 of #40DaysOfKubernetes journey with #Piyushsachdeva  
Below are the lecture of Day 15 learning.  
  
What is Node Affinity?  
  
Node Affinity in Kubernetes is a feature in Kubernetes that facilitates us to specify the rules for scheduling the pod based on the node labels.  
It facilitates providing a way for pod placement by expressing the requirements about the nodes where the pods should be scheduled.  
Node Affinity allows to expression of various conditions as preferred such as node attributes,  
and anti-affinity rules to avoid certain nodes and to have more complex label expressions for pod placement strategies.   
  
What Are the Types of Node Affinity in Kubernetes?  
  
- RequiredDuringSchedulingIgnoredDuringExecution  
- PreferredDuringSchedulingIgnoredDuringExecution  
  
RequiredDuringSchedulingIgnoredDuringExecution  
  
```yaml
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
       nodeSelectorTerms:
       - matchExpressions:
         - key: disktype
           operator: In
           values:
           - ssd
```
  
PreferredDuringSchedulingIgnoredDuringExecution  
  
```yaml
affinity:
  nodeAffinity:
    preferredDuringSchedulingIgnoredDuringExecution:
    - weight: 100
      preference:
        matchExpressions:
        - key: disktype
          operator: In
          values:
          - ssd
```
  
Bypass Node Affinity  
  
```yaml
  matchExpressions:
  - key: disktype
          operator: Exists
```
  
LAB  
### Step by Step ###  
Install Cluster with [Day06 Video](https://github.com/GGWPEZGAMER/day06)  
1) create a pod with nginx as the image and add the nodeffinity with property requiredDuringSchedulingIgnoredDuringExecution and condition disktype = ssd  
  
```bash
kubectl run nginx --image=nginx --dry-run=client -o yaml > nginx.yaml
```
```bash
kubectl apply -f nginx.yaml
kubectl get pod
```
Showing like that  
  
<img width="577" height="96" alt="1" src="https://github.com/user-attachments/assets/04ed52f5-dba8-4163-82ad-edcec97fcedf" />  
   
vim nginx.yaml  
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
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd 
status: {}
```
```bash
kubectl label nodes cka-cluster2-worker disktype=ssd
```
  
<img width="1062" height="203" alt="2" src="https://github.com/user-attachments/assets/6e00ba68-a13c-4ae3-832b-d8199c60cc89" />  
   
2) create a new pod with redis as the image and add the nodeaffinity with property requiredDuringSchedulingIgnoredDuringExecution  
and condition disktype without any value  
```bash
kubectl run redis --image=redis --dry-run=client -o yaml > redis.yaml
```
vim redis.yaml  
```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: redis
  name: redis
spec:
  containers:
  - image: redis
    name: redis
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: Exists
status: {}
```
```bash
kubectl apply -f redis.yaml
kubectl get pod
```
Showing like that  
  
<img width="577" height="108" alt="3" src="https://github.com/user-attachments/assets/85b3f8bc-ba13-4824-b066-6acd5607d5e3" />  
   
Because of below yaml file added  
  
<img width="558" height="175" alt="4" src="https://github.com/user-attachments/assets/518b1aaf-8b14-4e8b-b643-afd9b596cff5" />  
   
3) ensure that pod2 should be scheduled on worker02 node  
```bash
kubectl label nodes cka-cluster2-worker2 disktype=
```
```bash
kubectl delete pod/redis
```
```bash
kubectl apply -f redis.yaml
```
```bash
kubectl get pod -o wide
```
Showing like that  
  
<img width="1053" height="108" alt="5" src="https://github.com/user-attachments/assets/3d0a7eb8-2246-4486-be83-2ce46ddadcb6" />  
  
Check out the today's lecture from Original Mentor #Piyushsachdeva  
Youtube - https://youtu.be/5vimzBRnoDk?si=IJ0uQJuX8IZAuXBD  
Github - https://github.com/piyushsachdeva/CKA-2024/blob/main/Resources/Day15/readme.md  
Special thanks to Piyush sachdeva &The CloudOps Community for the guidance!  
  
#Kubernetes #Docker # DevOps #TechLearning #Containerization #CloudOps  
#piyushsachdeva #TheCloudOpsCommunity  










