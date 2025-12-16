Day 9: Kubernetes Services Explained - ClusterIP vs NodePort vs Loadbalancer vs External  
Hello #LinkedInCommunity and #TheCloudOpsCommunity ,  
  
Today I want to share the Day 9 of #40DaysOfKubernetes journey with #Piyushsachdeva .Below are the lecture of Day 9 learning.  
Step-by-Step Process  
Get Started  
- Creating Node(Cluster) with YAML file  
vim kind.yaml  
```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  extraPortMappings:
  - containerPort: 30001
    hostPort: 30001
- role: worker
- role: worker
```
```bash
 kind create cluster --config kind.yaml --name cka-cluster
```
What is Service in Kubernetes?  
Different applications communicate with each other within Kubernetes using a service; it is also used to access applications outside the cluster.    
There are 4 types of Services:  
  
-ClusterIP(For Internal access)  
-NodePort(To access the application on a particular port)    
-LoadBalancer(To access the application on a domain name or IP address without using the port number)  
-External (To use an external DNS for routing) 
  
ClusterIP  
After node, creating Service with yaml that called ClusterIP  
vim myapp.yaml  
```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp
  labels:
    env: demo
spec:
  ports:
  - port: 80
    targetPort: 80
  selector:
    env: demo
```
```bash
kubectl apply -f myapp.yaml
```
```bash
kubectl get svc
```
Creating deployment of myapp  
vim deploy.yaml  
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
  labels:
    env: demo
spec:
  replicas: 1
  selector:
    matchLabels:
      env: demo
  template:
    metadata:
      labels: 
        env: demo
    spec: 
      containers:
      - name: myapp
        image: nginx:1.23.4-alpine
        ports:
        - containerPort: 80
```
```bash
kubectl apply -f deploy.yaml
```
To check the nginx with specific port number  
```bash
kubectl run tmp-pod --rm -it --image=busybox -- sh
```
When you are inside the pod.  
```bash
wget http://<internal-ip>
```
You will see the Welcome to nginx  
```bash
kubectl get svc
```
You will see the internal ip address of services  
To communicate from outside browser  
NodePort  
vim nodeport.yaml  
```yaml
apiVersion: v1  
kind: Service
metadata:
  name: myapp
  labels:
    env: demo
spec:
  type: NodePort
  ports:
  - nodePort: 30001
    port: 80
    targetPort: 80
  selector:
      env: demo
```
```bash
kubectl apply -f nodeport.yaml
```
Go to the browser and type  
```bash
localhost:30001
```
LoadBalancer  
  
Your loadbalancer service will act as nodeport if you are not using any managed cloud Kubernetes such as GKE,AKS,EKS etc.   
In a managed cloud environment, Kubernetes creates a load balancer within the cloud project, which redirects the traffic to the Kubernetes Loadbalancer service.  
To create loadbalancer  
vim lb.yaml  
```yaml
apiVersion: v1
kind: Service
metadata:
  name: lb-svc
  labels:
    env: demo
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    env: demo
```
```bash
kubectl apply -f lb.yaml
```
ExternalName  
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
  namespace: prod
spec:
  type: ExternalName
  externalName: my.api.example.com
```  
Check out the today's lecture from Original Mentor #Piyushsachdeva  
Youtube - https://youtu.be/tHAQWLKMTB0?si=ztj4BUsU8eHuFGx6  
Github - https://github.com/piyushsachdeva/CKA-2024/blob/main/Resources/Day09/readme.md  
  
#Kubernetes #Docker # DevOps #TechLearning #Containerization #CloudOps  
#piyushsachdeva #TheCloudOpsCommunity  

