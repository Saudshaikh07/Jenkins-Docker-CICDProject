# Jenkins-Docker-CICDProject

## Overview

This Jenkins Pipeline project automates the deployment of a Node.js ToDo application using Docker on an AWS Ubuntu instance. It streamlines the process of building, testing, and deploying the application, ensuring a reliable and reproducible deployment pipeline.

## Features

- **Continuous Integration/Continuous Deployment (CI/CD):** Automate the entire software delivery process.
- **Dockerized Deployment:** Utilize Docker containers for consistent deployment environments.
- **AWS Integration:** Deploy the application on an AWS Ubuntu instance.
- **Node.js Application Support:** Tailored for Node.js applications.
- **GitHub Integration:** Integrate Github with Jenkins for automatically deploying the application when a change occurs.

## Getting Started

Follow the steps below to set up and run the Jenkins Pipeline locally:

## Prerequisites

1. Jenkins Installed and Configured:
Ensure Jenkins is installed on your local machine or server.
Configure Jenkins with the necessary plugins for Docker and AWS integration.

2. AWS Credentials Configured:
Create an AWS account if you don't have one.
Configure AWS credentials with the required permissions for deploying resources.

3. Docker Installed:
Install Docker on the machine where Jenkins is running.
Ensure the Docker daemon is up and running.

## Installation and Usage (Instructions on how to use and configure your DevOps project.)

1. Create an Ubuntu Instance in AWS
  - Log in to the AWS Management Console.
    Navigate to EC2 and launch a new Ubuntu instance.
  - Configure Security Groups:
    Configure security groups to allow traffic on the necessary ports. Allow HTTP and HTTPS traffic in security groups.
  - Create a new key pair and attach it under the key pair name.

2. SSH into the AWS instance either by connecting using the AWS management console or using SSH.
  
3. Clone this repository to the AWS Ubuntu instance
   ```bash
   git clone https://github.com/Saudshaikh07/Jenkins-Docker-CICDProject.git
   
4. Install Jenkins using this script [here](./install.sh/)
   ```bash
   chmod +x install.sh
   bash ./install.sh
   ```
   - Verify if Jenkins is installed with the below command:
     ```bash
     sudo systemctl status jenkins
     ```
   - Verify if docker is installed with the below command:
     ```bash
     docker --version
     ```

5. Configure Jenkins
   - Access Jenkins on your local machine using the public IP of the AWS Ubuntu instance with 8080 (For example: http://54.227.55.220:8080/)
   - SSH into the AWS ubuntu instance and run the below command:
   ```bash
   cat /var/jenkins_home/secrets/initialAdminPassword
   ```
   - Copy the password and paste it in the password section on Jenkins
     ![image](https://github.com/Saudshaikh07/Jenkins-Docker-CICDProject/assets/48210809/ad33129d-3802-4015-918b-16664878febe)
   - Install suggested plugins. Also install docker-build-step, delivery pipeline and docker plugin
     ![image](https://github.com/Saudshaikh07/Jenkins-Docker-CICDProject/assets/48210809/796aec27-c6a2-4f10-b506-9600f2370ca7)

6. Create the first job on Jenkins
   - Select "New Item" and enter any name and select "Freestyle Project" and hit Ok.
     ![image](https://github.com/Saudshaikh07/Jenkins-Docker-CICDProject/assets/48210809/944918e3-1612-49e8-add5-6b51179da47a)
   - Under "Source Code Management" Section, click Git and add the repository url for your app with all the other files.
     For example: https://github.com/Saudshaikh07/Jenkins-Docker-CICDProject.git
   - Run ssh-keygen in your AWS Ubuntu instance and that it will generate public and private key to create connection between Github and Jenkins.
     Go inside the directory where the keys are stored
     ```bash
     cd .ssh
     ```
   - We need to add the public key to Github and private key to Jenkins.
     ```bash
     cat id_rsa.pub
     ```
     and paste that key into Github - "GitHub -> Open setting -> Under Access Section Click on "SSH and GPG keys" -> Add SSH Key"
   - Now run the below command to get the private key.
     ```bash
     cat id_rsa
     ```
     and paste that key into Jenkins credentials.
     Under Source code Management, add credentials -> Jenkins -> Under kind select SSH with Private key, inside Private key select 'enter directly' and enter your private key -> Click Add.
     Under the Build Triggers Section, select GitHub hook trigger for GITScm polling.
   - Then select "Add build step" under Execute shell and add the below commands.
     ```bash
     docker build . -t node-saud-app       #This will build image from dockerfile with name node-saud-app
     docker run -d --name node-ctr1 -p 8000:8000 node-saud-app   #This will run container with name node-ctr1 ,-p = mapping container port with application port, -d = daemon mode i.e in background
     ```

7. Add “Github integration plugin” in Jenkins.
   - Select Manage Jenkins option on the dashboard → Manage plugins -> Available Plugins -> search "Github integration". Restart the jenkins server after the plugin is installed.
   - Add Webhook to integrate Github, Open Repository setting on Github -> Webhook -> Add Webhook -> enter jenkins url(http://”PublicIP”:8080/github-webhook/) inside payload url , select application/json under content type -> Click on Add Webhook.
     <img width="1263" alt="Screenshot 2023-12-02 at 4 45 49 PM" src="https://github.com/Saudshaikh07/Jenkins-Docker-CICDProject/assets/48210809/f1766603-c1d5-4611-a53f-1a8916b13c70">


8. Now if a developer make any changes in the github repository then the deployment will take place automatically.

<img width="1417" alt="Screenshot 2023-12-02 at 4 45 34 PM" src="https://github.com/Saudshaikh07/Jenkins-Docker-CICDProject/assets/48210809/455f9357-e456-4680-87ca-39a45a0658f5">


9. **Final UI of the Nodejs App!**
   You can access the app using the public IP of the server with 8000 port. (http://54.227.55.220:8000/)

![image](https://github.com/Saudshaikh07/Jenkins-Docker-CICDProject/assets/48210809/ed7ed0eb-666d-41b7-ab5b-d0fa6b61c9e0)


## Scripts and Files

Docker and Jenkins installation script -  [here](./install.sh/)
NodeJS app file - [here](./app.js/)
