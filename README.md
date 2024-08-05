# jenkins-gitea-webserver-on-docker
Running jenkins, gitea &amp; webserver on docker container locally

## CI/CD Pipeline with Docker: Jenkins, Gitea, and Ubuntu Server

This project demonstrates a basic CI/CD pipeline using Docker containers to run Jenkins, Gitea, and an Ubuntu server. The pipeline is set up to automatically transfer changes from a Git repository to a web server upon code updates.
The setup consists of three Docker containers running locally:

Jenkins: Automates the build and deployment process.

Gitea: A self-hosted Git service for version control.

Ubuntu Server: Hosts the web application.

Each service (Jenkins, Gitea, and Ubuntu) has its own Dockerfile located in its respective directory. A docker-compose.yml file orchestrates the build and deployment of all containers.
The goal of this project is to demonstrate a CI/CD pipeline where:

Code changes are pushed to a Gitea repository, specifically an update to the index.html file.

Jenkins detects these changes using a webhook and triggers a build job.

The build job automatically transfers the updated index.html file to the Ubuntu server's web directory.


Compose file is as below with network and port mapping details:

```
version: '3.9'

services:
  git:
    build: ./git-server
    ports:
      - "3050:3000"
      - "2250:22"
    volumes:
      - git-data:/data

  jenkins:
    build: ./jenkins-server
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:
      - jenkins-data:/var/jenkins_home

  ubuntu:
    build: ./ubuntu-server
    ports:
      - "2200:22"
      - "8081:80" #nginx
    volumes:
      - ubuntu-data:/data

volumes:
  git-data:
  jenkins-data:
  ubuntu-data:

networks:
  default:
    name: devops_network
```

To build the images and containers run the command
```
docker-compose up -d --build
```
Once the containers are up, you can access the instances locally on localhost but I would suggest using the docker ip. It would be helpful to create webhook between git and jenkins.
The servers can be accessed at

jenkins: http://docker-ip:8080/

ubuntu: http://docker-ip:8081/

gites: http://docker-ip:3050/

### Jenkins & Gitea Integration

Setup the jenkins server and install gitea plugin, once the server is up, setup the configuration as depicted below:

<img width="745" alt="image" src="https://github.com/user-attachments/assets/d20d1919-b605-4fac-80e1-8f276fa458de">

Setup the git instance and create your repo, then from settings you can create a webhook, with details as below:

<img width="1097" alt="image" src="https://github.com/user-attachments/assets/505b9dad-5ba0-4f1f-92ef-612d76233212">

you can also test the connection, once done, we can setup ubuntu webserver. We can access the default web at http://docker-ip:8081/, it is going to be the nginx welcome page. But we want 
or webserver on this address.

```
#sign in to the ubuntu container, credentials are in the Dockerfile
docker exec -it container-id /bin/bash

#create dir
cd /var/www/
mkdir my-web
chown -R www-data:www-data /var/www/my-web
chmod -R 755 /var/www/my-web

#update nginx conf file
vim /etc/nginx/sites-available/default

#locate the server block and modify as below

server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/my-web;

    index index.html index.htm;

    server_name _;

    location / {
        try_files $uri $uri/ =404;
    }
}

```

### Setup Jenkins Job

Go to the plugin details and install plugin "Publish over SSH" and configure it as below:

<img width="440" alt="image" src="https://github.com/user-attachments/assets/faae4ec0-2c3f-44ab-90ff-f95219be1147">

Create a freestyle project and configure it as below:

<img width="543" alt="image" src="https://github.com/user-attachments/assets/dcbd1a73-6c5a-47c3-8ef8-051df835a13d">

<img width="409" alt="image" src="https://github.com/user-attachments/assets/6e4ab3fb-d2e3-4072-a09a-6ec883ae135d">

<img width="573" alt="image" src="https://github.com/user-attachments/assets/f2740d74-8e53-4fa6-8c06-5e4364be4918">

Hit save and you are done. Now go to you repo and push the updated index.html file and check the jenkins job, it should be triggered automatically and it will copy over the index.html file from the git to the ubuntu's target directory. If you go to the nginx webserver url you will see your application/webpage:

<img width="408" alt="image" src="https://github.com/user-attachments/assets/019e8376-0b49-41bf-8e5b-764fed7c6af2">




