Day 5: What is Kubernetes - Kubernetes Architecture Explained  
Hello #LinkedInCommunity and #TheCloudOpsCommunity ,  
  
Today I want to share the Day 5 of #40DaysOfKubernetes journey with #Piyushsachdeva .  
Below are the lecture of Day 5 learning.  
<img width="1292" height="942" alt="Screenshot 2025-11-06 102855" src="https://github.com/user-attachments/assets/ac2e8079-718f-447e-aa25-c00bea0e4e79" />  
  
- What is Kubernetes?  
     
Kubernetes (K8s) is an open-source platform that helps you automate the deployment, scaling, and management of containerized applications.  
  
Kubernetes has two components that are Control Plane Component and Worker Nodes Component.  
  
Control Plane Component  
The Control Plane is the brain of the cluster, to manages and controls of the whole cluster.  
The Control Plane has 4 main parts of components.  
1) kube/apiserver - to manage all of the instructions between user and cluster.  
2) etcd - to store the key values which is like a database but not actually database showing like json format.  
3) kube/scheduler - to manage the pod's resources such as including available CPU,storage and etc...  
4) kube/controller/manager - monitoring all controller to make sure ..up and healthy..  
  
Worker Nodes Component  
Worker Nodes run pods(containers) and reports their status back to the Control Plane.  
1) Kubelet -  to control the pods(containers) from the request of control Plane.  
2) Kube/proxy - to communicate between pods and services ( just like pods networking )  
  
Containers vs Pods  
A container is the smallest unit that packages your application and everything it needs to run (like code, libraries, dependencies).  
A Pod is a Kubernetes wrapper around one or more containers.It’s the smallest deployable unit in Kubernetes.  
  
    
Check out the today's lecture from Original Mentor #Piyushsachdeva  
Youtube - https://youtu.be/SGGkUCctL4I?si=aL2GOt1Y--73Ad1z  
Github - https://github.com/piyushsachdeva/CKA-2024/blob/main/Resources/Day05/readme.md  

#Kubernetes #Docker # DevOps #TechLearning #Containerization #CloudOps  
#piyushsachdeva #TheCloudOpsCommunity  
