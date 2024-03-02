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
- Create Dockerfile for Nodejs application and build Docker image
- Run Nodejs application in Docker container and connect to MongoDB database container locally.
- Also run MongoExpress container as a UI of the MongoDB database.



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
- Write Docker Compose file to run MongoDB and MongoExpress containers



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
- Write Dockerfile to build a Docker image for a Nodejs application
- Create private Docker registry on AWS (Amazon ECR)
- Push Docker image to this private repository


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
[07_image75.png]
From above, you can see the build steps [1/3]-[3/3]
- when we run rocker images we can see our newly created image

[07_image76.png]
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
[07_image77.png]()

- you can get into the container by running the exec command as below:
```bash
docker exec -it <container ID> /bin/sh
```

- we are going to recreate the Dockerfile, making it more efficient (basically removing the files we dont need to copy over)
[07_image78.png]
07_image79.png
07_image80.png

---------------------------------------------------------------------------------------------------
## Demo Project: 
### Deploy Docker application on a server with Docker Compose

#### Technologies used:
- Docker, Amazon ECR, Node.js, MongoDB, MongoExpress

#### Project Description:
- Copy Docker-compose file to remote server
- Login to private Docker registry on remote server to fetch our app image
- Start our application container with MongoDB and MongoExpress services using docker compose


- Create a private repo for Docker
  - login to aws console
- open ecr
  - click get started
  - we call name it after the app (my-app), leave everything as default
  - 631368147597.dkr.ecr.eu-west-2.amazonaws.com/my-app
  - NOTE: ECR is a single repo per image
[07_image81.png]
- connect & login to ECR from local terminal
- click "view push commands" gives you line by line what needs to be executed
[07_image82.png]
[07_image83.png]

- Now we will make some changes to the App, rebuild & push a new version to AWS repo
- 
video: Docker > part 12 (6:11)

---------------------------------------------------------------------------------------------------
## Demo Project: 
### Persist data with Docker Volumes

#### Technologies used:
- Docker, Node.js, MongoDB

#### Project Description:
- Persist data of a MongoDB container by attaching a Docker volume to it


---------------------------------------------------------------------------------------------------
## Demo Project: 
### Create Docker repository on Nexus and push to it

#### Technologies used:
- Docker, Nexus, DigitalOcean, Linux

#### Project Description:
- Create Docker hosted repository on Nexus
- Create Docker repository role on Nexus
- Configure Nexus, DigitalOcean Droplet and Docker to be able to push to Docker repository
- Build and Push Docker image to Docker repository on Nexus


---------------------------------------------------------------------------------------------------
## Demo Project: 
### Deploy Nexus as Docker container

#### Technologies used:
- Docker, Nexus, DigitalOcean, Linux

#### Project Description:
- Create and Configure Droplet
- Set up and run Nexus as a Docker container


---------------------------------------------------------------------------------------------------
