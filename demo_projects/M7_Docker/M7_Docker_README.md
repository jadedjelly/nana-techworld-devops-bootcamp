# Module 7 - Containers with Docker - Projects
1. [Use Docker for local development](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/demo_projects/M7_Docker/M7_Docker_README.md#Use-Docker-for-local-development)
2. [Docker Compose - Run multiple Docker containers](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/demo_projects/M7_Docker/M7_Docker_README.md#Docker-Compose---Run-multiple-Docker-containers)
3. [Dockerize Nodejs application and push to private Docker registry](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/demo_projects/M7_Docker/M7_Docker_README.md#Dockerize-Nodejs-application-and-push-to-private-Docker-registry)
4. [Deploy Docker application on a server with Docker Compose](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/demo_projects/M7_Docker/M7_Docker_README.md#Deploy-Docker-application-on-a-server-with-Docker-Compose)
5. [Persist data with Docker Volumes](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/demo_projects/M7_Docker/M7_Docker_README.md#Persist-data-with-Docker-Volumes)
6. [Create Docker repository on Nexus and push to it](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/demo_projects/M7_Docker/M7_Docker_README.md#Create-Docker-repository-on-Nexus-and-push-to-it)
7. [Deploy Nexus as Docker container](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/demo_projects/M7_Docker/M7_Docker_README.md#Deploy-Nexus-as-Docker-container)


## Demo Project: 
### Use Docker for local development

#### Technologies used:
- Docker, Node.js, MongoDB, MongoExpress

#### Project Description:
1. Create Dockerfile for Nodejs application and build Docker image
2. Run Nodejs application in Docker container and connect to MongoDB database container locally.
3. Also run MongoExpress container as a UI of the MongoDB database.



- 1st we pull mongo DB & Mongo Express
```bash
docker pull mongo
docker pull mongo-express
```
![07_image65.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image65.png)

- we create a mongo network
```bash
docker network create mongo-network
```

- Run mongo & connect to the mongo network
```bash
docker run -p 27017:27017 -d -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=password --name mongodb --net mongo-network mongo
```
- Now we start mongoexpress, same as before we look at the doc on it via docker hub [here](https://hub.docker.com/_/mongo-express) (we also add the mongo-network variable so we can connect it)
```bash
docker run -d \
-p 8081:8081 \
-e ME_CONFIG_MONGODB_ADMINUSERNAME=admin \
-e ME_CONFIG_MONGODB_ADMINPASSWORD=password \
--net mongo-network \
--name mongo-express \
-e ME_CONFIG_MONGODB_SERVER=mongodb \
mongo-express
```
![07_image67.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image67.png)


*had an issue where "password" didnt work, till I shortened it to pass....*

- from the Mongro-express UI, we create a new DB called "user-account"
- now we connect to the db from the nodeJS app
- we open the server.js file in intellij (ensure the connection info is correct)
- cd to the app/ and run it
```bash
# run ci, as it's faster
npm ci
npm server.js
```
- access localhost:3000
- update the info
![07_image68.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image68.png)
- go to localhost:8080 to see the data in the db
![07_image69.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image69.png)

We have a fully functioning NodeJS app, which has persistant data in a MongoDB w/ MongoUI all of which running in a docker Container

---------------------------------------------------------------------------------------------------
## Demo Project: 
### Docker Compose - Run multiple Docker containers

#### Technologies used:
- Docker, MongoDB, MongoExpress

#### Project Description:
1. Write Docker Compose file to run MongoDB and MongoExpress containers



- create a new yaml file
```yaml
version: '3'
services:
  # my-app:
    # image: ${docker-registry}/my-app:1.0
    # ports:
     # - 3000:3000
  mongodb:
    image: mongo
    ports:
     - 27017:27017
    environment:
     - MONGO_INITDB_ROOT_USERNAME=admin
     - MONGO_INITDB_ROOT_PASSWORD=password
    volumes:
     - mongo-data:/data/db
  mongo-express:
    image: mongo-express
    # To make sure Mongo express can connect to MongoDB container, when we start, this is down to the possibility that Mongo
    # express starts first, and will need to wait till the DB is up
    restart: always
    ports:
     - 8081:8081
    environment:
     - ME_CONFIG_MONGODB_ADMINUSERNAME=admin
     - ME_CONFIG_MONGODB_ADMINPASSWORD=password
     - ME_CONFIG_MONGODB_SERVER=mongodb
    depends_on:
     - "mongodb"
volumes:
  mongo-data:
    driver: local
```
- Nana goes on to say, that you can use other methods like *depends_on*, *healthcheck* etc instead of the "*restart policy*"
- save the file as mongo.yaml & in the js-app folder
- run the compose file
```yaml
# the -f is for file, and up tells docker what action to take
docker-compose -f mongo.yaml up
```
You can see during the terminal output, Mongo-Express retarted a few times while it waited to get a connection to the DB

![07_image70.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image70.png)
- checking localhost:8081 & localhost:3000 again, we can see the app & DB are communicating again
![07_image71.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image71.png)
![07_image72.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image72.png)
![07_image73.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image73.png)

- To shut the containers down, you can run the usual "docker stop <container IDs>" or the below docker-compose will do the same
```bash
docker-compose -f mongo.yaml down
```
![07_image74.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image74.png)
- this command also, **removes** the containers & networks

---------------------------------------------------------------------------------------------------
## Demo Project: 
### Dockerize Nodejs application and push to private Docker registry

#### Technologies used:
- Docker, Node.js, Amazon ECR

#### Project Description:
1. Write Dockerfile to build a Docker image for a Nodejs application
2. Create private Docker registry on AWS (Amazon ECR)
3. Push Docker image to this private repository


- We will create a Dockerfile based on the mongo.yaml file we created previously
```go
FROM node:20-alpine

ENV MONGO_DB_USERNAME=admin \
    MONGO_DB_PWD=password

RUN mkdir -p /home/app

COPY ./app /home/app

# no need for /home/app/server.js because of WORKDIR
CMD ["node", "server.js"]
```
- with the Dockerfile created, we open terminal from inside the project folder, and run:
```bash
# -t what we call the image, plus using the : we give it a version number
# then we give the location of the Dockerfile, because we are executing from inside the project folder, we can just type
# .
docker build -t my-app:1.0 .
```
![07_image75.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image75.png)
From above, you can see the build steps [1/3]-[3/3]
- when we run docker images we can see our newly created image

![07_image76.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image76.png)
- We have just done what Jenkins would do (if we had it running), 
   - The way it sounds, Nana has setup a trigger for Jenkins to monitor / build when a repo changes (I couldn't get this to work last time)
- After the image is built, you would push it to a repo
- when we run the container we get an error (which makes sense seeing as we didnt tell it where to run)
- we add two lines between the COPY commadn and the CMD command, as below:
```go
# set default dir so that next commands executes in /home/app dir
WORKDIR /home/app
# will execute npm install in /home/app because of WORKDIR
RUN npm install
```
![07_image77.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image77.png)

- you can get into the container by running the exec command as below:
```bash
docker exec -it <container ID> /bin/sh
```

- we are going to recreate the Dockerfile, making it more efficient (basically removing the files we dont need to copy over)

![07_image78.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image78.png)
![07_image79.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image79.png)
![07_image80.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image80.png)

---------------------------------------------------------------------------------------------------
## Demo Project: 
### Deploy Docker application on a server with Docker Compose

#### Technologies used:
- Docker, Amazon ECR, Node.js, MongoDB, MongoExpress

#### Project Description:
1. Copy Docker-compose file to remote server
2. Login to private Docker registry on remote server to fetch our app image
3. Start our application container with MongoDB and MongoExpress services using docker compose


- Create a private repo for Docker
  - login to aws console
- open ecr
  - click get started
  - we call name it after the app (my-app), leave everything as default
  - 631368147597.dkr.ecr.eu-west-2.amazonaws.com/my-app
  - NOTE: ECR is a single repo per image
![07_image81.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image81.png)
- connect & login to ECR from local terminal
- click "view push commands" gives you line by line what needs to be executed
![07_image82.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image82.png)
![07_image83.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image83.png)

- Now we will make some changes to the App, rebuild & push a new version to AWS repo
  - (remember, re-auth with ecr (if you have shut your terminal down previously))
  - docker build -t my-app:1.0 .
  - update the tags w/ (docker tag my-app:1.0 631368147597.dkr.ecr.eu-west-2.amazonaws.com/my-app:1.0)
  - and push to ecr (docker push 631368147597.dkr.ecr.eu-west-2.amazonaws.com/my-app:1.0)
- we then start all 3x containers locally (probably to test to see it works) - running docker-compose -f mongo.yaml up

---------------------------------------------------------------------------------------------------
## Demo Project: 
### Persist data with Docker Volumes

#### Technologies used:
- Docker, Node.js, MongoDB

#### Project Description:
1. Persist data of a MongoDB container by attaching a Docker volume to it

- from inside the pp folder we run the following:
```bash
docker-compose -f docker-compose.yaml up
```
this starts up mongo & mongo-express, and we create a database called "user-accounts" and a collection called "users" (as below) [the DB & collection are variables within the nodejs app]
![07_image86.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image86.png)
![07_image87.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image87.png)

![07_image88.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image88.png)
- if we recreate the container we lose all this data, now we will create a compose file that uses named volumes
- we being by adding named volumes to our compose file, as below:
```yaml
version: '3'
services:
  mongodb:
    image: mongo
    ports:
      - 27017:27017
    environment:
      - MONGO_INITDB_ROOT_USERNAME=admin
      - MONGO_INITDB_ROOT_PASSWORD=password
    volumes:
      - mongo-data:/data/db
  mongo-express:
    image: mongo-express
    ports:
    - 8081:8081
    restart: always
    environment:
      - ME_CONFIG_MONGODB_ADMINUSERNAME=admin
      - ME_CONFIG_MONGODB_ADMINPASSWORD=pass
      - ME_CONFIG_MONGODB_SERVER=mongodb
volumes:
  mongo-data:
    driver: local
```
the rules set forth for the location of data is described on mongo DB docs page
![here](https://www.mongodb.com/docs/manual/tutorial/manage-mongodb-processes/)
**NOTE:** Always check docs for DBs data locations as they all differ (though SQL & Postgres loc's are the same /var/lib/data)
we can verify this by sh'ing into the container running the exec command and viewing the location
![07_image89.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image89.png)
the above will be destroyed when we recreate the data (obvs)
- during container replication, all the data saved locally will replicate to the container
- we now restart the compose command
- running "docker-compose -f mongo.yaml down" removes:
    - Containers for services defined in the Compose file.
    - networks
    - etc
- we restart the containers and as before all the data is replicated

So where is the data located locally?
- this will differ on OSs:
    - Windows: c:\programdata\docker\volumes
    - Linux: var/lib/docker/volumes
    - Mac: var/lib/docker/volumes
- each volumes has it's hash then /_data
we can run the below to view the data about the containers:
```bash
docker run -it --privileged --pid=host debian nsenter -t 1 -m -u -n -i sh
```
![07_image90.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image90.png)
- the volumes shown as "b3b5ef4ae37a3683f53dc6c4aeef05a760811de63ceaade75f09f74b439f53e4" etc are known as "anonymous volumes" as we explained earlier 
- if we access the contaienrs volume using the exec command and look inside the /data/db folder we will see the exact same data listed from the docker run -it --privileged ... command

---------------------------------------------------------------------------------------------------
## Demo Project: 
### Create Docker repository on Nexus and push to it

#### Technologies used:
- Docker, Nexus, DigitalOcean, Linux

#### Project Description:
1. Create Docker hosted repository on Nexus
2. Create Docker repository role on Nexus
3. Configure Nexus, DigitalOcean Droplet and Docker to be able to push to Docker repository
4. Build and Push Docker image to Docker repository on Nexus

- we create a new repo on Nexus, under a "docker (hosted)" and call it "docker-hosted" leave all the fields as default
![07_image91.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image91.png)
- the above image shows us the url we can use to push images to the repo
    - http://142.93.168.64:8081/repository/docker-hosted/
- now we create a docker role for this
- create role > "nexus role" > name it "nx-docker" > for privileges, we use the "nx-repository-view-docker-docker-hosted-*" > save
- next we assign it to our existing user "johnk"
- before we can get push to nexus we need to make some changes to the docker repo, we open the repo, and edit the "HTTP" (Create an HTTP connector at specified port. Normally used if the server is behind a secure proxy.). the reason for this is because docker cannot use the nexus URL endpoint for docker login, this is down to the port that nexus is running on. So we need a port for docker repo specifically
- on the nexus/docker-repo there's a tool tip for "repostory connectors"
"*Connectors allow Docker clients to connect directly to hosted registries, but are not always required. Consult our documentation for which connector is appropriate for your use case. For information on scaling the repositories see our scaling documentation.*"
- we set this to **8083** which is different from the nexus port
- if we run the netstat command on the droplet we can see it's been opened, we also need to open it on the firewall to allows us to access it / push to it
![07_image92.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image92.png)
- we also need to configure a "realm" within Nexus, this is a type of token used for authentication from nexus docker repo for a client, and that will be stored locally on my machine saved inside "config.json" inside the .docker folder
![07_image93.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image93.png)
- anytime we push or pull from the nexus repo, this token will be used
- we click the "docker bearer token realm" & save
- by default Docker only allows client requests to HTTPs endpoints of a docker registry, because we dont have this, we need to make some changes
- Using Windows Docker Desktop, go to settings > Docker Engine and add "insecure-registries" line, as below:
```json
  "insecure-registries": ["142.93.168.64:8083"]
```
- apple & restart the engine
![07_image94.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image94.png)
- head to the teminal and run docker login, as below:
```bash
docker login 142.93.168.64:8083
```
- this will ask for the nexus user & password
![07_image95.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image95.png)

- Time to push images to Nexus Repo
- We start off by building an image from the js-app folder, by running
```bash
docker build -t my-app:1.0 .
```
- we retag that image, to include the address of the Nexus Repo
    - Previously we didn't do this, as by default it pushes to our Docker Repo
- we run the below command with the new tag:
```bash
 docker tag my-app:1.0 142.93.168.64:8083/my-app:1.0
```
- the above, implicitly tells docker where to push
- when I run docker images, we can see the new image (w/ the Nexus repo)
![07_image96.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image96.png)
- now we run:
```bash
docker push 142.93.168.64:8083/my-app:1.0
```
![07_image97.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image97.png)
- And on the repo we can see the image:

![07_image98.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image98.png)

---------------------------------------------------------------------------------------------------
## Demo Project: 
### Deploy Nexus as Docker container

#### Technologies used:
- Docker, Nexus, DigitalOcean, Linux

#### Project Description:
1. Create and Configure Droplet
2. Set up and run Nexus as a Docker container

- 1st we create a new droplet (regular ssd ($48 pm), ubuntu, use the ssh key for this main pc, and addit to the same firewall rules as last time - change the name as it's got the same name as the previous one)
- we only need docker running on this server, everything else will be inside the container
- obvs run and update & upgrade by running the below:
```bash
apt update -y && apt upgrade -y
```
- the docker, running the below:
```bash
snap install docker
```
- head to docker hub and search for nexus3
![07_image99.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image99.png)
- scrolling down the documentation, we need to take a look at persistent data, run the below:
```bash
docker volume create --name nexus-data
```
then
```bash
docker run -d -p 8081:8081 --name nexus -v nexus-data:/nexus-data sonatype/nexus3
```
*NOTE: you should know the flags!!*

- running netstat we can see Nexus running on 8081 (needed to install net-tools)
![07_image100.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image100.png)
- we can see the same querying docker 
- we can now also open the servers ip in a browser and see Nexus ui is running
- In the nexus module, we created a nexus user (*best practices*)
- so we'll run exec and shell in
    - when we shell in we see the nexus user is already created for us
    - Opening Nexus3 tags on Docker hub, we can see the image layers, and if we scroll down to the bottom we can see the nexus user was created 
- running docker insepct [volume name] we can see more information, location, scope etc
```bash
docker inspect nexus-data
```
![07_image101.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image101.png)
- we can also see by changing to that dir we can see the temp admin password
![07_image102.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image102.png)
we can see the sanem data from the root of the server
![07_image103.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/07_image103.png)

---------------------------------------------------------------------------------------------------
