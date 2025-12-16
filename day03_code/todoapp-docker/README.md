# **Day 3: Multi Stage Docker Build**  
Hello #LinkedInCommunity and #TheCloudOpsCommunity ,  
  
Today I want to share the Day 3 of #40DaysOfKubernetes journey with #Piyushsachdeva .  
Below are the lecture of Day 3 learning.  
---------------------------------------------------------------------------------------------------------------------------------  
Docker and Kubernetes sandbox environment,  
https://labs.play-with-docker.com/  
https://labs.play-with-k8s.com/  
---------------------------------------------------------------------------------------------------------------------------------  
### Step-by-Step Process ###  
  
### Get Started ###  
  
- Clone the below sample repository, or you can use any web application that you have  
```bash
git clone https://github.com/piyushsachdeva/todoapp-docker.git
```
---------------------------------------------------------------------------------------------------------------------------------  
- cd into the directory  
```bash
cd todoapp-docker/
```
---------------------------------------------------------------------------------------------------------------------------------  
- Create an empty file with the name Dockerfile  
```bash
touch Dockerfile
```
----------------------------------------------------------------------------------------------------------------------------------  
- Using the text editor of your choice, paste the below content ( Details about each of these have already shared in the video )  
```bash
FROM node:18-alpine AS installer
WORKDIR /app
COPY package*.json ./
RUN npm ci --legacy-peer-deps
RUN npm install
COPY . .
RUN npm run build
FROM nginx:latest AS deployer
COPY --from=installer /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```
----------------------------------------------------------------------------------------------------------------------------------  
- Build the docker image using the application code and Dockerfile  
```bash
docker build -t todoapp-docker .
```
----------------------------------------------------------------------------------------------------------------------------------  
- Verify the image has been created and stored locally using the below command:  
```bash
docker images
```
------------------------------------------------------------------------------------------------------------------------------------  
- Create a public repository on hub.docker.com and push the image to remote repo  
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
docker run -dp 3000:80 username/new-reponame:tagname
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
- To view the content of Docker container  
```bash
docker inspect
```
------------------------------------------------------------------------------------------------------------------------------------------  
- Cleanup the old docker images from local repo using below command:  
```bash
docker image rm image-id
```
------------------------------------------------------------------------------------------------------------------------------------------  
Check out the today's lecture from Original Mentor #Piyushsachdeva  
Youtube - https://youtu.be/ajetvJmBvFo?si=PIPILeWP317JAvUg  
Github - https://github.com/piyushsachdeva/CKA-2024/blob/main/Resources/Day03/readme.md  
  
#Kubernetes #Docker # DevOps #TechLearning #Containerization #CloudOps  
#piyushsachdeva #TheCloudOpsCommunity  
