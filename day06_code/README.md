# **Day 6 : Setting Up a Multi-Node Kind Cluster**  
  
Hello #LinkedInCommunity and #TheCloudOpsCommunity ,  
Today I want to share the Day 6 of #40DaysOfKubernetes journey with #Piyushsachdeva .Below are the lecture of Day 6 learning.
  
### Installing KIND step by step ###  
Installing From Release Binaries  
On Linux:  
```bash
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.30.0/kind-linux-amd64
```
```bash
chmod +x ./kind
```
```bash
sudo mv ./kind /usr/local/bin/kind
```
Creating Cluster  
```bash
kind create cluster --image kindest/node:v1.29.12@sha256:62c0672ba99a4afd7396512848d6fc382906b8f33349ae68fb1dbfe549f70dec --name cka-cluster1
```
Reference link - https://kind.sigs.k8s.io/docs/user/quick-start/#installing-from-release-binaries    
Kind image link - https://github.com/kubernetes-sigs/kind/releases      
------------------------------------------------------------------------------------------------------------------------------------------------------------------
To check created cluster information  
```bash
kubectl cluster-info --context kind-cka-cluster1
```
To check nodes
```bash
kubectl get nodes
```
-------------------------------------------------------------------------------------------------------------------------------------------------------------------
To create Multi-nodes cluster
```bash
vim config.yaml
```
```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
```
To create second cluster among with this 3 nodes ( with config.yaml )  
```bash
kind create cluster --image kindest/node:v1.29.12@sha256:62c0672ba99a4afd7396512848d6fc382906b8f33349ae68fb1dbfe549f70dec --name cka-cluster2  --config config.yaml
```
To switch the clusters
```bash
kubectl config use-context my-cluster-name
```
  
Check out the today's lecture from Original Mentor #Piyushsachdeva  
Youtube - https://youtu.be/RORhczcOrWs?si=5Txmm9nzYpTjYrPI  
Github - https://github.com/piyushsachdeva/CKA-2024/blob/main/Resources/Day06/readme.md  
  
#Kubernetes #Docker # DevOps #TechLearning #Containerization #CloudOps  
#piyushsachdeva #TheCloudOpsCommunity  



