# Demo Project:
Install Jenkins on DigitalOcean

### Technologies used:
Jenkins, Docker, DigitalOcean, Linux


### Project Description:
1. Create an Ubuntu server on DigitalOcean
2. Set up and run Jenkins as Docker container
3. Initialize Jenkins


## Jenkins Installation Steps
1. Create droplet in DigitalOcean
2. Create a firewall and open port 22 and 8080
3. ssh the server: `ssh root@x.x.x.x`
4. Update the registry: `apt update`
5. Install docker: `apt  install docker.io`
6. Check that docker is availe by running: `docker`
7. Run Jenkins container: `docker run -p 8080:8080 -p 50000:50000 -d -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts`
8. Open the jenkins ip: `http://x.x.x.x:8080`
9. Login to the containter and get Jenkins password.
10. Paste the password in the browser and continue.
11. Select the sugested plugins and continue.
12. Enter the account information and continue.
13. Finish the installation by confirming the suggested address.