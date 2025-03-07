# Jenkins Installation

This guide will walk you through the installation of Jenkins on **Debian 12**. Follow the steps below to get Jenkins up and running on your machine.

***

## 1- Download Jenkins GPG key

```bash
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
```

## 2- Add Jenkins repository

```bash
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
```

## 3- Update package list

```bash
sudo apt-get update
```

## 4- Install Java (JDK and JRE)

```bash
sudo apt-get install fontconfig openjdk-17-jre openjdk-17-jdk -y
```

## 5- Check Java version

```bash
java -version
```

## 6- Install Jenkins

```bash
sudo apt-get install jenkins -y
sudo apt install -f
```

## 7- Start&Enable Jenkins service

```bash
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

## 8- Check Jenkins service status

```bash
sudo systemctl status jenkins
```

## 9- Access Jenkins web interface 

- Finally, open your browser and go to **`http://localhost:8080`** to access the Jenkins web interface.

![Jenkins Web Interface](/img/1-install.png)

***

- This is how you would structure a GitHub project README for Jenkins installation on **Debian 12**. Feel free to adjust the formatting or add more details to suit your project!
