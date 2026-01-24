# **Day 21 : Manage TLS Certificates In a Kubernetes Cluster - Create Certificate Signing Request**  
Hello #LinkedInCommunity and #TheCloudOpsCommunity ,  
  
Today I want to share the Day 21 of #40DaysOfKubernetes journey with #Piyushsachdeva  
Below are the lecture of Day 21 learning.  
  
# What is TLS(Transport Layer Security)?  
Transport Layer Security (TLS) is the protocol that secures encrypted communication between clients and servers.  
In Kubernetes, TLS is used everywhere secure communication is needed:  
- Between kubectl and the API server  
- Between internal cluster components (e.g., kubelets ↔ API server)  
- Between workloads in the cluster  
- Within service meshes (mTLS)  
- For Ingress traffic from external clients  
  
# What is Managing TLS in K8s Cluster?  
In Kubernetes, managing TLS typically covers:  
- Generating key pairs (private + public certificate)  
- Requesting certificates using a CSR (Certificate Signing Request)  
- Approving certificates  
- Issuing certificates  
- Distributing them to workloads or services  
- Using them to secure traffic  
  
# What is CertificateSigningRequest(CSR) ?  
Kubernetes provides a built-in API called certificates.k8s.io/v1 to let users request certificates signed by a Certificate Authority (CA) that the administrator controls.  
  
# Why Use CSR?  
Instead of manual certificate issuance:  
- You generate a CSR locally (with your own key)  
- Kubernetes can accept the CSR and return a signed certificate  
  
# Real Life Scenario  
Company = Kubernetes cluster  
Security gate = API Server  
Employee card = TLS Certificate  
HR Department = Certificate Authority (CA)  
Application form = Certificate Signing Request (CSR)  
  
# How it Works?  
1) Employee applies --> submits an application ( CSR is created and submitted )  
2) HR reviews and approves ( Cluster administrator approves the CSR )  
3) Employee card is issued ( TLS certificate is signed and issued by the CA )  
4) Security gate scans the card ( API server verifies the certificate using TLS authentication )  
Only valid employees with a trusted card can enter the company—just like only valid clients with trusted certificates can access the Kubernetes API.  
  
# Key Point:  
Kubernetes secures communication by using TLS certificates to verify identity and encrypt traffic,  
and these certificates are requested (CSR), approved, and issued by a trusted Certificate Authority (CA).  
- Identity = Certificates prove who is connecting  
- Encryption = TLS protects data in transit  
- Trust = CA decides which certificates are valid  
  
-------------------------------------------------------------------------------------------------------------------------------------------------------------------
  
