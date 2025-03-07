# Jenkins : Plugins&Pipelines

## Access the Plugin Manager

1- First of all let's access to the Plugin Manager :

- Login to your Jenkins dashboard
- Click on "Manage Jenkins" in the left sidebar
- Select "Manage Plugins"
- Click on the "Available" tab to see plugins available for installation

- Let's give a try to Git Plugin

## Install Git Integration Plugin

The Git plugin allows Jenkins to pull code from Git repositories:

- Look for "Git" and "Git Client" plugins
- Check the boxes next to them
- Click "Install without restart" at the bottom of the page
- Check "Restart Jenkins when installation is complete and no jobs are running" if you want an automatic restart

***

## Install Docker Plugins

For Docker integration, you'll need several plugins:

- Go back to the "Available" tab
- Search for "Docker"
- Look for and select these plugins:

*`Docker Plugin`*
*`Docker Pipeline`*
*`Docker API Plugin`*

Click "Install without restart"

![Docker Plugin](/img/5-Docker.png)

***

## Install Pipeline Plugins

Jenkins Pipeline allows you to define your build/test/deploy pipeline as code:

3- Search for "Pipeline" on Available Plugins
Select these essential pipeline plugins:

- *`Pipeline`*
- *`Pipeline: API`*
- *`Pipeline: Basic Steps`*
- *`Pipeline: Build Step`*
- *`Pipeline: Declarative`*
- *`Pipeline: Declarative Extension Points API`*
- *`Pipeline: Groovy`*
- *`Pipeline: Job`*
- *`Pipeline: Nodes and Processes`*
- *`Pipeline: REST API`*
- *`Pipeline: Stage Step`*
- *`Pipeline: Stage View`*

Click "Install without restart"

***

## Install Blue Ocean

Blue Ocean provides a modern UI for Jenkins Pipelines:

- Go back to the "Available" tab
- Search for "Blue Ocean"
- Select `"Blue Ocean"` (this will automatically select all required Blue Ocean components)
- Click "Install without restart"

***

## Test the Plugins

Let's test the Git Plugin :

- On Jenkins Dashboard let's create a new item
- Create a new freestyle project
- Under Source Code Management, select "Git"
- You should be able to enter the repository URL

***

## Docker PLugin Test (Nginx)

**Prerequisites :**

- Make sure Docker is installed on your Jenkins server:

```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
```

- Add the Jenkins user to the Docker group so it can run Docker commands:

```bash
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
```

- Now Let's Create a Docker Test Pipeline

- --> From your Jenkins dashboard, click "New Item"
- --> Enter a name (e.g., "Docker-Nginx-Test")
- --> Select "Pipeline" and click "OK"
- --> Scroll down to the Pipeline section
- --> Copy and paste the pipeline script I will provide in the "Script" text area
- --> Click "Save"

```groovy
pipeline {
    agent any
    
    stages {
        stage('Pull Nginx Image') {
            steps {
                sh 'docker pull nginx:latest'
            }
        }
        
        stage('Run Nginx Container') {
            steps {
                // Stop and remove container if it exists
                sh 'docker stop nginx-test || true'
                sh 'docker rm nginx-test || true'
                
                // Run nginx container on port 8081
                sh 'docker run -d -p 8081:80 --name nginx-test nginx:latest'
            }
        }
        
        stage('Test Nginx') {
            steps {
                // Wait a moment for nginx to start
                sh 'sleep 5'
                
                // Test if nginx responds on port 8081
                sh 'curl -s http://localhost:8081 > /dev/null && echo "Nginx is running" || echo "Nginx failed to start"'
            }
        }
    }
    
    post {
        always {
            // Cleanup - stop and remove the container
            sh 'docker stop nginx-test || true'
            sh 'docker rm nginx-test || true'
        }
    }
}
```

- Make sure that the Nginx Docker image is running on an available port because using port 8080 will fail since Jenkins is already running on the same port.
