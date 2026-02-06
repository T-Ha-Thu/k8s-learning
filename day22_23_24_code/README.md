<img width="1018" height="120" alt="6" src="https://github.com/user-attachments/assets/77c23bbc-340e-43c6-9891-558ced20f069" /># **Day 22 : Kubernetes Authentication and Authorization Simply Explained**
# **Day 23 : Kubernetes RBAC Explained - Role Based Access Control Kubernetes**
# **Day 24 : Kubernetes RBAC Continued - Clusterrole and Clusterrole Binding**

Hello #LinkedInCommunity and #TheCloudOpsCommunity ,  
  
Today I want to share the Day 22,23 and 24 of #40DaysOfKubernetes journey with #Piyushsachdeva  
Below are the lecture of Day 22,23 and 24 learning.  
  
# What is Authentication and Authorization in K8s?  
  
# Authentication ( Who you are? )  
Authentication is the process of verifying the identity of a user or system.  
Kubernetes needs to know who is making the request. This is done via authentication mechanisms.  
Authentication methods is Certificates that sign by Cluster CA.( Client-certificate-data + Client-key-data in kubeconfig )  
  
# Authorization ( Who you can do? )  
Authorization is the process of determining what actions or resources a verified user is allowed to access.  
Kubernetes knows who you are, it checks what actions you are allowed to perform.  
Authorization modes are  
1) RBAC( Role-Based Access Control )  
2) ABAC( Attribute-Based Access Control )  
3) Webhook  
4) Node  
  
# What is RBAC?  
RBAC (Role-Based Access Control) is Kubernetes’s authorization mechanism that defines  
"what an authenticated user or service account is allowed to do".  
  
# What is Role and RoleBinding?  
These are RBAC (authorization) objects used to control permissions inside a single namespace.  
A Role defines what actions are allowed on which resources within one namespace.  
A RoleBinding attaches a Role (or ClusterRole) to a user, group, or service account within one namespace.  
  
# What is ClusterRole and ClusterRoleBinding?  
These are RBAC (authorization) objects used to control cluster-wide permissions.  
A ClusterRole defines what actions are allowed on cluster-scoped resources or resources across all namespaces.  
A ClusterRoleBinding attaches a ClusterRole to a user, group, or service account across the entire cluster.  
  
# Real Life Scenario  
ID Card = Authentication  
Door access rules = Authorization  
Permission System = RBAC  
Department Permission = Role  
Department Assignment = RoleBinding  
Company-wide Permission = ClusterRole  
Global assignment = ClusterRoleBinding  
  
# Key Point  
Authentication = Who are you  
Authorization = What can you do  
Role = Namespace permissions  
RoleBinding = Who gets them  
ClusterRole = Global permissions  
ClusterRoleBinding = Global assignment

# Let's do LAB  

1) Creating key,csr and crt
2) Creating role and rolebinding
3) Creating clusterrole and clusterrolebinding

# 1) Creating key,csr and crt with [day21](https://github.com/T-Ha-Thu/k8s-learning/blob/main/day21_code/README.md)
U can check the created user certificate in kubeconfig file
```bash
cat .kube/config
```
This is CA( Certificate Authority )  
<img width="1918" height="716" alt="1" src="https://github.com/user-attachments/assets/66bd95f3-a47c-4cbb-bb59-b8b919392dd1" />  
This is User ( Client-certificate-data and client-key-data )  
<img width="1907" height="602" alt="2" src="https://github.com/user-attachments/assets/c9967152-3f62-4ca0-9edc-fb7dcb4643fa" />  
Also u can check kube-apiserver which has all of informations in command section   
```bash
kubectl get node
```
<img width="965" height="77" alt="3" src="https://github.com/user-attachments/assets/b8b3bc90-b0a0-41df-9707-0109002d41f5" />  

```bash
docker exec -it cka-cluster1-control-plane bash
cd /etc/kubernetes/manifests
cat kube-apiserver.yaml
```
u can check in pki which has all of certificates
```bash
cd /etc/kubernetes/pki
ls -alrth
```

# 2) Creating role and rolebinding for specific user  
Creating role to access what resources and to take action for these resources.
Role is what resources u can acceess and what action u can do in this resources.
vim role.yaml
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```
```bash
kubectl applly -f role.yaml
kubectl get role
kubectl describe role pod-reader
```
<img width="786" height="98" alt="4" src="https://github.com/user-attachments/assets/d12e609f-e536-4aed-a1f2-bcbe6fb923c9" />  
  
Creating RoleBinding ( Role and Rolebinding are connected )  
  
Real Life Scenario  
Role is permission and rolebinding is "this permissions who get them"
vim rolebinding.yaml
```yaml
apiVersion: rbac.authorization.k8s.io/v1
# This role binding allows "jane" to read pods in the "default" namespace.
# You need to already have a Role named "pod-reader" in that namespace.
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
# You can specify more than one "subject"
- kind: User
  name: learner # "name" is case sensitive
  apiGroup: rbac.authorization.k8s.io