LAB  
### Step by Step ###  
Install Cluster with [Day06 Video](https://github.com/GGWPEZGAMER/day06)  
  
# 1) Generate a PKI private key and CSR  
To generate a key file  
```bash
openssl genrsa -out learner.key 2048
```
To generate a csr file  
```bash
openssl req -new -key learner.key -out learner.csr -subj "/CN=adam"
```
When u get the csr file decode to base64 without line break  
```bash
cat learner.csr | base64 | tr -d "\n"
```
# 2) Create a CertificateSigningRequest for learner and set the expiration date to 1 week  
Creating Yaml File  
vim csr.yaml  
```yaml
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: learner
spec:
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1Z6Q0NBVDhDQVFBd0VqRVFNQTRHQTFVRUF3d0hiR1ZoY201bGNqQ0NBU0l3RFFZSktvWklodmNOQVFFQgpCUUFEZ2dFUEFEQ0NBUW9DZ2dFQkFPVmJEeTc5MzRyR0dyQUFMOGIzUHpOTTlQNnZLSkE3N0RpRDBKWEoyY1RUCmR2NUQ2dmhBYWJiV1lzYUMzZElHNHMxeFB5Y0ZGUTR5eU83dVBIeEowRWRHOFlWSUJCcUhvcktZR1VkOW1nNHEKV2FnZ3QvRld1R1JZb2hHMEU4VlBKVTg0NGdDUjdzVHpkUU0xUmtZUWtrT1lkcmprQk1mWTF6UjJmajByNUpYLwpjRVpFbi9pVFcwalhVeVpTWEV1U1MwdlF3Mklmd1B4M0h2czJlZzVvbkhHUDRvMmdYa2dTYTJnL1h2MmxpZWhiCnNDTktPNEJET0twTnVhSEkyNlV1Wk9pQzNTRFpOc0dadldKUmZSNWxYVWV3YUo1ODdLSlNxMnVNMm45SEo5T1QKSTE1L3EzRjhBODBnRE9WZDY3RjdlMnA3MlF4OWY3bERHTjQyRHVpMnJWRUNBd0VBQWFBQU1BMEdDU3FHU0liMwpEUUVCQ3dVQUE0SUJBUUNXTXNObVpFOGRaVFFjUXJyN1dCOUNBSDMxa1dORE15Qm81cXgxRjF6b2d4bXlsbFB5CjliVFFBYmhiTGFpN2laM1hzbEFjVm9WU3diMldncFYzQXlQRTZKbkl5aHJmdWMxSzY3Uk1JZ1JSRlJsWmFBdzAKSVVJcGIzK3JFMURwblk3U0x1ME13VVh4RTUrWmNiMkJ6K0ttY0tudHdXWG0raGN3ekFKQVpwQWJtcFFTbUh2RApzcHZwQkV4TVV2SjlhZFhDbFE1WkpzU25wa3dHZmJmSTdHVjYvRUY0ZlZscytVa1hpMHI0SVpLTUw0Q0E3cEJWCjltNmlTR3d5VmhlVktPRCswM1krVFhrU3FxYTJSTDRQMzdtc01oUjdGT2ZTVGJ1d0FUdXpPQnNNYnYzakpUZE0KS3hEeUg2U1AxeW1HRzZ2N1NtRGd1M05Fdnc1RmozeWgvL3RwCi0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=
  signerName: kubernetes.io/kube-apiserver-client
  expirationSeconds: 604800 # one week
  usages:
  - client auth
```
# 3) Make sure to use the encoded value of csr in the request field  
Note:  
replace request with ur decoded base64 key  
```bash
kubectl apply -f csr.yaml
```
```bash
kubectl get certificate
```
Showing like that "not approving"  
<img width="1046" height="72" alt="Screenshot 2026-01-24 204700" src="https://github.com/user-attachments/assets/40f03196-0620-4eb8-b3b2-1ad57b6f7631" />  
  
# 4) Approve the csr  
```bash
kubectl certificate approve learner
```
```bash
kubectl get certificate
```
<img width="1016" height="87" alt="Screenshot 2026-01-24 204700" src="https://github.com/user-attachments/assets/307785ef-8fb9-4121-99f3-2d5b65a638ab" />  
  
# 5) Retrieve the certificate from the CSR  
```bash
kubectl get certificate learner -o yaml > issue.yaml
```
It will generate like that  
```yaml
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"certificates.k8s.io/v1","kind":"CertificateSigningRequest","metadata":{"annotations":{},"name":"learner"},"spec":{"expirationSeconds":604800,"request":"LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1Z6Q0NBVDhDQVFBd0VqRVFNQTRHQTFVRUF3d0hiR1ZoY201bGNqQ0NBU0l3RFFZSktvWklodmNOQVFFQgpCUUFEZ2dFUEFEQ0NBUW9DZ2dFQkFPVmJEeTc5MzRyR0dyQUFMOGIzUHpOTTlQNnZLSkE3N0RpRDBKWEoyY1RUCmR2NUQ2dmhBYWJiV1lzYUMzZElHNHMxeFB5Y0ZGUTR5eU83dVBIeEowRWRHOFlWSUJCcUhvcktZR1VkOW1nNHEKV2FnZ3QvRld1R1JZb2hHMEU4VlBKVTg0NGdDUjdzVHpkUU0xUmtZUWtrT1lkcmprQk1mWTF6UjJmajByNUpYLwpjRVpFbi9pVFcwalhVeVpTWEV1U1MwdlF3Mklmd1B4M0h2czJlZzVvbkhHUDRvMmdYa2dTYTJnL1h2MmxpZWhiCnNDTktPNEJET0twTnVhSEkyNlV1Wk9pQzNTRFpOc0dadldKUmZSNWxYVWV3YUo1ODdLSlNxMnVNMm45SEo5T1QKSTE1L3EzRjhBODBnRE9WZDY3RjdlMnA3MlF4OWY3bERHTjQyRHVpMnJWRUNBd0VBQWFBQU1BMEdDU3FHU0liMwpEUUVCQ3dVQUE0SUJBUUNXTXNObVpFOGRaVFFjUXJyN1dCOUNBSDMxa1dORE15Qm81cXgxRjF6b2d4bXlsbFB5CjliVFFBYmhiTGFpN2laM1hzbEFjVm9WU3diMldncFYzQXlQRTZKbkl5aHJmdWMxSzY3Uk1JZ1JSRlJsWmFBdzAKSVVJcGIzK3JFMURwblk3U0x1ME13VVh4RTUrWmNiMkJ6K0ttY0tudHdXWG0raGN3ekFKQVpwQWJtcFFTbUh2RApzcHZwQkV4TVV2SjlhZFhDbFE1WkpzU25wa3dHZmJmSTdHVjYvRUY0ZlZscytVa1hpMHI0SVpLTUw0Q0E3cEJWCjltNmlTR3d5VmhlVktPRCswM1krVFhrU3FxYTJSTDRQMzdtc01oUjdGT2ZTVGJ1d0FUdXpPQnNNYnYzakpUZE0KS3hEeUg2U1AxeW1HRzZ2N1NtRGd1M05Fdnc1RmozeWgvL3RwCi0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=","signerName":"kubernetes.io/kube-apiserver-client","usages":["client auth"]}}
  creationTimestamp: "2026-01-24T12:52:08Z"
  name: learner
  resourceVersion: "91673"
  uid: d8b8c9a2-26f1-40cd-bc8a-211de46719b8
spec:
  expirationSeconds: 604800
  groups:
  - kubeadm:cluster-admins
  - system:authenticated
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1Z6Q0NBVDhDQVFBd0VqRVFNQTRHQTFVRUF3d0hiR1ZoY201bGNqQ0NBU0l3RFFZSktvWklodmNOQVFFQgpCUUFEZ2dFUEFEQ0NBUW9DZ2dFQkFPVmJEeTc5MzRyR0dyQUFMOGIzUHpOTTlQNnZLSkE3N0RpRDBKWEoyY1RUCmR2NUQ2dmhBYWJiV1lzYUMzZElHNHMxeFB5Y0ZGUTR5eU83dVBIeEowRWRHOFlWSUJCcUhvcktZR1VkOW1nNHEKV2FnZ3QvRld1R1JZb2hHMEU4VlBKVTg0NGdDUjdzVHpkUU0xUmtZUWtrT1lkcmprQk1mWTF6UjJmajByNUpYLwpjRVpFbi9pVFcwalhVeVpTWEV1U1MwdlF3Mklmd1B4M0h2czJlZzVvbkhHUDRvMmdYa2dTYTJnL1h2MmxpZWhiCnNDTktPNEJET0twTnVhSEkyNlV1Wk9pQzNTRFpOc0dadldKUmZSNWxYVWV3YUo1ODdLSlNxMnVNMm45SEo5T1QKSTE1L3EzRjhBODBnRE9WZDY3RjdlMnA3MlF4OWY3bERHTjQyRHVpMnJWRUNBd0VBQWFBQU1BMEdDU3FHU0liMwpEUUVCQ3dVQUE0SUJBUUNXTXNObVpFOGRaVFFjUXJyN1dCOUNBSDMxa1dORE15Qm81cXgxRjF6b2d4bXlsbFB5CjliVFFBYmhiTGFpN2laM1hzbEFjVm9WU3diMldncFYzQXlQRTZKbkl5aHJmdWMxSzY3Uk1JZ1JSRlJsWmFBdzAKSVVJcGIzK3JFMURwblk3U0x1ME13VVh4RTUrWmNiMkJ6K0ttY0tudHdXWG0raGN3ekFKQVpwQWJtcFFTbUh2RApzcHZwQkV4TVV2SjlhZFhDbFE1WkpzU25wa3dHZmJmSTdHVjYvRUY0ZlZscytVa1hpMHI0SVpLTUw0Q0E3cEJWCjltNmlTR3d5VmhlVktPRCswM1krVFhrU3FxYTJSTDRQMzdtc01oUjdGT2ZTVGJ1d0FUdXpPQnNNYnYzakpUZE0KS3hEeUg2U1AxeW1HRzZ2N1NtRGd1M05Fdnc1RmozeWgvL3RwCi0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth
  username: kubernetes-admin
status:
  certificate: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMrRENDQWVDZ0F3SUJBZ0lSQUk2YU91eEtSOXM0Ymg4bkYvWGp5TFl3RFFZSktvWklodmNOQVFFTEJRQXcKRlRFVE1CRUdBMVVFQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TmpBeE1qUXhNalEzTWpsYUZ3MHlOakF4TXpFeApNalEzTWpsYU1CSXhFREFPQmdOVkJBTVRCMnhsWVhKdVpYSXdnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCCkR3QXdnZ0VLQW9JQkFRRGxXdzh1L2QrS3hocXdBQy9HOXo4elRQVCtyeWlRTyt3NGc5Q1Z5ZG5FMDNiK1ErcjQKUUdtMjFtTEdndDNTQnVMTmNUOG5CUlVPTXNqdTdqeDhTZEJIUnZHRlNBUWFoNkt5bUJsSGZab09LbG1vSUxmeApWcmhrV0tJUnRCUEZUeVZQT09JQWtlN0U4M1VETlVaR0VKSkRtSGE0NUFUSDJOYzBkbjQ5SytTVi8zQkdSSi80CmsxdEkxMU1tVWx4TGtrdEwwTU5pSDhEOGR4NzdObm9PYUp4eGorS05vRjVJRW10b1AxNzlwWW5vVzdBalNqdUEKUXppcVRibWh5TnVsTG1Ub2d0MGcyVGJCbWIxaVVYMGVaVjFIc0dpZWZPeWlVcXRyak5wL1J5ZlRreU5lZjZ0eApmQVBOSUF6bFhldXhlM3RxZTlrTWZYKzVReGplTmc3b3RxMVJBZ01CQUFHalJqQkVNQk1HQTFVZEpRUU1NQW9HCkNDc0dBUVVGQndNQ01Bd0dBMVVkRXdFQi93UUNNQUF3SHdZRFZSMGpCQmd3Rm9BVTFHdXRMNG5CSDN5VXRvNlQKNi82WDZkNDB2clF3RFFZSktvWklodmNOQVFFTEJRQURnZ0VCQURKUVNSZ291MUNTb1NtOXlFTllOZHIwdXY3SwpsODRuVWdCZzhZMTlreHF5empPZ0wzd3REbzlDSGg1MXdmajY4VHhLRzR6SmRlcWIxNjhCMTBVNDBvbVRvMDVkCkZZYXN3THFjNitPa1J6Uk9LMlBIUDRsSHJaMXl5NzdrTW8zWlNUMG4waDF1SHdrSGJ4a01IeWVsL3Q1Yk0wd1oKWU1pRk15ZXJGbjVYZmJzNWhaaEVVV3lYaVRzSEVCbFYzVkFCbmxiSDJodDFyc1NPeloxM3QwV1NrdzZiNmoxMgp2MzFKeFhUcE5uKzlxY1N4ZXJ1amh3K0hla05qSDhDc3ZNN1QvWDdlM2FzWnFIdUN1emJyRm9FeUlDRGhaR2Y1CnRiS0d2d0x0YlU1c3d4dTdYL29Jcis5aE5QV0VWUFR4eUlIcGhBdTlpTzJabkNseUdreC9rdVJ4VW9ZPQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
  conditions:
  - lastTransitionTime: "2026-01-24T12:52:29Z"
    lastUpdateTime: "2026-01-24T12:52:29Z"
    message: This CSR was approved by kubectl certificate approve.
    reason: KubectlApprove
    status: "True"
    type: Approved
```
Pls copy the request key and decode it  
```bash
echo "<certificate>" | base64 -d
```
After this showing like that  
<img width="793" height="427" alt="final" src="https://github.com/user-attachments/assets/0d18b888-9c73-4ff7-9c70-1d7a671b9a05" />  
  
Now,u can copy this and place this in kubeconfig...so, u can manage the cluster with the user of learner ....  
  
Check out the today's lecture from Original Mentor #Piyushsachdeva  
Youtube - https://youtu.be/LvPA-z8Xg4s?si=ienVWmcV3ey14FKj  
Github - https://github.com/piyushsachdeva/CKA-2024/blob/main/Resources/Day21/readme.md  
Special thanks to Piyush sachdeva &The CloudOps Community for the guidance!  
  
#Kubernetes #Docker # DevOps #TechLearning #Containerization #CloudOps  
#piyushsachdeva #TheCloudOpsCommunity  






