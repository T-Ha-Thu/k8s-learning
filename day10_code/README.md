# **Day 10 : Kubernetes Namespace Explained**  
  
Hello #LinkedInCommunity and #TheCloudOpsCommunity ,  
  
Today I want to share the Day 10 of #40DaysOfKubernetes journey with #Piyushsachdeva  
Below are the lecture of Day 10 learning.  
  
What is Namespace?  
- Provides isolation of resources.  
- Avoid accidental deletion/modification.  
- Separated by resource type or environment or domain and so on.  
- Resources can access each other in the same namespace with their first name by the DNS name for other namespaces.  
  
LAB  
### Step By Step ###  
Install Cluster with [Day06 Video](https://github.com/GGWPEZGAMER/day06)  
1) Create two namespaces and name them ns1 and ns2  
vim ns.yaml  
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: ns1
```
```bash
kubectl apply -f ns.yaml
```
```bash
kubectl get namespace
```
2) Create deployment using nginx image name as deploy-ns1 and deploy-ns2 with a single replica  
```bash
kubectl create deploy deploy-ns1 --image=nginx -n ns1
kubectl create deploy deploy-ns2 --image=nginx -n ns2
```
```bash
kubectl get deploy -n ns1
kubectl get deploy -n ns2
```
3)Exec into the pod of deploy-ns1 and try to curl the IP address of the pod running on deploy-ns2  
```bash
kubectl exec -it deploy-ns1-787478497-9ktr9 -n ns1 -- sh
curl < ip-address of deploy-ns2 >
```
```bash
kubectl exec -it deploy-ns2-754f57f654-tngx7 -n ns2 -- sh
curl < ip-address of deploy-ns1 >
```
4) Scale your deployment replica 1 to 3  
```bash
kubectl scale --replicas=3 deploy/deploy-ns1
kubectl scale --replicas=3 deploy/deploy-ns2
```
5) Create two services of deployments name as svc-ns1 and svc-ns2  
```bash
kubectl expose deploy/deploy-ns1 --name=svc-ns1 --port 80 -n ns1
kubectl expose deploy/deploy-ns2 --name=svc-ns2 --port 80 -n ns2
```
```bash
kubectl get svc -n ns1
kubectl get svc -n ns2
```
```bash
kubectl exec -it deploy-ns1-787478497-9ktr9 -n ns1 -- sh
curl < ip-address of svc-ns2 >
kubectl exec -it deploy-ns2-754f57f654-tngx7 -n ns2 -- sh
curl < ip-address of svc-ns1 >
```
6) Curl the service name instead of IP  
```bash
kubectl exec -it deploy-ns1-787478497-9ktr9 -n ns1 -- sh
curl svc-ns2
kubectl exec -it deploy-ns2-754f57f654-tngx7 -n ns2 -- sh
curl svc-ns1
```
7) Using FQDN  
```bash
kubectl exec -it deploy-ns1-787478497-9ktr9 -n ns1 -- sh
cat /etc/resolv.conf
```
search <mark>ns1.svc.cluster.local</mark> svc.cluster.local cluster.local  
nameserver 10.96.0.10  
options ndots:5  
```bash
kubectl exec -it deploy-ns2-754f57f654-tngx7 -n ns2 -- sh
cat /etc/resolv.conf
```
search <mark>ns2.svc.cluster.local</mark> svc.cluster.local cluster.local  
nameserver 10.96.0.10  
options ndots:5  
```bash 
curl svc-ns1.ns1.svc.cluster.local
curl svc-ns2.ns2.svc.cluster.local
```
  
Check out the today's lecture from Original Mentor #Piyushsachdeva  
Youtube - https://youtu.be/yVLXIydlU_0?si=a8dgaiTosoNKAhN_  
Github - https://github.com/piyushsachdeva/CKA-2024/blob/main/Resources/Day10/readme.md  
Special thanks to Piyush sachdeva &The CloudOps Community for the guidance!  
  
#Kubernetes #Docker # DevOps #TechLearning #Containerization #CloudOps  
#piyushsachdeva #TheCloudOpsCommunity  
