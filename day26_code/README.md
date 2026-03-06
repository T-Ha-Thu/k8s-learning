# **Day 26 : Kubernetes Network Policies Explained**  
  
Hello #LinkedInCommunity and #TheCloudOpsCommunity ,  
  
Today I want to share the Day 26 of #40DaysOfKubernetes journey with #Piyushsachdeva  
Below are the lecture of Day 26 learning.   
  
# What is a network policy in kubernetes?  
A NetworkPolicy is a Kubernetes resource that controls Layer 3/Layer 4 traffic flow (IP/Port level) between Pods, Namespaces, and external endpoints.  
It acts as a virtual firewall for your cluster's pod-to-pod communication.  
  
# Real life Scenario  
Imaging Office Building without network policies ( default kubernetes )  
In office building( Cluster ),  
- person A can talk to everyone  
- person B can talk to everyone  
- person C can talk to everyone
  
# Key point  
- all doors are open  
- no security guards, anyone can go everywhere  
  
Imaging Office Building with network policies  
In office building ( Cluster ),  
- Room ( CEO Office )  
  - Guard rule : " Only manager can enter "  
  - Manager --> CEO Office ( Allowed )  
  - Intern  --> CEO Office ( Blocked )  
  
- Room ( Server Room )  
  - Guard rule : " Only IT Team can enter "  
  - IT Admin --> Server Room ( Allowed )  
  - Saleman  --> Server Room ( Blocked )  
  
# Key Point  
" Network Policy = Security guard with a checklist "  
  
Office Building    =    Kubernetes Cluster  
Rooms              =    Pods (your applications)  
People             =    Traffic (data/requests)  
Security Guard     =    Network Policy  
Door               =    Network Connection  
ID Badge           =    Labels on Pods  
  
If u have 3 pod ( frontend, backend, mysql ) ,  
In ingress  
Frontend ----> Backend ---> Mysql  
In Egress  
Mysql ---> Backend ---> Frontend  
  
Without Network Policies  
Frontend can talk to Backend  
Frontend can talk to Mysql  
Backend can talk to Mysql  
Database can talk to Frontend  
  
With Network Policies  
According to the network policy yaml file declaration pod can talk to declaration pod too...    
  
# Let's do LAB  
# 1) Create a new kind cluster by disabling the default CNI  
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
networking:
  disableDefaultCNI: true
  podSubnet: 192.168.0.0/16
```
```bash
kind create cluster --config kind.yaml --name cka
```
```bash
kubectl get nodes
```
after creating the cluster , u will see the nodes are not ready ...like this...  
<img width="911" height="443" alt="1" src="https://github.com/user-attachments/assets/5f35352e-5d1d-4df1-8ee9-27635e2d0f3a" />  
Because the cluster needs plugin CNI( Container Network Interface ).  
  
# 2) Install Calico Network Add-on to your Kind cluster  
In this Lab, we will use calico plugin ...   
To install Calico Plugin  
```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.31.4/manifests/calico.yaml
```
<img width="1192" height="966" alt="2" src="https://github.com/user-attachments/assets/04cd54fe-e129-4b03-81b9-45127623dc9d" />  
  
Calico Documentation - https://docs.tigera.io/calico/latest/getting-started/kubernetes/kind  
To verify the installtion  
```bash
kubectl get pods -l k8s-app=calico-node -A
```
It will show like this  
<img width="925" height="115" alt="3" src="https://github.com/user-attachments/assets/506c462c-10d4-4c96-975f-de26cb8bf722" />  

# 3) Create 3 deployments with as below: name: frontend , image-name: nginx , replicas=1 , containerPort name: backend , image-name: nginx , replicas=1 , containerPort name: db , image-name: mysql , replicas=1 , containerPort: 3306  
vim manifest.yaml  
```bash
apiVersion: v1
kind: Pod
metadata:
  name: frontend
  labels:
    role: frontend
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - name: http
      containerPort: 80
      protocol: TCP
