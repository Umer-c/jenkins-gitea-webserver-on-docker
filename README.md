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

