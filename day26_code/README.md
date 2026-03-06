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
Key point  
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
1) Create a new kind cluster by disabling the default CNI
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

