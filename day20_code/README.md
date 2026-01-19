# **Day 20 : SSL/TLS Explained Simply - How SSL/TLS Works?**  
Hello #LinkedInCommunity and #TheCloudOpsCommunity ,   
  
Today I want to share the Day 20 of #40DaysOfKubernetes journey with #Piyushsachdeva  
Below are the lecture of Day 20 learning.  
  
# How SSL and TLS works?  
What an SSL / TLS Certificate Is  
  
- A digital certificate is like an ID card for a website — it proves the website is really who it says it is.  
- It contains the website’s public key and other information signed by a trusted authority.  
  
# Why we Need SSL/TLS ?  
When you visit a website, you want:  
- Encryption (so no one can eavesdrop)  
- Authentication (to verify you're talking to the real site)  
- Integrity (so data isn’t tampered with)  
SSL/TLS certificates provide all three.  
  
# Step by Step SSL/TLS Handshake Process  
1) Step 1 - Client Hello  
2) Step 2 - Server Hello  
3) Step 3 - Certificate Verification  
4) Step 4 - Session Key Creation  
5) Step 5 - Secure Connection Created  
  
# What a Certificate Contains  
- Public key  
- Website name / domain  
- CA that issued it  
- Validity dates (start and expiry)  
- Digital signature from CA  
  
# What a Certificate Authority (CA) is  
- A trusted third party that verifies identities. Browser trusts a list of CAs.  
- When a CA signs a certificate, the browser trusts that site.  
- What you see in Your Browser. When HTTPS works:  
-Lock icon appears in the address bar  
- Your connection is encrypted and authenticated  
- This means the site has a valid SSL/TLS certificate.  
  
# Key Point  
- Browser sends a hello  
- Server responds with certificate  
- Browser verifies certificate  
- Browser sends encrypted session key  
- Secure communication starts  
  
Check out the today's lecture from Original Mentor #Piyushsachdeva  
Youtube - https://youtu.be/njT5ECuwCTo?si=yX4oR4EJmryJes6G  
Github -  https://github.com/piyushsachdeva/CKA-2024/blob/main/Resources/Day20/task.md  
Special thanks to Piyush sachdeva &The CloudOps Community for the guidance!  
  
#Kubernetes #Docker # DevOps #TechLearning #Containerization #CloudOps  
#piyushsachdeva #TheCloudOpsCommunity  
