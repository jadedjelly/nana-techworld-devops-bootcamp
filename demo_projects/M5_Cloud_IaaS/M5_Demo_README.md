# Module 5 - Cloud & infrastructure as Service Basics

## Demo Project: 
### Create Server & deploy application on Digital Ocean

#### Technologies used:
- DigitalOcean, Linux, Java, Gradle

#### Project Description:
- Setup and configure a server on DigitalOcean
- Create and configure a new Linux user on the Droplet
- (Security best practice)
- Deploy and run a Java Gradle application on Droplet
---------------------------------------------------------------------------------------------------

- run update and upgrade on linux server

``` bash
apt update && apt upgrade -y
```

- install the correct Version of java
``` bash
apt install openjdk-8-jre-headless -y
```
- clone "java-react-example"
```bash
git clone https://gitlab.com/twn-devops-bootcamp/latest/05-cloud/java-react-example
```
- cd to the project folder, and run the gradle build command
```bash
gradle build
```
- use scp to move the java app from your local to the droplet (scp loc ssh addr:destin)
```bash
scp java-react-example.jar root@46.101.212.166:/root
```
- ssh to the server & run the java app (open the port 7071 on the droplet, "&" runs the app in detached mode)
```bash
java -jar java-react-example.jar &
```
- create a user
```bash
useradd john
```
- add to the sudoers grp
```bash
usermod -aG sudo john
```
- copy pub key from local & cp to new linux user "john", create .ssh folder & an "authorized_keys" file and cp pub key here
```bash
mkdir .ssh
sudo nano authorized_keys
```
- ssh from new user to server
```bash
ssh john@46.101.212.166
```

- below the java app running

![05_image19.png](../notes/assets/05_image19.png)

