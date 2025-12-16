# **Day 12 : DaemonSets, Job and Cronjob in Kubernetes**  
Hello #LinkedInCommunity and #TheCloudOpsCommunity ,  
  
Today I want to share the Day 12 of #40DaysOfKubernetes journey with #Piyushsachdeva  
Below are the lecture of Day 12 learning.  
  
What is DaemonSet?  
A DaemonSet ensures that a copy of a specific Pod runs on every node (or on selected nodes) in the Kubernetes cluster.
Used for tasks that must run on all nodes, such as:  
  
- Log collection (e.g., Promtail, Fluentd)  
- Monitoring agents (e.g., Node Exporter, Datadog Agent)  
- Security agents  
  
When a new node is added, the DaemonSet automatically schedules the pod on it.  
If a node is removed, the pod on that node is also removed.  
```yaml
apiVersion: apps/v1
kind:  DaemonSet
metadata:
  name: nginx-ds
  labels:
    env: demo
spec:
  template:
    metadata:
      labels:
        env: demo
      name: nginx
    spec:
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
  selector:
    matchLabels:
      env: demo
```

What is Job and CronJob?  
A Job creates one or more Pods that run to completion. Once the task finishes successfully, the Job is considered done.  
Tasks that are finite and need to run only once or a set number of times:  
  
- Database migrations  
- Batch processing  
- One-time scripts
  
If a Pod fails, the Job can retry it until successful(depending on .spec.backoffLimit).  
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  template:
    spec:
      containers:
      - name: pi
        image: perl:5.34.0
        command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
      restartPolicy: Never
  backoffLimit: 4
```
   
A CronJob runs Jobs on a scheduled basis (like Linux cron).  

Tasks that need to run periodically:
- Backups  
- Sending reports or emails  
- Cleaning up temporary files
  
Uses a cron schedule (e.g., */5 * * * *) to launch Jobs automatically.  
Each scheduled run creates a new Job.  
```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "* * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox:1.28
            imagePullPolicy: IfNotPresent
            command:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure
```
LAB  
### Step by Step ###  
Install Cluster with [Day06 Video](https://github.com/GGWPEZGAMER/day06)  
1) Creating DaemonSet Cluster
vim ds.yaml
```yaml
apiVersion: apps/v1
kind:  DaemonSet
metadata:
  name: nginx-ds
  labels:
    env: demo
spec:
  template:
    metadata:
      labels:
        env: demo
      name: nginx
    spec:
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
  selector:
    matchLabels:
      env: demo
```
```bash
kubectl apply -f ds.yaml
```
```bash
kubectl get ds
```
```bash
kubectl get ds -n kube-system
```
```bash
kubectl get ds -A
```
```bash
kubectl get pod -A
```
2) Creating CronJob
vim cronjob.yaml
```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: cronjob
spec:
  schedule: "*/5 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: cronjob
            image: busybox:1.28
            imagePullPolicy: IfNotPresent
            command:
            - /bin/sh
            - -c
            - date; echo 40daysofkubernetes
          restartPolicy: OnFailure
```
```bash
kubectl create -f cronjob.yaml
```
```bash
kubectl get cronjob
```
```bash
kubectl get job
```
```bash
kubectl logs cronjob-name
```
```bash
kubectl get pods --selector=job-name=cronjob-name
```
```bash
kubectl logs cronjob-name
```
You will see like this  
<img width="629" height="62" alt="image" src="https://github.com/user-attachments/assets/a0ad6416-75c3-4acb-a62b-26a8aadc7935" />  
  
Check out the today's lecture from Original Mentor #Piyushsachdeva  
Youtube - https://youtu.be/kvITrySpy_k?si=zh81bDYaDxCbtAVf  
Github - https://github.com/piyushsachdeva/CKA-2024/blob/main/Resources/Day12/task.md  
Special thanks to Piyush sachdeva &The CloudOps Community for the guidance!  
  
#Kubernetes #Docker # DevOps #TechLearning #Containerization #CloudOps  
#piyushsachdeva #TheCloudOpsCommunity  


