# Jenkins Project : Nginx Web Server with Github Integration

- Let's create our GitHub repository and integrate it with Jenkins. You can do that by checking this link: [Github Integeration](/files/6-Github-Integration.md) .

## 1. Create Your Deployment Script

Create a file named `deploy-nginx.sh` in your GitHub repository:

```bash
#!/bin/bash
# Script to install and configure Nginx locally

echo "=== Starting Nginx Installation ==="

# Update package lists
echo "Updating package lists..."
sudo apt-get update

# Install Nginx
echo "Installing Nginx..."
sudo apt-get install -y nginx

# Start Nginx and enable it to start on boot
echo "Starting Nginx service..."
sudo systemctl start nginx
sudo systemctl enable nginx

# Create a custom index page
echo "Creating custom index page..."
echo "<h1>Nginx Deployed with Jenkins!</h1>" | sudo tee /var/www/html/index.html

# Verify Nginx is running
echo "Verifying Nginx status..."
if sudo systemctl is-active --quiet nginx; then
  echo "=== SUCCESS: Nginx is running! ==="
  echo "You can access it at: http://$(hostname -I | awk '{print $1}')"
  exit 0
else
  echo "=== ERROR: Nginx failed to start ==="
  exit 1
fi
```

## 2. Create a Simple Jenkinsfile

Create a file named `Jenkinsfile` in your GitHub repository:

```groovy
pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Prepare') {
            steps {
                sh 'chmod +x deploy-nginx.sh'
            }
        }
        
        stage('Deploy Nginx') {
            steps {
                sh './deploy-nginx.sh'
            }
        }
        
        stage('Verify') {
            steps {
                sh 'curl -s http://localhost | grep "Deployed with Jenkins"'
            }
        }
    }
    
    post {
        success {
            echo 'Nginx deployment successful!'
        }
        failure {
            echo 'Nginx deployment failed!'
        }
    }
}
```

## 3. Set Up Jenkins Permissions

Jenkins needs permissions to run commands with sudo. Add these permissions by editing the sudoers file:

```bash
# Run this on your your jenkins Server
sudo visudo -f /etc/sudoers.d/jenkins

# Add this line (assuming 'jenkins' is the user running Jenkins)
jenkins ALL=(ALL) NOPASSWD: /usr/bin/apt-get, /usr/bin/systemctl, /usr/bin/tee, /bin/systemctl
```

## 4. Create a Jenkins Pipeline Job

1. In Jenkins, click "New Item"
2. Enter a name like "nginx-deployment"
3. Select "Pipeline" and click "OK"
4. In the configuration:
    - Under "Pipeline", select "Pipeline script from SCM"
    - Select "Git" as the SCM
    - Enter your GitHub repository URL
    - Specify the branch (usually "main" or "master")
    - Set "Jenkinsfile" as the Script Path
    - Click "Save"

## 5. Run Your Pipeline

1. Click "Build Now" to start the pipeline
2. Monitor the build console output
3. When completed successfully, Nginx will be running on your machine

## 6. Access Your Nginx Server

You can access your Nginx server at:

- http://localhost (from the machine itself)
- http://YOUR_IP (from the internet, if your security group allows port 80)
