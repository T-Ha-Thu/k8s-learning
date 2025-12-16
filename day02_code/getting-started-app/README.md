Day 2: How to Dockerize a Project  
Hello #LinkedInCommunity and #TheCloudOpsCommunity ,  
  
Today I want to share the Day 2 of #40DaysOfKubernetes journey with #Piyushsachdeva .  
Below are the lecture of Day 2 learning.  
---------------------------------------------------------------------------------------------------------------------------------  
Docker and Kubernetes sandbox environment,  
https://labs.play-with-docker.com/  
https://labs.play-with-k8s.com/  
---------------------------------------------------------------------------------------------------------------------------------  
Installing Docker Desktop Client  
https://www.docker.com/products/docker-desktop/  
  
### Step-by-Step Process ###  

### Get Started ###  
  
- Clone a sample git repository using the below command or use your project for the demo:  
```bash
git clone https://github.com/GGWPEZGAMER/my-getting-started-app.git
```  
---------------------------------------------------------------------------------------------------------------------------------  
- cd into the directory  
```bash
cd my-getting-started-app/
```
---------------------------------------------------------------------------------------------------------------------------------  
- Create an empty file with the name Dockerfile  
```bash
touch Dockerfile
```
----------------------------------------------------------------------------------------------------------------------------------  
- Using the text editor of your choice, paste the below content ( Details about each of these have already shared in the video )
```bash
FROM node:18-alpine
RUN sed -i 's|https://dl-cdn.alpinelinux.org|https://mirrors.aliyun.com|g' /etc/apk/repositories
RUN apk add --no-cache python3 make g++
WORKDIR /app
COPY . .
RUN npm install --production
CMD ["node", "src/index.js"]
EXPOSE 3000
```
----------------------------------------------------------------------------------------------------------------------------------  
- Build the docker image using the application code and Dockerfile  
```bash
docker build -t day02-todo .
```
----------------------------------------------------------------------------------------------------------------------------------  
- Verify the image has been created and stored locally using the below command:  
```bash
docker images
```
------------------------------------------------------------------------------------------------------------------------------------  
-Create a public repository on hub.docker.com and push the image to remote repo  
```bash
docker login
docker tag day02-todo:latest username/new-reponame:tagname
docker images
docker push username/new-reponame:tagname
```
-------------------------------------------------------------------------------------------------------------------------------------  
- To pull the image to another environment, you can use below command  
```bash
docker pull username/new-reponame:tagname
```
--------------------------------------------------------------------------------------------------------------------------------------  
- To start the docker container, use below command  
```bash
docker run -dp 3000:3000 username/new-reponame:tagname
```
---------------------------------------------------------------------------------------------------------------------------------------  
- Verify your app.If you have followed the above steps correctly, your app should be listening on localhost:3000  
- To enter(exec) into the container, use the below command  
```bash
docker exec -it conatinername sh
or
docker exec -it containerid sh
```
----------------------------------------------------------------------------------------------------------------------------------------  
- To view docker logs  
```bash
docker logs containername
or
docker logs containerid
```
------------------------------------------------------------------------------------------------------------------------------------------  
  
Check out the today's lecture from Original Mentor #Piyushsachdeva  
Youtube - https://youtu.be/nfRsPiRGx74?si=WoB9CzqbINdjEbb_  
Github - https://github.com/piyushsachdeva/CKA-2024/blob/main/Resources/Day02/readme.md  
  
#Kubernetes #Docker # DevOps #TechLearning #Containerization #CloudOps   
#piyushsachdeva #TheCloudOpsCommunity  
