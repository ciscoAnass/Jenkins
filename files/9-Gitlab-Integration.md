# Jenkins : Gitlab Integration

## Part 1 : Set Up Docker

- First of all let's Install docker packages :

```bash
sudo apt install docker.io docker-compose
```

- After Installing Docker Engine and Compose let's create a folder named **Project** :

```bash
mkdir project
cd project
```

- Inside this folder let's create a docker compose file and edit it :

```bash
touch docker-compose.yml
nano docker-compose.yml
```

- Paste this content into your `Docker-compose.yml` :

```yml
version: '3.8'

services:
  gitlab:
    image: gitlab/gitlab-ce:latest
    container_name: gitlab-test1
    hostname: gitlab.local
    networks:
      - devops-network-anass
    ports:
      - "8929:80"
      - "2224:22"
    volumes:
      - gitlab-config:/etc/gitlab
      - gitlab-logs:/var/log/gitlab
      - gitlab-data:/var/opt/gitlab
    restart: unless-stopped

  jenkins:
    image: jenkins/jenkins:lts
    container_name: jenkins-test1
    networks:
      -  devops-network-anass
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:
      - jenkins-data:/var/jenkins_home
    restart: unless-stopped


networks:
  devops-network-anass:
    driver: bridge

volumes:
  gitlab-config:
  gitlab-logs:
  gitlab-data:
  jenkins-data:

```

Now let' run our containers :

```bash
sudo docker-compose up -d
```

---

## Part 2 : Set Up Gitlab

- Open your browser and go to : **[Gitlab](http://localhost:8929)**
- In the username field, enter **root**
- To retrieve your GitLab password, run the following command:

```bash
sudo docker exec -it gitlab-anass grep 'Password:' /etc/gitlab/initial_root_password
```

- Copy and paste the password to log in to GitLab.
- Once logged in, create a new project and name it as you wish.
- Now, it's time to generate an Access Token for your repository:
- Go to Settings → Access Tokens → Add New Token.
- Name it as you wish and assign the following scopes: **"api - read_repository - write_repository"**
- Copy the Access Token and save it in a secure file, as this is the last time you will see it.

---

## Part 3 : Set Up Jenkins

- Go to your browser and get access to this link : **[Jenkins](http://localhost:8080)**
- In the username Area put  **admin**
- To get the password of Your Jenkins execute this command :

```bash
sudo docker exec -it jenkinsb-anass cat /var/jenkins_home/secrets/initialAdminPassword
```

Now Let's get access to our Jenkins.

- Lets Install the Suggested Plugins that the Jenkins recommend you.
- And Lets install our specific Plugins which are :  **`Gitlab`** and **`OWASP Dependency-Check`**

---

## Part 4 : Gitlab Integration on Jenkins (Access Token)

- Go to the Jenkins portal and navigate to: Manage Jenkins → System → GitLab → Credentials.

- Give the connection a random name.
- Set the GitLab Host URL to: **`http://172.17.0.1:8929`**
- For Credentials, select GitLab API token and paste the Access Token you created in GitLab.

---

## Part 5 : First Job to test the Integrity

- Go to the Jenkins portal and click on **New Item**.
- Give it a name and select Freestyle project.
- In the SCM section, choose **Git** and enter the repository URL.
- For Credentials, use the Username and Password (Access Token).
- The default branch is main.
