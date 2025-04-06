# Demo Project:
Dynamically Increment Application version in Jenkins Pipeline

## Technologies used:
Jenkins, Docker, GitLab, Git, Java, Maven

## Project Description:
1. Configure CI step:Increment patch version
2. Configure CI step: Build Java application and clean old artifacts
3. Configure CI step: Build Image with dynamic Docker Image Tag
4. Configure CI step: Push Image to private DockerHub repository
5. Configure CI step: Commit version update of Jenkins back to Git repository
6. Configure Jenkins pipeline to not trigger automatically on CI build commit to avoid commit loop


## Configure CI step:Increment patch version
1. To increment the version in the Jenkins file just add a new step before the app is build:
```
        stage("increment version") {
            steps {
                script {
                    echo "Incrementing version"
                    sh 'mvn build-helper:parse-version versions:set -DnewVersion=\\\${parsedVersion.majorVersion}.\\\${parsedVersion.minorVersion}.\\\${parsedVersion.nextIncrementalVersion} versions:commit'
                }
            }
        }
```

## Configure CI step: Build Java application and clean old artifacts
1. In the Jenkins `build jar` step we need to make sure we remove the old artifacts
2. Look for the buildJar function and instead of `mvn package` use the command `mvn clean package`

## Configure CI step: Build Image with dynamic Docker Image Tag
1. To add a new version dynamically we need to increment the docker image version
2. We can have an env variable set for the image name so the build image should look like:
```
        stage("build image") {
            steps {
                script {
                    echo "Building the docker image..."
                    withCredentials([usernamePassword(credentialsId: "docker-hub-repo", passwordVariable: "PASS", usernameVariable: "USER")]) {
                        sh "docker build -t gonzflarez/demo-app:$IMAGE_NAME ."
                        sh 'echo $PASS | docker login -u $USER --password-stdin'
                        sh "docker push gonzflarez/demo-app:$IMAGE_NAME"
                    }
                }
            }
        }
```
3. But also we need to change the increment version step to set the image env variable:
```
        stage("increment version") {
            steps {
                script {
                    echo "Incrementing version"
                    sh 'mvn build-helper:parse-version versions:set -DnewVersion=\\\${parsedVersion.majorVersion}.\\\${parsedVersion.minorVersion}.\\\${parsedVersion.nextIncrementalVersion} versions:commit'
                    def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
                    def version = matcher[0][1]
                    env.IMAGE_NAME = "$version-$BUILD_NUMBER"
                }
            }
        }
```

## Configure CI step: Push Image to private DockerHub repository
Achieved already with the previous code: `sh "docker push gonzflarez/demo-app:$IMAGE_NAME"`

## Configure CI step: Commit version update of Jenkins back to Git repository
1. It is achieved if we add a new step to the Jenkinsfile:
```
        stage("commit version update") {
            steps {
                script {
                        withCredentials([usernamePassword(credentialsId: "GitHub Credentials", passwordVariable: "PASS", usernameVariable: "USER")]) {
                        def encodedUser = USER.replaceAll('@', '%40')
                        sh 'git config --global user.email "jenkins@example.com"'
                        sh 'git config --global user.name "Jenkins"'

                        sh "git remote set-url origin https://${encodedUser}:${PASS}@github.com/test/java-maven-app-jenkins.git"
                        sh 'git add .'
                        sh 'git commit -m "ci: version bump"'
                        sh 'git push origin HEAD:master'
                    }
                }
            }
        }   
```
## Configure Jenkins pipeline to not trigger automatically on CI build commit to avoid commit loop
1. We need to install the plugin Ignore Commiter Strategy
2. Go to the jenkins pipeline configuration
3. In branch shources, under bluild strategies add a new committer strategy
4. Enter the List of author emails to ignore, in our case `jenkins@example.com`
5. Save


#### Notes
  4   .  2    .  1
Major . Minor . Patch

#### Command to increment patch version:
```
mvn build-helper:parse-version versions:set -DnewVersion=\${parsedVersion.majorVersion}.\${parsedVersion.minorVersion}.\${parsedVersion.nextIncrementalVersion} versions:commit
```