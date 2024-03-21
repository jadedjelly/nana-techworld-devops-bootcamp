# Module 7 - Build Automation & CI/CD with Jenkins
1. [Install Jenkins on DigitalOcean](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/demo_projects/M7_Docker/M8_Jenkins_README.md#Install-Jenkins-on-DigitalOcean)
2. [Create a CI Pipeline with Jenkinsfile (Freestyle, Pipeline, Multibranch
Pipeline)](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/demo_projects/M7_Docker/M8_Jenkins_README.md#Create-a-CI-Pipeline-with-Jenkinsfile-(Freestyle,-Pipeline,-Multibranch-Pipeline))
3. [Create a Jenkins Shared Library](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/demo_projects/M7_Docker/M8_Jenkins_README.md#Create-a-Jenkins-Shared-Library)
4. [Configure Webhook to trigger CI Pipeline automatically on
every change](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/demo_projects/M7_Docker/M8_Jenkins_README.md#Configure-Webhook-to-trigger-CI-Pipeline-automatically-on-every-change)
5. [Dynamically Increment Application version in Jenkins Pipeline](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/demo_projects/M7_Docker/M8_Jenkins_README.md#Dynamically-Increment-Application-version-in-Jenkins-Pipeline)
---------------------------------------------------------------------------------------------------
## Demo Project: 
### Install Jenkins on DigitalOcean

#### Technologies used:
- Jenkins, Docker, DigitalOcean, Linux

#### Project Description:
1. Create an Ubuntu server on DigitalOcean
2. Set up and run Jenkins as Docker container
3. Initialize Jenkins

2x options, either directly on the server (requiring java, a new user, etc) or (hopefully in this instance) on a container, where everything is done

- we create a new droplet (region (FK seems cheaper than london), Ubuntu, reg SSD @ 24/mo, use the normal ssh key, host renamed to jenkinsServer)
- create a new firewall, with the below ports:
    - 22 - set to my IP
    - 8080 - for jenkins

- ssh in, and run: (note: look up how to use default options for menu prompts....)
```bash
apt update -y && apt upgrade -y
```
then install docker with:
```bash
apt  install docker.io -y
```
we need to open the port for jenkins, so we'll set that up along with the usual
```bash
# [host:container]
# 8080:8080 how Jenkins communicates, but you also need 50000 open as well (I didn't see this last time I ran a 
# J.server, but its needed for the master and slave / worker) we also need to mount a vol (obvs), we dont want 
# to have to recreate everything if the container goes down

docker run -p 8080:8080 -p 50000:50000 -d -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts
```
[docker hub jenkins](https://hub.docker.com/r/jenkins/jenkins)

![08_image40.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image40.png)

Obvs when you start Jenkins for the 1st time, you get the setup wizard as below, and you'll need to get the temp admin creds for it here - /var/jenkins_home/secrets/initialAdminPassword

using the below we shell into the jenkins server to get to the temp password location
![08_image41.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image41.png)
we could have also looked for the location for the volume using docker inspect volume [vol name]

- pw set to usual test pw
- click install suggested plugins

![08_image42.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image42.png)

- now we create the 1st admin user
- start & finish, next on the url stuff and press "Start using Jenkins"

and Done!

---------------------------------------------------------------------------------------------------
## Demo Project: 
### Create a CI Pipeline with Jenkinsfile (Freestyle, Pipeline, Multibranch Pipeline)

#### Technologies used:
- Jenkins, Docker, Linux, Git, Java, Maven

#### Project Description:
CI Pipeline for a Java Maven application to build and push to the repository
1. Install Build Tools (Maven, Node) in Jenkins
2. Make Docker available on Jenkins server
3. Create Jenkins credentials for a git repository
4. Create different Jenkins job types (Freestyle, Pipeline, Multibranch
pipeline) for the Java Maven project with Jenkinsfile to:
    - Connect to the application’s git repository
    - Build Jar
    - Build Docker Image
    - Push to private DockerHub repository



---------------------------------------------------------------------------------------------------
## Demo Project: 
### Create a Jenkins Shared Library

#### Technologies used:
- Jenkins, Groovy, Docker, Git, Java, Maven

#### Project Description:
Create a Jenkins Shared Library to extract common build logic:
1. Create separate Git repository for Jenkins Shared Library project
2. Create functions in the JSL to use in the Jenkins pipeline
3. Integrate and use the JSL in Jenkins Pipeline (globally and for a specific project in Jenkinsfile)



---------------------------------------------------------------------------------------------------
## Demo Project: 
### Configure Webhook to trigger CI Pipeline automatically on every change

#### Technologies used:
- Jenkins, GitLab, Git, Docker, Java, Maven

#### Project Description:
1. Install GitLab Plugin in Jenkins
2. Configure GitLab access token and connection to Jenkins in GitLab project settings
3. Configure Jenkins to trigger the CI pipeline, whenever a change is pushed to GitLab



---------------------------------------------------------------------------------------------------
## Demo Project: 
### Dynamically Increment Application version in Jenkins Pipeline

#### Technologies used:
- Jenkins, Docker, GitLab, Git, Java, Maven

#### Project Description:
1. Configure CI step: Increment patch version
2. Configure CI step: Build Java application and clean old artifacts
3. Configure CI step: Build Image with dynamic Docker Image Tag
4. Configure CI step: Push Image to private DockerHub repository
5. Configure CI step: Commit version update of Jenkins back to Git repository
6. Configure Jenkins pipeline to not trigger automatically on CI build commit to avoid commit loop



---------------------------------------------------------------------------------------------------
