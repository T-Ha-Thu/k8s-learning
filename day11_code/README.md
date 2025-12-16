Day 11 : Multi Container Pod Kubernetes - Sidecar vs Init Container  
  
Hello #LinkedInCommunity and #TheCloudOpsCommunity ,  
  
Today I want to share the Day 11 of #40DaysOfKubernetes journey with #Piyushsachdeva  
Below are the lecture of Day 11 learning.  
  
What is Init Container?  
An Init Container is a container that runs before the main (regular) containers start in a Pod. Its job is to do some initialization or setup work.  
  
Characteristics:  
- Runs sequentially, one by one if multiple Init Containers exist.  
- Must succeed before the main containers start. If an Init Container fails, Kubernetes keeps retrying it until it succeeds.  
- Usually used for tasks like:  
    - Setting up configuration files  
    - Database migrations  
    - Waiting for a service to be available  
    - Creating or modifying volumes  
  
Example YAML of Init Container  
```yaml
initContainers:
- name: init-myservice
  image: busybox
  command: ['sh', '-c']
  args:
    - until nslookup myservice.default.svc.cluster.local; do echo waiting; sleep 2; done
```
Sidecar Container  
A Sidecar Container is a container that runs alongside the main container in the same Pod. It extends or enhances the functionality of the main container but is not the primary application.  
  
Characteristics:  
- Runs concurrently with the main container.  
- Typically shares the same network namespace and volumes with the main container.  
- Often used for tasks like:  
    - Logging (e.g., Promtail, Fluentd)  
    - Monitoring / metrics collection  
    - Proxying or routing traffic (e.g., Envoy, Istio sidecar)  
    - Backup or sync services  
   
Example YAML of Sidecar Container  
```yaml
containers:
- name: main-app
  image: myapp:1.0
- name: log-sidecar
  image: fluentd:latest
  volumeMounts:
  - name: shared-logs
    mountPath: /var/log/myapp
```
LAB  
### Step by Step ###  
Install Cluster with [Day06 Video](https://github.com/GGWPEZGAMER/day06)  
1) Create Init Container  
vim pod.yaml  
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp
  labels:
    name: myapp-pod
spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    command: ['sh', '-c', 'echo the app is running && sleep 3600']
    env: 
    - name: FIRSTNAME
      value: "Thiha"
  initContainers:
  - name: init-myservice
    image: busybox:1.28
    command: ['sh', '-c']
    args: ['until nslookup myservice.default.svc.cluster.local; do echo waiting for service to be up;sleep 2;done'] 
  - name: init-mydb
    image: busybox:1.28
    command: ['sh', '-c']
    args: ['until nslookup mydb.default.svc.cluster.local; do echo waiting for service to be up;sleep 2;done']
```
```bash
kubectl apply -f pod.yaml
```
```bash
kubectl get pods
```
```bash
kubectl create deploy nginx-deploy --image nginx --port 80
kubectl create deploy mydb --image redis --port 80
```
```bash
kubectl get deploy
```
```bash
kubectl expose deploy nginx-deploy --name myservice --port 80
kubectl expose deploy mydb --name mydb --port 80
```
```bash
kubectl get svc
```
```bash
kubectl get pods
```
```bash
kubectl exec -it myapp -- printenv
```
You will see like this  
<img width="637" height="113" alt="image" src="https://github.com/user-attachments/assets/2c67aff3-213f-4e92-8dec-fd8b89f741ef" />
  
Check out the today's lecture from Original Mentor #Piyushsachdeva  
Youtube - https://youtu.be/yRiFq1ykBxc?si=JZ-nrKc0AIW3nGZD  
Github - https://github.com/piyushsachdeva/CKA-2024/blob/main/Resources/Day11/readme.md  
Special thanks to Piyush sachdeva &The CloudOps Community for the guidance!  
  
#Kubernetes #Docker # DevOps #TechLearning #Containerization #CloudOps  
#piyushsachdeva #TheCloudOpsCommunity  
