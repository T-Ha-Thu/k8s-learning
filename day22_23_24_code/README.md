# **Day 22 : Kubernetes Authentication and Authorization Simply Explained**
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


