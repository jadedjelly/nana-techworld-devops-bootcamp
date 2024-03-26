# Module 8 - Build Automation & CI/CD with Jenkins
1. [Install Jenkins on DigitalOcean](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/demo_projects/M8_jenkins/M8_Jenkins_README.md#Install-Jenkins-on-DigitalOcean)
2. [Create a CI Pipeline with Jenkinsfile (Freestyle, Pipeline, Multibranch
Pipeline)](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/demo_projects/M8_jenkins/M8_Jenkins_README.md#Create-a-CI-Pipeline-with-Jenkinsfile-(Freestyle,-Pipeline,-Multibranch-Pipeline))
3. [Create a Jenkins Shared Library](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/demo_projects/M8_jenkins/M8_Jenkins_README.md#Create-a-Jenkins-Shared-Library)
4. [Configure Webhook to trigger CI Pipeline automatically on
every change](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/demo_projects/M8_jenkins/M8_Jenkins_README.md#Configure-Webhook-to-trigger-CI-Pipeline-automatically-on-every-change)
5. [Dynamically Increment Application version in Jenkins Pipeline](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/demo_projects/M8_jenkins/M8_Jenkins_README.md#Dynamically-Increment-Application-version-in-Jenkins-Pipeline)
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

## Configure a Plugin for Maven
From the UI
- From Jenkins Dashboard, head to:
    - Manage Jenkins > Tools
        - In here multiple configurations are available - Gradle, Ant, Git, Maven (here but not configured, so can't be used in jobs)
    - Click Add Maven
    - Give it a name (like maven-3.9)
    - Select the version you want
    - 
![08_image43.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image43.png)

    - Click save & this will dowload and make available
*Note: You also have the option to add an installer via extract of a tar, run sh / bat command (server os pending) etc*

## Install npm and Node in Jenkins Container
From the command line (server / container)
- ssh to server
- Once here if you run the docker exec command as below, it will log you in as the Jenkins user (1), we need to log in as root (2):
```bash
# (1)
docker exec -it 8e1c bash
# (2) -u = username, 0 denotes root (uid)
docker exec -u 0 8e1c bash
```
![08_image44.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image44.png)

a handy command to remember is the below, this cats the version you are running in a container
```bash
cat etc/issue
```
In this case its: "*Debian GNU/Linux 12 \n \l*"

as is best practice before installing s/w, we run the below:
```bash
apt update && apt install curl -Y
```
we run nthe below, which downloads a node installer script, runs it, then installs it:
```bash
curl -sL https://deb.nodesource.com/setup_20.x -o nodesource_setup.sh && bash nodesource_setup.sh && apt install nodejs -Y
```
we can then run the node & npm with the -v flag

![08_image55.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image55.png)

## Jenkins Basics Demo - Freestyle job

- From the Dashboard:
    - Click either New item (left) or Create job (centre screen)
    - Give it a name "my-job"
    - and select "Freestyle project"
    - click ok
- In the next screen, this is where you can add your SCM, Triggers, Environment & build steps
    - Keep everything as default, and scroll to "Build Steps"
    - click the drop down menu here, and the below is displayed

![08_image56.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image56.png)

    - select "execute shell"
        - these are execute inside the Jenkins container
    - pop in "npm --version"
    - we can't run mvn as it's not installed directly in the container (recall it's a plugin)
    - click the drop down again, and select "Invoke top-level maven targets"
    - in the next drop down, select the maven we have configured (maven-3.9) and set the "goal" as --version
as below:

![08_image57.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image57.png)

    - click save
    - heading back to the dashboard, we can see the list of jobs
click it we are brought into the job itself:

![08_image45.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image45.png)

click "Build now" to run the job
    - click "Console Output" and we see the output
    - 
![08_image46.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image46.png)

**Remember**: /var/jenkins_home is the volume we created and mounted for this container

## Install a plugin via the plugins option in "manage jenkins"

From Dashboard, head to Manage Jenkins > then Plugins
- Click available plugins
- search NodeJs
- tick the box and then click install (top right)

![08_image47.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image47.png)

then going back to Tools, we can see a configuration for NodeJS, as below:

![08_image58.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image58.png)

HEading back to my-job > Configure > scroll down to "Build steps", we can see from teh drop down menu we now have an option for "Execute NodeJS script" as below:

![08_image48.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image48.png)

## Configure Git Repository
![Starter code](https://github.com/jadedjelly/Jenkins_Module8.git)

- From inside the job (via configure), head to Source code management
    - Input the git url
    - Credentials:
    - If you dont have credentials saved, you can click add and Jenkins will go through the setup, via the below:

![08_image59.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image59.png)

- From kind you have multiple options you can use:
    
![08_image60.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image60.png)

- We keep everything as default, and add the following:
        - Username, Password, Description (we can ref later)
    - next we can specify what branch we want to build, my master branch is called main so I change it
    - click save & Build now, the output is below:
![08_image50.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image50.png)
- In the outpt we can see:
        - Jenkins cloned my repo (saved to var/jenkins_home/workspaces/my-job - will get into this later)
        - ran the npm --version
        - ran the mvn --version
- if we access the container (exec command), and ls to /var/jenkins_home we can see files that contain info on plugins, credentails, jobs, logs, etc

![08_image61.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image61.png)

- Inside the jobs folder, will list all jobs created on the server, including their builds (every time you build the job it creates a new build) and an xml file that shows all the options used
- If we look inside the workspaces folder and into the my-job folder we can see the cloned repo, in the my-job@tmp is a temporary folder 

## Complete task from git repo in Jenkins job

- head back to job configuration, change the branch specifier to */jenkins-jobs and go to Build steps and remove the npm --version command and add chmod +x on the script and add the script name to be executed
- The reasoning behind the chmod is to give jenkins permissions to execute it, as below:

![08_image62.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image62.png)

- click save and then "build now", output below:
![08_image63.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image63.png)

## Run test & build Java Application

![08_image52.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image52.png)
- we create a new freestyle job, call it "java-maven-build"
- add the repo:
    - https://github.com/jadedjelly/Jenkins_Module8.git
- change the credentials to the ones we created earlier
- change the branch to "jenkins-jobs"
- Add a build step "Invoke top-level maven targets"
- choose the version we created earlier (maven-3.9)
- add "test" in to goals
- do the same again put add "package" as the goal
- click save
- Looking through the output (very long), we see that the app has been tested & packaged, and now when we go to the server and do a ls of /var/jenkins_home/workspace/java-maven-build/target we can see the jar files as below:

![08_image64.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image64.png)

## Make Docker available on Jenkins server
We need to mount the Docker runtime directory to the container, 1st we need to stop the container
- ssh ont othe jenkins server, and run the below stop command:
```bash
docker container stop
```
then run the below to add the docker.sock folder (runtime)
```bash
docker run -p 8080:8080 -p 50000:50000 -d -v jenkins_home:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock jenkins/jenkins:lts
```
then we need to grab the latest version, change the permissions on it then run it

```bash
curl https://get.docker.com/ > dockerinstall && chmod 777 dockerinstall && ./dockerinstall
```

- then we need to give the jenkins user permissions to use docker commands, so we remote into the container and run:
```bash
chmod 666 /var/run/docker.sock
```
![08_image66.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image66.png)
we can now run docker commands from inside the "execute shell" area of a job configuration

## Build Docker image 

![08_image65.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image65.png)
```Dockerfile
FROM amazoncorretto:8-alpine3.17-jre

EXPOSE 8080

COPY ./target/java-maven-app-*.jar /usr/app/
WORKDIR /usr/app

CMD java -jar java-maven-app-*.jar
```
The above has been added as a Dockerfile to the jenkins-jobs branch of the java-maven app

- From the dashboard, click java-maven-build > then configuration
- scroll down to "build steps", remove the maven command to test, as the package command does this
- Add a new build step for "Execute shell"
- add the following to the shell:
    -  docker build .
    -  we also add the name tag -t along with java-maven-app
```bash
docker build -t java-maven-app:1.0 .
```
- click save & Build now
Now when we look at docker images on the server we can see our java-maven app image as listed:

![08_image67.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image67.png)

## Push image to Docker hub
As a next step we will push that image to a Docker Repo

![08_image68.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image68.png)

- Create a private repo on Dockerhub account, give it a name and set it to private

![08_image69.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image69.png)

- Then from Jenkins dashboard, go to Manage Jenkins > from the Security section, select Credentials > select "Global Credentials (unrestricted)" > You'll see the github -creds created earlier listed
- Press "add Credentials"
    - add username, password & give an id of "Docker-hub-repo"
- Press Create
- Go back to the "java-maven-build" job, press "configure"
- Scroll to "execute shell", the way we push to a private repo, is to add it to the tag element as below: 
```bash
docker build -t jadedjelly/mod8-jenkins:1.0 .
docker push jadedjelly/mod8-jenkins:1.0
```
- Obviously we need to login so Jenkins can use those credentials we just created so between the build and push we add docker login. If we're doing this from the temrinal we are prompted, we can't do that here so we need to enable another plugin (noted under "build Environment", "Use secret text(s) or files(s)", from the drop down box (as "Bindings"), as below:

![08_image70.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image70.png)
- and select > "Username & Password (seperated)"
- Give the username & password variable names
- From the drop down box, select the docker hub one
NOTE: *When creating credentials, add a description to the creds as it appears in the drop down box as only [username]/[password] with no designation between the 2x when not done!!!*

![08_image71.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image71.png)

```bash
docker build -t jadedjelly/mod8-jenkins:1.0 .
docker login -u $USERNAME -p $PASSWORD
docker push jadedjelly/mod8-jenkins:1.0
```
![08_image72.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image72.png)

- pushing to an external repo like ecr / nexus etc you would amend the tag with the externals url as below:
```bash
docker push aws_account_id.dkr.ecr.us-west-2.amazonaws.com/my-repository:tag
```
- click save and Build now

![08_image73.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image73.png)
NOTE: *Authentication failed for me using Nana's method, changed password to be the access token*

- This time it worked without issues (as below)
![08_image74.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image74.png)

Note: *Within the output we get a warning that the password via cli is insecure and should use --password-sdtin, the fix is below*

- CHnaging the line for authentication we pipe the password variable into the docker login

```bash
docker build -t jadedjelly/mod8-jenkins:jma-1.0 .
echo $PASSWORD | docker login -u $USERNAME --password-stdin
docker push jadedjelly/mod8-jenkins:jma-1.0
```

- On test we get a success message

## Push docker image to Nexus repo
NOTE: *had to rebuild nexus server, this time as a docker image... *
http://157.230.30.94:8081/repository/docker-hosted/
- From the root of the jenkins server, we create a new file called daemon.json in /etc/docker/, with the below:
```json
}
  "insecure-registries": ["157.230.30.94:8083"]
}
```
- Recall [ip of Docker repo]:[port number be assigned for http]
NOTE: *daemon.json = config file used by docker to specify settings to customize  the behaviour*
NOTE2: *we add this to the host, we only mounted docker to jenkins!*
- Now we restart docker so changes can be made
    - systemctl restart docker
    - also need to restart the container
- We head back to Jenkins, and configure the "java-maven-build" to push to our Nexus repo
- 1st we need to setup credentials so Jenkins can push to Nexus
- From credentials we add one for Nexus

![08_image75.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image75.png)
- Head to the configuration for the java-maven-build, replace the dockerhub credentials with that of the newly created Nexus creds
- From execute shell we need to ammend it for pushing to NExus
```bash
docker build -t 157.230.30.94:8083/java-maven-app:1.1 .
echo $PASSWORD | docker login -u $USERNAME --password-stdin 157.230.30.94:8083
docker push 157.230.30.94:8083/java-maven-app:1.1
```
- save & Build now
![08_image76.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image76.png)
![08_image77.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image77.png)
![08_image78.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image78.png)

## Create pipeline job

- New item > give it a name "my-pipeline" > select pipeline > click ok
- Unlike freestyle jobs, pipelines uses scripts, from the dropdown menu, there's 2x options
    - pipeline script
    - pipeline script from scm (source code management)
    - You can view the samples from the right drop down menu:
```groovy
pipeline {
    agent any

    stages {
        stage('Hello') {
            steps {
                echo 'Hello World'
            }
        }
    }
}
```

NOTE:
Below the script box, there's a check box called "groovy sandbox", it's tooltip describes it as:
"*If checked, run this Groovy script in a sandbox with limited abilities. If unchecked, and you are not a Jenkins administrator, you will need to wait for an administrator to approve the script. *"

- Best practice for IaC says that you should nt use inline pipeline scripts (scripts written directly into a pipeline) and should have it saved in your scm (understandable)

- we select "pipeline script from SCM"
    - input the git repo address: https://github.com/jadedjelly/Jenkins_Module8.git
    - use the correct credentials
- The Script path, is set to the newly created Jenkinsfile in the repo
    - This contains the groovy script that configires the pipeline
- click save
- the Job fails because the Jenkisnfile doesn't exist

- we create a simple Jenkinsfile on the repo, with the below:
```groovy
pipeline {

    agent any

    stages {

        stage("build") {

            steps {
              echo "building the application..."

            }
        }
        stage("test") {

            steps {
              echo "testing the application..."
            }
        }
        stage("deploy") {

            steps {
              echo "deploying the application..."
            }
        }
    }
}
```
- The build is a success and we can see the output below:

![08_image81.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image81.png)

- There's also a new output, that shows each stage that we defined (build, test, deploy) & this will show if it failed or not

![08_image82.png](https://github.com/jadedjelly/nana-techworld-devops-bootcamp/blob/main/notes/assets/08_image82.png)























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
