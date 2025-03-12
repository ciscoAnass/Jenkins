# Jenkins Project : Apache Web Server with Github Integration

## Bash Script

- Let's create our GitHub repository and integrate it with Jenkins. You can do that by checking this link: [Github Integeration](/files/6-Github-Integration.md) .
- After integrating your repository with Jenkins, let's create a Bash script in our GitHub repository.
- This Bash Script do :
- ---> Update package lists
- ---> Install Apache
- ---> Start Apache and enable it to start on boot
- ---> Clear the content of the existing index file and replace it with your own (you can use the `index.html` file I will create later or use your own HTML file)
- ---> Restart Apache Service
- ---> Verify that everything is working correctly.

```bash

#!/bin/bash

echo "=== Starting Apache Installation ==="

# Update package lists
echo "Updating package lists..."
sudo apt-get update

# Install Apache
echo "Installing Apache..."
sudo apt-get install -y apache2

# Start Apache and enable it to start on boot
echo "Starting Apache service..."
sudo systemctl start apache2
sudo systemctl enable apache2

# Create a custom index page
echo "Creating custom index page..."

echo " " > /var/www/html/index.html
cat index.html > /var/www/html/index.html


# Restart Apache
echo "Restarting Apache service..."
sudo systemctl restart apache2


# Verify Apache is running
echo "Verifying Apache status..."
if sudo systemctl is-active --quiet apache2; then
  echo "=== SUCCESS: Apache is running! ==="
  echo "You can access it at: http://$(hostname -I | cut -d " " -f1)"
  exit 0
else
  echo "=== ERROR: Apache failed to start ==="
  exit 1
fi

```

## Jenkinsfile

- Now, after creating our Bash script, let's create a Jenkinsfile so we can execute it.
- This `Jenkinsfile` will :
- ---> First, pull the latest code from Git so the pipeline can work with it.
- ---> Grant execution permissions to the Bash script file.
- ---> Run and Execute the Bash Script
- ---> Verify that everything is working correctly.
- ---> Finally, if everything works correctly, it will output `Apache deployment successful!`. Otherwise, if something fails, it will output `Apache deployment failed!`.

2- Jenkins File

```groovy

pipeline {
	agent any

	stages {
		stage('Checkout'){
			steps {
			checkout scm
			}
		}

		stage('Permissions') {
			steps {
			sh 'chmod +x apache.sh'
			}
		}

		stage('Deploy apache') {
			steps {
			sh './apache.sh'
			}
		}

        stage('Verify') {
            steps {
                sh 'curl -s http://localhost | grep "<!DOCTYPE html>"'
            }
        }
	}

    post {
        success {
            echo 'Apache deployment successful!'
        }
        failure {
            echo 'Apache deployment failed!'
        }
    }


}
```

## Index File

- This is the HTML file i did use for the apache WebServer

```html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Basic Beautiful Page</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            background: linear-gradient(135deg, #1e3c72, #2a5298);
            text-align: center;
        }
        h1 {
            color: #fff;
        }
        button {
            padding: 10px 20px;
            font-size: 16px;
            border: none;
            background-color: #1f4068;
            color: white;
            cursor: pointer;
            border-radius: 5px;
            transition: 0.3s;
        }
        button:hover {
            background-color: #102542;
        }
    </style>
</head>
<body>
    <h1>Welcome to My Page - CiscoAnass</h1>
    <p id="message">Click the button to change this text.</p>
    <button onclick="changeText()">Click Here</button>
    <script>
        function changeText() {
            document.getElementById('message').textContent = "You clicked the button!";
        }
    </script>
</body>
</html>

```