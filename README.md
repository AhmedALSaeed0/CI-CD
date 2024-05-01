# Automated Build and Test with Jenkins on EC2 Instance

## Project Setup

### Tools Needed
- EC2 instance on AWS to install Jenkins
- GitHub account
- Gitbash on your local host

### Prepare GitHub Repository
1. Create or select a GitHub repository.
2. Apply the Git Flow model.

## Installation on EC2 Instance
1. Use the following script to install Jenkins:

    ```bash
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
    sudo apt-get install jenkins -y
    ```

2. Add Jenkinsfile to your repository with the following content:

    ```groovy
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
    ```

3. Push the Jenkinsfile to your GitHub repository:

    ```bash
    $ git add Jenkinsfile
    $ git commit -m "Add Jenkinsfile"
    $ git push origin develop
    ```

## Accessing Jenkins
1. Install Jenkins container on your EC2 instance.
2. Add Inbound rule in the security group of the EC2 instance:
    - Type: Custom TCP
    - Port Range: 8080
    - Source: 0.0.0.0/0
3. Access Jenkins by navigating to `http://ec2-public-ip:8080` in a web browser.
4. Retrieve Jenkins initial admin password by running the following command inside your container:

    ```bash
    $ sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```

5. Copy the password to Jenkins login page and sign in.

## Configure Jenkins
1. Install necessary plugins in Jenkins such as Git and Pipeline.
2. Connect Jenkins to GitHub:
    - Go to "Manage Jenkins" > "Manage Plugins" > "Available" and install "GitHub Integration Plugin".
    - Set up credentials in Jenkins for GitHub (username and token).
3. Create a new pipeline job:
    - Select "New Item", name your pipeline, and choose "Pipeline" as the type.
    - In the pipeline configuration, select "Pipeline script from SCM" and choose "Git" as the SCM.
    - Enter the repository URL and credentials.
    - Specify the branch to build.
    - In Build Triggers, Check "GitHub hook trigger for GITScm polling".
    - Save.

## Implement Webhooks for Continuous Integration
1. Set up webhooks in GitHub to trigger Jenkins builds on push events:
    - In GitHub, go to your repository settings and select "Webhooks".
    - Add a new webhook:
        - Payload URL: `http://ec2-public-ip:8080/github-webhook/`
        - Content type: application/json
        - Select "Just the push event".
    - Ensure the webhook is active.

2. With the webhook configured, Jenkins will trigger a new build every time changes are pushed to the connected branch.

## Testing and Validation
1. Push a change to the develop branch and verify Jenkins triggers a build.
2. Check the Jenkins dashboard for build status and output.
