# **Day 16 : Kubernetes Requests and Limits**  
Hello #LinkedInCommunity and #TheCloudOpsCommunity ,  
  
Today I want to share the Day 16 of #40DaysOfKubernetes journey with #Piyushsachdeva  
Below are the lecture of Day 16 learning.  
  
1) What is a Kubernetes Resource Request?  
Technical Definition  
  
A resource request (CPU or memory) is the minimum guaranteed amount of resources that Kubernetes reserves for a container.  
- Used by the Kubernetes Scheduler  
- Determines which node the Pod can be placed on  
- Not enforced at runtime (except for memory admission)  
```yaml
resources:
  requests:
    memory: "256Mi"
    cpu: "250m"
```
Meaning:  
- Scheduler will place the Pod only on a node that has:  
 - ≥ 256Mi free memory  
 - ≥ 250 millicores CPU available  
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------  
2) What is a Kubernetes Resource Limit?  
Technical Definition  
  
A resource limit is the maximum amount of resources a container is allowed to consume.  
- Enforced by:  
 - cgroups (Linux kernel feature)  
- Protects the node from resource exhaustion  
```yaml
resources:
  limits:
    memory: "512Mi"
    cpu: "500m"
```
Meaning:  
- Container cannot exceed:  
 - 512Mi memory  
 - 0.5 CPU core
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------  
3) Runtime Behavior (Very Important)  
Memory  
- If container exceeds memory limit ( OOMKilled )  
- Pod restarts if restart policy allows  
CPU  
- If container exceeds CPU limit ( Throttled, not killed )  
- Performance slows down  
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------  
  
LAB  
### Step by Step ###  
Install Cluster with [Day06 Video](https://github.com/GGWPEZGAMER/day06)  
  
1) login to your cluster and create a new namespace with the name mem-example  
```bash
kubectl create ns mem-example
kubectl get ns
```
2) Install metrics server using the yaml  
Get the yaml from [this](https://github.com/piyushsachdeva/CKA-2024/blob/main/Resources/Day16/metrics-server.yaml)  
vim metrics-server.yaml  
```bash
kubectl apply -f metrics-server.yaml
kubectl get pod -n kube-system
```
<img width="928" height="352" alt="3" src="https://github.com/user-attachments/assets/56e429b6-365c-469e-aee2-5afdf198e4b6" />  
  
```bash
kubectl top node
```
<img width="807" height="132" alt="2" src="https://github.com/user-attachments/assets/0e1a45bc-9d91-4043-b959-7c258ad790fc" />  
  
This yaml is the addons of kubernetes cluster to check CPU and Memory .  
  
3) Specify a memory request and limit  
```bash
kubectl create ns mem-example
```
vim mem-request-limit.yaml  
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: memory-demo
  namespace: mem-example
spec:
  containers:
  - name: memory-demo-ctr
    image: polinux/stress
    resources:
      requests:
        memory: "100Mi"
      limits:
        memory: "200Mi"
    command: ["stress"]
    args: ["--vm", "1", "--vm-bytes", "150M", "--vm-hang", "1"]
```
```bash
kubectl apply -f mem-request-limit.yaml
```
```bash
kubectl get pod memory-demo -n mem-example
```
<img width="763" height="67" alt="mem1" src="https://github.com/user-attachments/assets/223337e5-f118-4005-af51-7683baf24790" />  
  
Running because of "150M" is between "100Mi" and "200Mi".  
  
4) Exceed memory limit  
vim mem-2.yaml  
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: memory-demo-2
  namespace: mem-example
spec:
  containers:
  - name: memory-demo-2-ctr
    image: polinux/stress
    resources:
      requests:
        memory: "50Mi"
      limits:
        memory: "100Mi"
    command: ["stress"]
    args: ["--vm", "1", "--vm-bytes", "250M", "--vm-hang", "1"]
```
```bash
kubectl apply -f mem-2.yaml
```
```bash
kubectl get pod memory-demo-2 -n mem-example
```
<img width="757" height="70" alt="mem-2" src="https://github.com/user-attachments/assets/f2061783-020b-4feb-8b65-a7272e2bed97" />  
  
CrashLoopBackoff because of "250M" is out of "50Mi" and "100Mi"..out of memory  
  
5) Memory request too big for Nodes  
vim mem-3.yaml  
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: memory-demo-3
  namespace: mem-example
spec:
  containers:
  - name: memory-demo-3-ctr
    image: polinux/stress
    resources:
      requests:
        memory: "1000Gi"
      limits:
        memory: "1000Gi"
    command: ["stress"]
    args: ["--vm", "1", "--vm-bytes", "150M", "--vm-hang", "1"]
```
```bash
kubectl apply -f mem-3.yaml
```
```bash
kubectl get pod memory-demo-3 -n mem-example
```
<img width="683" height="65" alt="mem-3" src="https://github.com/user-attachments/assets/306bff9c-a1e3-416c-9457-ad8593d61087" />  
  
Pending because of "150M" is too small for request and limits "1000Gi".  
  
```bash
kubectl get pod -n mem-example
```
<img width="807" height="137" alt="1" src="https://github.com/user-attachments/assets/28e2b12f-ace3-4ff8-85ed-d126ab60ce6b" />  
  
Check out the today's lecture from Original Mentor #Piyushsachdeva  
Youtube - https://youtu.be/Q-mk6EZVX_Q?si=zrO5abVA5msxXQ3x  
Github - https://github.com/piyushsachdeva/CKA-2024/tree/main/Resources/Day16  
Special thanks to Piyush sachdeva &The CloudOps Community for the guidance!  
  
#Kubernetes #Docker # DevOps #TechLearning #Containerization #CloudOps  
#piyushsachdeva #TheCloudOpsCommunity  








