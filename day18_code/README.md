# **Day 18 : Kubernetes Health Probes Explained | Liveness vs Readiness Probes**  
Hello #LinkedInCommunity and #TheCloudOpsCommunity ,  
  
Today I want to share the Day 18 of #40DaysOfKubernetes journey with #Piyushsachdeva  
Below are the lecture of Day 18 learning.  
  
What are probe in kubernetes?  
In Kubernetes, Probes are health-check mechanisms used by the kubelet to determine the state of a container and to decide when to start traffic,  
when to restart a container, or when to consider it ready for service.  
They are fundamental to building reliable, self-healing applications in Kubernetes.  
  
Types of Probes in Kubernetes  
Kubernetes supports three probe types, each serving a distinct operational purpose.  
1. Liveness Probe  
2. Readiness Probe  
3. Startup Probe  
  
Liveness Probe  
- If the liveness probe fails repeatedly, Kubernetes restarts the container.  
- Used to recover from deadlocks, infinite loops, or hung processes.  
  
Readiness Probe  
- If the readiness probe fails:  
  - The Pod remains running  
  - But it is removed from Service load balancing  
- No restart occurs.  
  
Startup Probe  
- Used for slow-starting applications  
- Until startup probe succeeds:  
  - Liveness and readiness probes are disabled  
- If startup probe fails, the container is restarted.  
  
-------------------------------------------------------------------------------------------------------------------------------------------------------------------
  
LAB  
### Step by Step ###  
Install Cluster with [Day06 Video](https://github.com/GGWPEZGAMER/day06)  
  
1) Creating Liveness command probe  
```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-exec
spec:
  containers:
  - name: liveness
    image: registry.k8s.io/busybox
    args:
    - /bin/sh
    - -c
    - touch /tmp/healthy; sleep 30; rm -f /tmp/healthy; sleep 600
    livenessProbe:
      exec:
        command:
        - cat 
        - /tmp/healthy
      initialDelaySeconds: 5
      periodSeconds: 5
```
```bash
vim liveness-c.yaml
```
```bash
kubectl apply -f liveness-c.yaml
```
```bash
kubectl get pod
```
```bash
kubectl describe pod liveness-exec
```
It will show like this  
<img width="1481" height="582" alt="1" src="https://github.com/user-attachments/assets/b4b64ed0-f83e-4bfd-8903-ea54a3d8a510" />  
  
2) Creating liveness-http and readiness-http probe  
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello
spec:
  containers:
  - name: liveness
    image: registry.k8s.io/e2e-test-images/agnhost:2.40
    args:
    - liveness
    livenessProbe:
      httpGet:
        path: /healthz
        port: 8080
      initialDelaySeconds: 3
      periodSeconds: 3
    readinessProbe:
      httpGet:
        path: /healthz
        port: 8080
      initialDelaySeconds: 15
      periodSeconds: 10
```
```bash
vim liveness&readiness-http.yaml
```
```bash
kubectl apply -f liveness&readiness-http.yaml
```
```bash
kubectl get pod
```
```bash
kubectl describe pod hello
```
It will show like this  
<img width="1461" height="591" alt="2" src="https://github.com/user-attachments/assets/9a3ff1e1-1587-4bbc-b821-ca56e996802b" />  
  
3) Creating liveness-tcp probe  
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: tcp-pod
  labels:
    app: tcp-pod
spec:
  containers:
  - name: goproxy
    image: registry.k8s.io/goproxy:0.1
    ports:
    - containerPort: 8080
    livenessProbe:
      tcpSocket:
        port: 3000
      initialDelaySeconds: 10
      periodSeconds: 5
```
```bash
vim liveness-tcp.yaml
```
```bash
kubectl apply -f liveness-tcp.yaml
```
```bash
kubectl get pod
```
```bash
kubectl describe pod tcp-pod
```
It will show like this  
<img width="1423" height="584" alt="3" src="https://github.com/user-attachments/assets/e6246b27-90df-42ac-b3ed-e21729f81d1f" />  
  
Check out the today's lecture from Original Mentor #Piyushsachdeva  
Youtube - https://youtu.be/x2e6pIBLKzw?si=qOpyEwPzrMn_HvcY  
Github - https://github.com/piyushsachdeva/CKA-2024/blob/main/Resources/Day18/readme.md  
Special thanks to Piyush sachdeva &The CloudOps Community for the guidance!  
  
#Kubernetes #Docker # DevOps #TechLearning #Containerization #CloudOps  
#piyushsachdeva #TheCloudOpsCommunity  