roleRef:
  # "roleRef" specifies the binding to a Role / ClusterRole
  kind: Role #this must be Role or ClusterRole
  name: pod-reader # this must match the name of the Role or ClusterRole you wish to bind to
  apiGroup: rbac.authorization.k8s.io
```
```bash
kubectl apply -f rolebinding.yaml
kubectl get rolebinding
kubectl describe rolebinding read-pods
```
<img width="905" height="92" alt="5" src="https://github.com/user-attachments/assets/c2bdf956-d848-484e-9308-1b7c635064b6" />

After created learner user of role and rolebinding check with admin user for access for this user yes or no
```bash
kubectl auth can-i get pod --as learner
```
Setting the client certificate credential in kubeconfig  
```bash
kubectl config set-credentials learner --client-key=/path/of/learner.key --client-certificate=/path/of/learner.crt --embed-certs=true
```
Setting a kubeconfig context
```bash
kubectl config set-context learner --cluster=kind-cka-cluster1 --user=learner
```
<img width="1018" height="120" alt="6" src="https://github.com/user-attachments/assets/4deb23d9-4748-4893-83c3-989fb6ef2245" />  

After that login with learner
```bash
kubectl auth can-i get pod --as learner
```
U can get pod only bcoz of in the role.yaml ( resources: ["pods"] ) if u want other resources u can add them. resources: ["pods", "deploy", "replicas"]  
Finally u can also check the kubeconfig file again this user "learner" will see....  

# 3) Creating ClusterRole and ClusterRoleBinding
ClusterRole is just like Role but not one namespace,across ALL namespaces(or cluster-scoped resources).
ClusterRoleBinding is just like RoleBinding but not one namespace, across the entire Cluster.
Not Cluster Level Resources (Namespace-scoped resource)
```bash
kubectl api-resources --namespaced=true
```
CLuster Level Resources (Cluster-scoped resource)
```bash
kubectl api-resources --namespaced=false
```
Creating Clusterrole
```bash
kubectl create clusterrole node-reader --verb=list,get,watch --resource=node
```
```bash
kubectl get clusterrole | grep node-reader
kubectl describe clusterrole/node-reader
```
<img width="1151" height="252" alt="7" src="https://github.com/user-attachments/assets/9b4e6533-6007-4529-8b62-752aea8e6fa8" />  

Creating Clusterrolebinding
```bash
kubectl create clusterrolebinding reader-binding --clusterrole=node-reader --user=learner
```
```bash
kubectl get clusterrolebinding | grep reader-binding
kubectl describe clusterrolebinding/reader-binding
```
<img width="1216" height="326" alt="8" src="https://github.com/user-attachments/assets/76d4205b-932d-4c48-93d1-4283fc08e608" />  

```bash
kubectl config get-contexts
```

Change to learner context
```bash
kubectl auth can-i get nodes --as black
kubectl config use-context black
kubectl get nodes
kubectl describe node cka-cluster1-control-plane
```
<img width="1507" height="511" alt="9" src="https://github.com/user-attachments/assets/d5aa01c1-c614-4f11-acb8-62f447e49747" />  

  
Check out the today's lecture from Original Mentor #Piyushsachdeva  
Youtube - day_22 ( https://www.youtube.com/watch?v=P0bogYEyfeI&list=PLl4APkPHzsUUOkOv3i62UidrLmSB8DcGC&index=23 )
          day_23 ( https://www.youtube.com/watch?v=uGcDt7iNFkE&list=PLl4APkPHzsUUOkOv3i62UidrLmSB8DcGC&index=24 )
          day_24 ( https://www.youtube.com/watch?v=DswQe7shSa4&list=PLl4APkPHzsUUOkOv3i62UidrLmSB8DcGC&index=25 )
Github -  day_22 ( https://github.com/piyushsachdeva/CKA-2024/tree/main/Resources/Day22 )
          day_23 ( https://github.com/piyushsachdeva/CKA-2024/tree/main/Resources/Day23 )
          day_24 ( https://github.com/piyushsachdeva/CKA-2024/tree/main/Resources/Day24 ) 
Special thanks to Piyush sachdeva &The CloudOps Community for the guidance!  
  
#Kubernetes #Docker # DevOps #TechLearning #Containerization #CloudOps  
#piyushsachdeva #TheCloudOpsCommunity  








