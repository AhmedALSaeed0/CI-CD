Automated Build and Test with Jenkins on EC2 Instance
Project Setup
Tools Needed
EC2 instance on AWS to install Jenkins
GitHub account
Gitbash on your local host
Installation and Configuration Guide
Prepare GitHub Repository:
Create or select a GitHub repository.
Apply the Git Flow model.
bash
to install Jenkins
#!/bin/bash

# Install OpenJDK 17 JRE Headless
sudo apt install openjdk-17-jre-headless -y

# Download Jenkins GPG key
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

# Add Jenkins repository to package manager sources
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

# Update package manager repositories
sudo apt-get update

# Install Jenkins
sudo apt-get install jenkins -y

add Jenkinsfile

pipeline {
    agent any

    stages {
        stage('List Files') {
            steps {                
                sh 'ls'
            }
        }
    }
}
Save and exit.
Push the file to GitHub Repo:
bash
Copy code
$ git add Jenkinsfile
$ git commit -m "Add Jenkinsfile"
$ git push develop
Check your develop branch on GitHub repo.
Installing Jenkins container on EC2:
pipeline {
    agent any

    stages {
        stage('List Files') {
            steps {                
                sh 'ls'
            }
        }
    }
}
Add Inbound rule in the security group of the EC2 instance:
Type: Custom TCP
Port Range: 8080
Source: 0.0.0.0/0
Accessing Jenkins:
Go to a web browser and write: http://ec2-public-ip:8080.
Run the following command inside your container to get Jenkins password:
bash
Copy code
$ sudo cat /var/lib/jenkins/secrets/initialAdminPassword
Copy the password to Jenkins tab and sign in.
Configure Jenkins:
Install necessary plugins in Jenkins such as Git and Pipeline.
Connect Jenkins to GitHub.
Go to "Manage Jenkins" > "Manage Plugins" > "Available" and install "GitHub Integration Plugin".
Set up credentials in Jenkins for GitHub (username and token).
Create a new pipeline job.
Select "New Item", name your pipeline, and choose "Pipeline" as the type.
In the pipeline configuration, select "Pipeline script from SCM" and choose "Git" as the SCM.
Enter the repository URL and credentials.
Specify the branch to build.
In Build Triggers, Check "GitHub hook trigger for GITScm polling".
Save.
Implement Webhooks for Continuous Integration:
Set up webhooks in GitHub to trigger Jenkins builds on push events.
In GitHub, go to your repository settings and select "Webhooks".
Add a new webhook:
Payload URL: http://ec2-public-ip:8080/github-webhook/
Content type: application/json
Select "Just the push event".
Ensure the webhook is active.
With the webhook, Jenkins will trigger a new build every time changes are pushed to the connected branch.
Testing and Validation:
Push a change to the develop branch and verify Jenkins triggers a build.
Check the Jenkins dashboard for build status and output.
Feel free to adjust the formatting or add any additional information as needed.
