# Demo Project:
Create a CI Pipeline with Jenkinsfile (Freestyle, Pipeline, Multibranch
Pipeline)

### Technologies used:
Jenkins, Docker, Linux, Git, Java, Maven

### Project Description:
CI Pipeline for a Java Maven application to build and push to the
repository
1. Install Build Tools (Maven, Node) in Jenkins
2. Make Docker available on Jenkins server
3. Create Jenkins credentials for a git repository
4. Create different Jenkins job types (Freestyle, Pipeline, Multibranch
pipeline) for the Java Maven project with Jenkinsfile to:
    - Connect to the application’s git repository
    - Build Jar
    - Build Docker Image
    - Push to private DockerHub repository


## Configuring Jenkins
### Install the required build tools
There are two options: Install Jenkins plugins or install the build tools on the server:

#### Doing it in Jenkins:

1. Go to manage Jenkins > Tools
2. Click add Maven
3. Select a version, give a name and save

#### Doing it on the server:

1. ssh the server
2. Enter the docker container as root: `docker exec -u 0 -it 701a0218209f bash`
3. Update the repository: `apt update`
4. Install curl: `apt install curl`
5. Download the node installation script: `curl -sL https://deb.nodesource.com/setup_20.x -o nodesource_setup.sh`
6. Execute the script: `bash nodesource_setup.sh`
7. Install node js: `apt install nodejs`


### Install the other plugins

1. Install the stage view plugin
2. Start the Jenkins docker container: `docker start containerID`

## Jenkins Freestyle Job 1
1. Create a freestyle project
2. Add a build step -> Execute shell
3. Add a build step -> Invoke top-level Maven targets -> goals: --version
4. Save
5. Run the job
6. Go to project config
7. In source code management, select Git option.
8. Enter the git repo url and save
9. Build the job again.

## Jenkins Java Freestyle Job 
1. Create a freestyle project
2. In source code management, select Git option.
3. Enter the git repo url
4. Add a maven build step with goal `test`
5. Add a maven build step with goal `package`
6. Save
7. Run the job
8. Check the logs


## Docker
1. Run Jenkins with Docker volume: `docker run -p 8080:8080 -p 50000:50000 -d -v jenkins_home:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock jenkins/jenkins:lts`
2. Run inside the Jenkins container to download docker: `curl https://get.docker.com/ > dockerinstall && chmod 777 dockerinstall && ./dockerinstall`
3. Set permissions to the docker.dock file: `chmod 666 /var/run/docker.sock`

## Java Pipeline
```
docker build -t gonzflarez/demo-app:1.1 -f java-maven-app-starting-code/Dockerfile java-maven-app-starting-code/
echo $DOCKER_PASSOWRD | docker login -u $DOCKER_USERNAME --password-stdin
docker push gonzflarez/demo-app:1.1 
```

## Changes to push to docker private repo
1. Login to Jenkins server
2. Execute: `vim /etc/docker/daemon.json`
3. Allow insecure repo copy and paste: 
```
{
    "insecure-registries": [
    "http://x.x.x.x:8083"
  ]
}
```
4. Restart docker: `systemctl restart docker`
5. Start the container again: `docker start containerID`
6. Reconfigure permissions of docker.sock
7. Enter the container: `docker exec -u 0 -it containerID bash`
8. Run `chmod 666 /var/run/docker.sock`
9. Add nexus credentials to Jenkins
10. Update the pipeline to:
```
docker build -t x.x.x.x:8083/java-jenkins-app:1.1 -f java-maven-app-starting-code/Dockerfile java-maven-app-starting-code/
echo $DOCKER_PASSOWRD | docker login -u $DOCKER_USERNAME --password-stdin x.x.x.x:8083
docker push x.x.x.x:8083/java-jenkins-app:1.1
```

Notes:
1. See env variables: `http://x.x.x.x:8080/env-vars.html/`
2. To get credentials in Jenkins file it is required to install the "Credentials Binding" plugin