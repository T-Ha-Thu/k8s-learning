# **Day 27 : Setup a Multi Node Kubernetes Cluster Using Kubeadm in AWS**  
  
Hello #LinkedInCommunity and #TheCloudOpsCommunity ,  
  
Today I want to share the Day 26 of #40DaysOfKubernetes journey with #Piyushsachdeva  
Below are the lecture of Day 26 learning.  

# EC2 setup and security group in AWS   
1.In the navigation pane, choose EC2  
2.Select Security group  
3.Create Security group for master node  
<img width="1911" height="806" alt="1" src="https://github.com/user-attachments/assets/b959f581-16bd-495d-b7f3-4a2644a6fdb0" />  
  
4.Create Security group for worker node  
<img width="1602" height="726" alt="2" src="https://github.com/user-attachments/assets/967eab93-acaf-4e0a-af34-1b8eba323519" />  
  
5.Create EC2 for master node with this specification  
<img width="1542" height="837" alt="3" src="https://github.com/user-attachments/assets/224e89b4-9dd0-4342-a724-451feaeeccfa" />  
  
6.Create EC2 for worker node with this specification  
<img width="1535" height="832" alt="4" src="https://github.com/user-attachments/assets/b5a63742-fa88-4e53-9be6-bc2406975df6" />  
  
7.When EC2 are ready to use  
<img width="1600" height="427" alt="5" src="https://github.com/user-attachments/assets/eb0dbffd-7003-415c-b6a4-9ca4939f9d0e" />  
  
8.Pls copy the SSH command
<img width="1883" height="737" alt="6" src="https://github.com/user-attachments/assets/04958b8f-86e1-4e89-ac76-1c80f3f15ae8" />  
  
9.Before login with SSH pls download first the .pem key and pls go to the .pem directory 
<img width="752" height="221" alt="7" src="https://github.com/user-attachments/assets/6ccb67cf-d4ae-411a-86fb-177acf7533f0" />  
  
10.Finally you are inside the EC2 of master node also worker node too  
<img width="1190" height="667" alt="8" src="https://github.com/user-attachments/assets/8aae18c5-98c7-49fe-aa0b-1dd43a5274b8" />  

# Installation of Kubeadm
1.SSH into the Master EC2 server  
2.Disable Swap using the below commands  
```bash
swapoff -a
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
```
3.Forwarding IPv4 and letting iptables see bridged traffic  
```bash
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# sysctl params required by setup, params persist across reboots
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

# Apply sysctl params without reboot
sudo sysctl --system

# Verify that the br_netfilter, overlay modules are loaded by running the following commands:
lsmod | grep br_netfilter
lsmod | grep overlay

# Verify that the net.bridge.bridge-nf-call-iptables, net.bridge.bridge-nf-call-ip6tables, and net.ipv4.ip_forward system variables are set to 1 in your sysctl config by running the following command:
sysctl net.bridge.bridge-nf-call-iptables net.bridge.bridge-nf-call-ip6tables net.ipv4.ip_forward
```
4.Install container runtime  
```bash
curl -LO https://github.com/containerd/containerd/releases/download/v1.7.14/containerd-1.7.14-linux-amd64.tar.gz
sudo tar Cxzvf /usr/local containerd-1.7.14-linux-amd64.tar.gz
curl -LO https://raw.githubusercontent.com/containerd/containerd/main/containerd.service
sudo mkdir -p /usr/local/lib/systemd/system/
sudo mv containerd.service /usr/local/lib/systemd/system/
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml
sudo systemctl daemon-reload
sudo systemctl enable --now containerd

# Check that containerd service is up and running
systemctl status containerd
```
5.Install runc
```bash
curl -LO https://github.com/opencontainers/runc/releases/download/v1.1.12/runc.amd64
sudo install -m 755 runc.amd64 /usr/local/sbin/runc
```
6.install cni plugin  
```bash
curl -LO https://github.com/containernetworking/plugins/releases/download/v1.5.0/cni-plugins-linux-amd64-v1.5.0.tgz
sudo mkdir -p /opt/cni/bin
sudo tar Cxzvf /opt/cni/bin cni-plugins-linux-amd64-v1.5.0.tgz
```
7.Install kubeadm, kubelet and kubectl
```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gpg

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update
sudo apt-get install -y kubelet=1.29.6-1.1 kubeadm=1.29.6-1.1 kubectl=1.29.6-1.1 --allow-downgrades --allow-change-held-packages
sudo apt-mark hold kubelet kubeadm kubectl

kubeadm version
kubelet --version
kubectl version --client
```
8.Configure crictl to work with containerd  
```bash
sudo crictl config runtime-endpoint unix:///var/run/containerd/containerd.sock
```
9.Initialize Control Plane  
```bash
sudo kubeadm init --pod-network-cidr=192.168.0.0/16 --apiserver-advertise-address=172.31.89.68 --node-name master
```
note- apiserver-addvertise-address=172.31.0.0/16  
note- node-name can use whatever u want "master" or "control-plane"  
  
10.Prepare kubeconfig  
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
11.Install calico  
```bash
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.28.0/manifests/tigera-operator.yaml
curl https://raw.githubusercontent.com/projectcalico/calico/v3.28.0/manifests/custom-resources.yaml -O
kubectl apply -f custom-resources.yaml
```
# Perform the below steps on both the worker nodes  
- Perform steps 1-8 on both the nodes  
- Run the command generated in step 9 on the Master node which is similar to below
```bash
sudo kubeadm join 172.31.71.210:6443 --token xxxxx --discovery-token-ca-cert-hash sha256:xxx
```
- If you forgot to copy the command, you can execute below command on master node to generate the join command again
```bash
kubeadm token create --print-join-command
```
After step 10 in master node pls copy like this  
```bash
kubeadm join 172.31.89.206:6443 --token r3fz2k.dpg7h2ot57wqncf3 \
        --discovery-token-ca-cert-hash sha256:60ae6ed6ec6839ba4f32805705c037e6ed5472be6b45327705a050371a6295fb
```
<img width="1612" height="420" alt="9" src="https://github.com/user-attachments/assets/3289c530-f10b-4cff-bbdb-b715fa914343" />  
  
U can get node from master node with this command and then u will se like this ....  
  
<img width="795" height="142" alt="10" src="https://github.com/user-attachments/assets/6ec3aa0c-854b-4440-8739-e3c3cf54fb28" />  
  
Ok now, u have done ur k8s cluster using Kubeadm in AWS...  

Check out the today's lecture from Original Mentor #Piyushsachdeva  
Youtube - https://www.youtube.com/watch?v=WcdMC3Lj4tU&t=765s  
Github - https://github.com/piyushsachdeva/CKA-2024/blob/main/Resources/Day27/readme.md  
Special thanks to Piyush sachdeva &The CloudOps Community for the guidance!  
  
#Kubernetes #Docker # DevOps #TechLearning #Containerization #CloudOps  
#piyushsachdeva #TheCloudOpsCommunity  















