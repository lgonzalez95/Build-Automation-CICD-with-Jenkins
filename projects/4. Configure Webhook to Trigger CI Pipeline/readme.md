# Demo Project:
Configure Webhook to trigger CI Pipeline automatically on every change

## Note:
Using GitHub since I am using this platform

### Technologies used:
Jenkins, GitHub, Git, Docker, Java, Maven

### Project Description:
1. Install GitHub Plugin in Jenkins
2. Configure GitHub access token and connection to Jenkins in GitHub project settings
4. Configure Jenkins to trigger the CI pipeline, whenever a change is pushed to GitHub


## Install GitHub Plugin in Jenkins
1. Install the GitHub integration plugin
2. Install the Multibranch Scan Webhook Trigger Plugin

## Configure GitHub access token and connection to Jenkins in GitHub project settings


## Configure Jenkins to trigger the CI pipeline, whenever a change is pushed to GitHub
1. Go to project settings
2. Click on WebHooks
3. Ckic on Add webhook
4. Enter the Url:
- `http://JENKINS_URL:8080/github-webhook/` for non multibranch projects
- `http://JENKINS_URL:8080/multibranch-webhook-trigger/invoke?token=GitHubRandomToken` for multibranch projects
5. Select the events we wnat to push
6. Click on Save

### For Jenkins Projects that are not multibranch pipelines
1. Go to manage Jenkins -> System
2. Add a GitHub Server connection (Use GitHub Secret Token as auth method)
3. Go to the Jenkins job configuration settings
4. Turn on the setting `GitHub hook trigger for GITScm polling`
5. Save

### For Jenkins Projects that are multibranch pipelines
1. Go to the Jenkins job configuration settings
2. Turn on the setting `Scan by webhook`
3. Provide any random string for the trigger token, for example `GitHubRandomToken`
4. Save

# NOTE VERY IMPORTANT
The Digital Ocean Firewall might might make the GitHub events not to reach Jenkins server, if the is the case then we need to either disable the IP restriction or forward the webhooks using some service like Smee.