# **Day 25 : Kubernetes Service Account - RBAC Continued**  

Hello #LinkedInCommunity and #TheCloudOpsCommunity ,  
  
Today I want to share the Day 25 of #40DaysOfKubernetes journey with #Piyushsachdeva  
Below are the lecture of Day 25 learning.   

# What is Service Account ?
A ServiceAccount in Kubernetes is a non-human identity used by pods and applications to authenticate and access the Kubernetes API securely.  

Key points :  
- Used by pods, not humans  
- Works with RBAC for permissions  
- Automatically mounted as a token inside pods  
- Commonly used in CI/CD, controllers, automation

# Let's do LAB  
# (1) Creating Service Account and connecting with namespace  
Use day22_23_24 [video](https://github.com/T-Ha-Thu/k8s-learning/edit/main/day22_23_24_code/README.md)  
After the previous day step..let's create service account.  
Before creating service account ...  
Check first the default service account  
```bash
kubectl get sa
kubectl get sa -A | grep default
kubectl describe sa default
```
like this  
<img width="807" height="228" alt="image" src="https://github.com/user-attachments/assets/608a1bdb-bafe-4daa-99f7-081927b3ae6c" />  

# Checking the default service account yaml file  
```bash
kubectl get sa default -o yaml
```
<img width="807" height="228" alt="3" src="https://github.com/user-attachments/assets/bdde40c8-dcb2-471c-9698-7a70f30ef94a" />  

# Creat custom service account
```bash
kubectl create sa build-sa
kubectl describe sa build-sa
```
<img width="677" height="222" alt="4" src="https://github.com/user-attachments/assets/5b63b658-bcd3-4c7a-ae95-8c632d1d64c7" />  

# Creating secret for service account with yaml file  
reference from this link - https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/  
vim secret.yaml  
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: build-robot-secret
  annotations:
    kubernetes.io/service-account.name: build-sa
type: kubernetes.io/service-account-token
```
```bash
kubectl apply -f secret.yaml
```
```bash
kubectl get secret
```
<img width="731" height="70" alt="5" src="https://github.com/user-attachments/assets/a0dd3490-29bf-4389-b88d-9337cb65223a" />  

```bash
kubectl describe secret/build-robot-secret
```
<img width="1211" height="462" alt="6" src="https://github.com/user-attachments/assets/2575c3e9-0565-4adb-b187-3ec866646d40" />  

# Testing build-sa can read pod  
```bash
kubectl get pod --as build-sa
```
u will see like this  
<img width="1206" height="68" alt="7" src="https://github.com/user-attachments/assets/1335052b-6b5b-4b2c-afac-7a0d88d83a59" />  
because the service build-sa didn't have role and rolebinding.  
```bash
kubectl auth can-i get pods --as build-sa
```
<img width="962" height="48" alt="image" src="https://github.com/user-attachments/assets/7f1b3474-a322-4961-91c6-e79e18a7385e" />  
  
# Creating role and rolebinding for build-sa  
```bash
kubectl create role build-role --verb=list,get,watch --resource=pod
```
<img width="802" height="92" alt="9" src="https://github.com/user-attachments/assets/beb94a29-5899-43e6-a69b-79e5a6604d55" />  

```bash
kubectl create rolebinding rolebuild-sabinding --role=build-role --user=build-sa
```
<img width="852" height="92" alt="10" src="https://github.com/user-attachments/assets/daf45270-0a2f-4794-ba01-f8b49bea926e" />  

U can check with this command  
```bash
kubectl auth can-i get pods --as build-sa
kubectl get pods --as build-sa
```
if u run second command to check u need to create first one pod
<img width="823" height="136" alt="11" src="https://github.com/user-attachments/assets/1aecb099-16d7-461b-8ac1-139050985261" />  

U can check the pod's service account and mount point with following command  
```bash
kubectl describe pod nginx-pod
```
u can check service account section and mounts section  

# Actual checking of mount point inside pod  
```bash
kubectl exec -it nginx-pod --bash
```
When u are inside pod
```bash
cd /var/run/secrets/kubernetes.io/serviceaccount
ls -alrth
```
<img width="1027" height="328" alt="12" src="https://github.com/user-attachments/assets/de688944-44d9-4520-901e-9869cd7e56f6" />  

U can check the token with the above secret  
<img width="1185" height="222" alt="13" src="https://github.com/user-attachments/assets/803e6789-10ac-4849-aecb-f2d0ce8e6cbc" />  

Check out the today's lecture from Original Mentor #Piyushsachdeva  
Youtube - https://www.youtube.com/watch?v=k2iCq7IlMKM&t=353s  
Github - https://github.com/piyushsachdeva/CKA-2024/tree/main/Resources/Day25  
Special thanks to Piyush sachdeva &The CloudOps Community for the guidance!  
  
#Kubernetes #Docker # DevOps #TechLearning #Containerization #CloudOps  
#piyushsachdeva #TheCloudOpsCommunity  
















