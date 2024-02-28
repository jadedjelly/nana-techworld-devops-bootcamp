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


---------------------------------------------------------------------------------------------------
## Demo Project: 
### Dockerize Nodejs application and push to private Docker registry

#### Technologies used:
- Docker, Node.js, Amazon ECR

#### Project Description:
- Write Dockerfile to build a Docker image for a Nodejs application
- Create private Docker registry on AWS (Amazon ECR)
- Push Docker image to this private repository


---------------------------------------------------------------------------------------------------
## Demo Project: 
### Deploy Docker application on a server with Docker Compose

#### Technologies used:
- Docker, Amazon ECR, Node.js, MongoDB, MongoExpress

#### Project Description:
- Copy Docker-compose file to remote server
- Login to private Docker registry on remote server to fetch our app image
- Start our application container with MongoDB and MongoExpress services using docker compose



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
