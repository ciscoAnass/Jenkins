# Despligue de imagenes a DockerHub

## Parte 1 : Instalacionde Docker y Preparacion de Entorno

- En nuestro localhost instalamos docker :

```bash
sudo apt install docker.io docker-compose
```

- Creamos una carpeta se llama proyecto y en esta carpeta creamos nuestro fichero docker-compose.yml :

```bash
mkdir proyecto
cd proyecto
touch docker-compose.yml
nano docker-compose.yml
```

  - Metemos esta configuraciona  nuestro fichero docker-compose.yml :

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
      - /var/run/docker.sock:/var/run/docker.sock
    restart: unless-stopped
    group_add:
      -  "998"

networks:
  devops-network-anass:
    driver: bridge

volumes:
  gitlab-config:
  gitlab-logs:
  gitlab-data:
  jenkins-data:
```


- Lanzamos nuestros contendores de Gitlab y Jenkins :

```bash
sudo docker-compose up -d
```

---

## Parte 2 : COnfiugurar Gitlab 

- En Gitlab accedemos como root
- Para obtener la contraseña ejecutamos este comando :

```bash
sudo docker exec -it gitlab-test1 grep 'Password:' /etc/gitlab/initial_root_password
```


- Creamos un Repositorio en Gitlab.
- Y generamos un AccessToken con roles de **"api - read_repository - write_repository"** y guardamos este AccessToken

## Gitlab : Script build_and_push.sh
- en Nuestro Gitlab Creamos un Script bash se llama **build_and_push.sh**:


```bash
#!/bin/bash
set -e


echo "Proceso de crear Imagen"


# crear las imaimagenesges 
docker commit jenkins-test1 anass24/jenkins-anass24:latest
docker commit gitlab-test1 anass24/gitlab-anass24:latest


# Push las imagenes a DockerHub
docker push anass24/jenkins-anass24:latest
docker push anass24/gitlab-anass24:latest

echo "la imagen de Jenkins y Gitlab estan creadas y subidas a DOcker HUb ✅"
```

## Gitlab : Jenkinsfile

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
                sh 'chmod +x build_and_push.sh'
            }
        }
        
        stage('Build and Push') {
            steps {
                sh './build_and_push.sh'
            }
        }
        
        stage('Verify') {
            steps {
                sh 'docker images | grep anass24'
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline compeltado!'
        }
        failure {
            echo 'Pipeline fallado!'
        }
    }
}
```

---

## Parte 3 : Configuracion Jenkins

- En nuestro contenedor Jenkins Instalamod el paquete `docker-ce-cli`

```bash
docker exec -it jenkins-test1 bash
apt-get update
apt-get install -y apt-transport-https ca-certificates curl gnupg lsb-release
curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
apt-get update
apt-get install -y docker-ce-cli
exit
```

- En la maquina local (localhost) :

```bash
sudo chmod 666 /var/run/docker.sock
```

---

## Parte 4 : Integracion entre Gitlab y Jenkins


- Logueamos a Jenkins con el usuario **admin**
- Para Obtener la contraseña del usuario admin ejecutamos este comando :

```bash
sudo docker exec -it jenkinsb-test1 cat /var/jenkins_home/secrets/initialAdminPassword
```

- Instalamos los Plugins **Gitlab** y **OWASP Dependency-Check**

- Ve al portal de Jenkins y ve a: Administrar Jenkins → Sistema → GitLab → Credenciales.

- Asigna un nombre aleatorio a la conexión.

- Establece la URL del host de GitLab en: http://172.17.0.1:8929

- En Credenciales, selecciona el token de la API de GitLab y pega el token de acceso que creaste en GitLab.

