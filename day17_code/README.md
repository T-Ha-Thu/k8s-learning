# **Day 17 : Kubernetes Autoscaling Explained | HPA Vs VPA**  
Hello #LinkedInCommunity and #TheCloudOpsCommunity ,  
  
Today I want to share the Day 17 of #40DaysOfKubernetes journey with #Piyushsachdeva  
Below are the lecture of Day 17 learning.  

What is Kubernetes Auto Scaling?  
Kubernetes Auto Scaling increases resources when load rises and decreases them when load falls—without manual intervention.  
Core Types of Kubernetes Auto Scaling are HPA ( Horizontal Pod Autoscaler ) and VPA ( Vertical Pod Autoscaler ).  

HPA ( Horizontal Pod Autoscaler )  
- HPA automatically adjusts the number of pods in a Deployment, ReplicaSet, or StatefulSet based on observed CPU utilization, memory usage, or custom metrics.  
- It scales horizontally, meaning it adds or removes pod instances to handle load.

VPA ( Vertical Pod Autoscaler )
- VPA automatically adjusts the CPU and memory requests/limits of individual pods based on their actual usage.  
- It scales vertically, meaning it changes the resources assigned to a pod, rather than adding/removing pods.

Key Point
- HPA adjusts quantity of pods, not their size.
- VPA adjusts resources per pod, not the number of pods.
  
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------  
  
LAB  
### Step by Step ###  
Install Cluster with [Day06 Video](https://github.com/GGWPEZGAMER/day06)  

1) Deploy with the given YAML file
```bash
vim deploy.yaml
```
```bash
kubectl apply -f deploy.yaml
kubectl get deploy
kubectl get pod
kubectl get service
```
2) login to your cluster and create a new namespace with the name mem-example  
```bash
kubectl create ns mem-example
kubectl get ns
```
3) Install metrics server using the yaml  
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

4) Autoscaling the pod
```bash
kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=10

kubectl run -i --tty load-generator --rm --image=busybox:1.28 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done"

kubectl get hpa php-apache --watch

kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=10
```
Above command are the autoscaling command of pod.  
  
<img width="847" height="267" alt="1" src="https://github.com/user-attachments/assets/eb405ad7-052a-4f5a-ba7c-8f2ebd0f0447" />  

Check out the today's lecture from Original Mentor #Piyushsachdeva  
Youtube - https://youtu.be/afUL5jGoLx0?si=loaPvrNIsfpcNCK4
Github - https://github.com/piyushsachdeva/CKA-2024/blob/main/Resources/Day17/readme.md  
Special thanks to Piyush sachdeva &The CloudOps Community for the guidance!  
  
#Kubernetes #Docker # DevOps #TechLearning #Containerization #CloudOps  
#piyushsachdeva #TheCloudOpsCommunity 