---
apiVersion: v1
kind: Service
metadata:
  name: frontend
  labels:
    role: frontend
spec:
  selector:
    role: frontend
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
---
apiVersion: v1
kind: Pod
metadata:
  name: backend
  labels:
    role: backend
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - name: http
      containerPort: 80
      protocol: TCP
---
apiVersion: v1
kind: Service
metadata:
  name: backend
  labels:
    role: backend
spec:
  selector:
    role: backend
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: db
  labels:
    name: mysql
spec:
  selector:
    name: mysql
  ports:
  - protocol: TCP
    port: 3306
    targetPort: 3306
---
apiVersion: v1
kind: Pod
metadata:
  name: mysql
  labels:
    name: mysql
spec:
  containers:
    - name: mysql
      image: mysql:latest
      env:
        - name: "MYSQL_USER"
          value: "mysql"
        - name: "MYSQL_PASSWORD"
          value: "mysql"
        - name: "MYSQL_DATABASE"
          value: "testdb"
        - name: "MYSQL_ROOT_PASSWORD"
          value: "verysecure"
      ports:
        - name: http
          containerPort: 3306
          protocol: TCP
```
```bash
kubectl apply -f manifest.yaml
```
<img width="676" height="152" alt="4" src="https://github.com/user-attachments/assets/d6bc03d3-937d-4feb-8364-a78e731f60bb" />    
  
If your manifest.yaml is fine runnig ,u will see like this  
<img width="672" height="242" alt="5" src="https://github.com/user-attachments/assets/7c7e6d72-d052-40d4-b6b8-685c8815ed99" />  

# 4) Do the connectivity test that all of your pods are able to interact with each other.  
Now, we should curl inside the pod from another pod ( frontend to backend, backend to frontend )  
```bash
kubeclt exec -it frontend -- bash
```
Inside frontend pod  
```bash
curl backend:80
```
if curl is successful , try telnet if telnet is not installed, install first  
```bash
apt-get update && apt-get install telnet -y
```
```bash
telnet db 3306
```
<img width="487" height="161" alt="6" src="https://github.com/user-attachments/assets/a6d8faf9-a8ce-4b2c-95b2-09aeeaf8b3d6" />  
  
Also try the backend pod like the frontend pod   
It will be successed bcoz of there is no network policy..  

# 5) Create a network policy and restrict the access so that only backend pod should be able to access the db service on port 3306.  
Now we will add network policy yaml into the cluster...bcoz of security practice  
vim netpol.yaml  
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-test
spec:
  podSelector:
    matchLabels:
      name: mysql
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          role: backend
    ports:
    - port: 3306
```
```bash
kubectl apply -f netpol.yaml
kubectl describe netpol db-test
```
<img width="756" height="355" alt="7" src="https://github.com/user-attachments/assets/7db7e42f-dc57-4124-b9b9-ba4a897f7d89" />  
  
Now, let's check again with telnet for all pods
```bash
kubectl exec -it frontend -- bash
```
```bash
telnet db 3306
```
<img width="716" height="107" alt="8" src="https://github.com/user-attachments/assets/52b1c6c5-f42c-48b3-959e-c12906dcd3dc" />  
  
Frontend pod is not allowed for database because of network policy rule.  
```bash
kubectl exec -it backend -- bash
```
```bash
telnet db 3306
```
<img width="413" height="153" alt="9" src="https://github.com/user-attachments/assets/aedf3d25-bc6d-4235-adee-a86dbfb154b0" />  
  
Check out the today's lecture from Original Mentor #Piyushsachdeva  
Youtube - https://www.youtube.com/watch?v=eVtnevr3Rao  
Github - https://github.com/piyushsachdeva/CKA-2024/blob/main/Resources/Day26/readme.md  
Special thanks to Piyush sachdeva &The CloudOps Community for the guidance!  
  
#Kubernetes #Docker # DevOps #TechLearning #Containerization #CloudOps  
#piyushsachdeva #TheCloudOpsCommunity  










