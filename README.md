# jenkins-gitea-webserver-on-docker
Running jenkins, gitea &amp; webserver on docker container locally

## CI/CD Pipeline with Docker: Jenkins, Gitea, and Ubuntu Server

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

