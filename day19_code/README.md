# **Day 19 : Kubernetes configmap and secret**  
Hello #LinkedInCommunity and #TheCloudOpsCommunity ,   
  
Today I want to share the Day 19 of #40DaysOfKubernetes journey with #Piyushsachdeva  
Below are the lecture of Day 19 learning.  
  
# What is Kubernetes ConfigMap and Secret?  
Before going into Kubernetes ConfigMaps and Secrets, let’s first talk about the .env file.  
A .env file is a file that collects data used to inject configuration into an application without modifying the source code written by developers.  
In other words, when you want to separate an application into Development, Staging, and Production environments, you do not need to duplicate or change the code.  Instead, you only duplicate or adjust the configuration. This makes it easy to manage environment-specific variables from a single file.  
These configurations typically include databases, message brokers, ports, authentication settings, APIs, and feature flags.  
  
When using Docker, .env files can be injected using docker run -e. This approach avoids hardcoding values inside the application.  
Instead, variables are declared in the .env file, which simplifies management and reduces complexity.  
  
Simply put, .env files are used to make applications easy to clone and easy to reconfigure by changing settings only.  
Once you understand the .env file, let’s move back to Kubernetes ConfigMaps and Secrets.  
In Kubernetes, ConfigMaps and Secrets are data store objects that hold the same kind of settings you would normally place in a .env file.  
  
Kubernetes separates .env-type data into ConfigMaps and Secrets.  
A Secret follows the same concept as a ConfigMap, but it is used only for credentials and sensitive data.  
These ConfigMap and Secret objects are then consumed by Pods. Secrets are also Base64-encoded.  
  
Data typically stored in a ConfigMap:  
- Application mode (dev / staging / prod)  
- Hostname / URL  
- Port numbers  
- Feature flags  
- Configuration file contents  
  
Data typically stored in a Secret:  
- Database username and password  
- API keys  
- Tokens (JWT, OAuth)  
- TLS private keys  
- Docker registry credentials  
  
Easy way to remember:  
  
.env = ConfigMap + Secret  
  
In this lab, the usage of ConfigMaps and Secrets is explained in detail.  
Please check the GitHub repository for more information.  
-------------------------------------------------------------------------------------------------------------------------------------------------------------------
  
LAB  
### Step by Step ###  
Install Cluster with [Day06 Video](https://github.com/GGWPEZGAMER/day06)  
  
1) Creating ConfigMap  
```bash
kubectl create cm app-cm --from-literal=firstname=Thiha --from-literal=lastname=Thu
```
```bash
kubectl get cm
```
```bash
kubectl describe cm app-cm
```
Showing like that  
<img width="826" height="581" alt="1 - Copy" src="https://github.com/user-attachments/assets/6f32576c-f294-40bd-b18d-185bd74304e2" />  
  
2) Creating Pod  
```bash
vim configmap.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp
  labels:
    name: myapp-pod
spec:
  containers:
  - name: myapp-container
    image: registry.k8s.io/busybox:latest
    env:
    - name: FIRSTNAME
      valueFrom:
        configMapKeyRef:
          name: app-cm
          key: firstname
    command: ['sh', '-c', 'echo the app is running && sleep 3600']
```
```bash
kubectl apply -f configmap.yaml
```
```bash
kubectl get pod
```
```bash
kubectl exec -it myapp -- sh
```
Inside the Pod  
```bash
echo $FIRSTNAME
```
Showing like that  
<img width="442" height="95" alt="2" src="https://github.com/user-attachments/assets/0078dab9-e9cf-42b3-9cc0-53acf7817aa8" />  
  
```bash
kubectl describe pod myapp
```
Check it  
<img width="833" height="141" alt="3" src="https://github.com/user-attachments/assets/31309944-41e4-4296-8bbf-a4a9ff83e27b" />  
  
3) Creating Secret  
Firstly , to change base64 encode  
```bash
echo -n 'my-app' | basd64
echo -n '39528$vdg7Jb' | base64
```
```bash
vim secret.yaml
```
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: test-secret
data:
  username: bXktYXBw
  password: Mzk1MjgkdmRnN0pi
```
```bash
kubectl apply -f secret.yaml
```
```bash
kubectl get secret test-secret
```
```bash
kubectl describe secret test-secret
```
Showing like that  
<img width="892" height="462" alt="4" src="https://github.com/user-attachments/assets/bd37222c-1490-4fbd-9708-056f6a3847b9" />  
  
# Create a Secret directly with kubectl  
If you want to skip the Base64 encoding step, you can create the same Secret using the kubectl create secret command.  
```bash
kubectl create secret generic test-secret --from-literal='username=my-app' --from-literal='password=39528$vdg7Jb'
```
# Create a Pod that has access to the secret data through a Volume  
```bash
vim secret-pod.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-test-pod
spec:
  containers:
    - name: test-container
      image: nginx
      volumeMounts:
        # name must match the volume name below
        - name: secret-volume
          mountPath: /etc/secret-volume
          readOnly: true
  # The secret data is exposed to Containers in the Pod through a Volume.
  volumes:
    - name: secret-volume
      secret:
        secretName: test-secret
```
```bash
kubectl apply -f secret-pod.yaml
```
```bash
kubectl get pod secret-test-pod
```
```bash
kubectl exec -it secret-test-pod -- sh
```
if u r not with 'sh' type 'bash'  
Inside the pod  
```bash
ls /etc/secret-volume
```
It will show 'password' 'username'  
run this command  
```bash
echo "$( cat /etc/secret-volume/username )"
echo "$( cat /etc/secret-volume/password )"
```
The Output is your username and password  
```bash
my-app
39528$vdg7Jb
```
Pls do some exercise with this link  
https://kubernetes.io/docs/tasks/inject-data-application/distribute-credentials-secure/  
------------------------------------------------------------------------------------------------------------------------------------------------------------------
  
Check out the today's lecture from Original Mentor #Piyushsachdeva  
Youtube - https://youtu.be/Q9fHJLSyd7Q?si=JGa4J9dlMgS1hC3e  
Github - https://github.com/piyushsachdeva/CKA-2024/blob/main/Resources/Day19/readme.md  
Special thanks to Piyush sachdeva &The CloudOps Community for the guidance!  
  
#Kubernetes #Docker # DevOps #TechLearning #Containerization #CloudOps  
#piyushsachdeva #TheCloudOpsCommunity  









