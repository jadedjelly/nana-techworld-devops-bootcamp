# Module 6 - Artifact Repository Manager with Nexus

## Demo Project: 
### Run Nexus on Droplet and Publish Artifact to Nexus

#### Technologies used:
- Nexus, DigitalOcean, Linux, Java, Gradle, Maven

#### Project Description:
- Install and configure Nexus from scratch on a cloud server
- Create new User on Nexus with relevant permissions
- Java Gradle Project: Build Jar & Upload to Nexus
- Java Maven Project: Build Jar & Upload to Nexus
---------------------------------------------------------------------------------------------------

- run update, upgrade, install java 8 HL & net-tools on linux server

``` bash
apt update && apt install openjdk-8-jre-headless -y && apt install net-tools -y
```
- cd to /opt/
- download & extract nexus

```bash
wget https://download.sonatype.com/nexus/3/nexus-3.65.0-02-unix.tar.gz && tar -zxvf nexus-3.64.0-04-unix.tar.gz
```

- create a newuser called nexus
```bash
adduser nexus
```

- change ownership of nexus files to user "nexus"
```bash
chown -R nexus:nexus nexus-3.65.0-02 && chown -R nexus:nexus sonatype-work
```
- set Nexus to run as the user "nexus"
```bash
nano nexus-3.64.0-04/bin/nexus.rc
# remove comment, add nexus
# should look like: run_as_user="nexus"
```
- log in as nexus user

- start nexus & remember to open 8081 on the droplet
```bash
/opt/nexus-3.64.0-04/bin/nexus start
```

- view the temp admin apssword, below:
```bash
cat /opt/sonatype-work/nexus3/admin.password
```

- login to the linux server using the ipaddress:port#

- clone the 2x java examples:
```bash
git clone https://gitlab.com/twn-devops-bootcamp/latest/06-nexus/java-app
git clone https://gitlab.com/twn-devops-bootcamp/latest/06-nexus/java-maven-app
```

- create a new user on Nexus called "johnk" & pw: fh0fjjvrjigjf
- create a role specific for this new user called "nx-java", w/ nx-repository-view-maven2-maven-snapshots-*
- from Intellij open the "java-app" folder
- update the url for the nexus repo in the section called "repositories"
    - http://164.92.169.54:8081/repository/maven-snapshots/
-update the gradle.properties file with the nexus credentials

- open terminal from Intellijs terminal, run the below build command
```bash
gradle build
```

- build folder will be created, inside this (buried inside of /libs) will be the "my-app-1.0-SNAPSHOT.jar" file 
- From terminal, cd to the java-app folder, then run the below to publish
```bash
gradle publish
```
change url for below image!!
![06_image1]()

- open java-maven-app folder in intellij
- open pom.xml file
  - Update the url for the nexus-snapshot
- From terminal cd to the .m2 file & create a settings.xml file with the below info:
```xml
<settings>
  <servers>
     <server>
      <id>nexus-snapshots</id>
      <username>johnk</username>
      <password>fh0fjjvrjigjf</password>
     </server>
  </servers>
</settings>
```
- cd back to java-maven-app folder, and we run the below the build the java-maven app
```bash
mvn package
```
change url for below image!!
![06_image2.png]()

- we deploy it to the nexus repo, running the below:
```bash
mvn deploy
```
change url for below image!!
![06_image3.png]()


