---
title: "Docker"
description: "Docker main concepts and reference"
weight: 4
---

### Installation

You can follow the Docker installation instructions in the following Linux distributions:

- **Centos**: [https://docs.docker.com/engine/install/centos/](https://docs.docker.com/engine/install/centos/)
- **Debian**: [https://docs.docker.com/engine/install/debian/](https://docs.docker.com/engine/install/debian/)
- **Fedora**: [https://docs.docker.com/engine/install/fedora/](https://docs.docker.com/engine/install/fedora/)
- **Ubuntu**: [https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)


### Post installation

After installation, you have to make the user member of group `docker` :

``` 
sudo usermod -aG docker $USER
```

### Commands

#### General

* **docker version**: Check docker version installed
* **docker info**: Info about the docker environment
* **docker login -u "username"**: Access your Docker Hub repository

#### Images

* **docker images**: List all available images in our host
* **docker pull [image]**: Pull the image from docker hub to our host
* **docker run -d [image]**: Pull and start a new container based on the image (Pull only if image not present in local)
* **docker rmi [image]**: Remove the image from our host
* **docker rmi -f $(docker images -a -q)**: Remove all images

#### Containers

* **docker ps**: List all running containers
* **docker ps -a**: List all containers, running or not
* **docker stop [container]**: Stop a running container
* **docker start [container]**: Start a stopped container
* **docker rm [container]**: Remove a non running container 
* **docker rm [container] -f**: Remove a container, running or not 
* **docker kill $(docker ps -q)** Stop all running containers
* **docker rm $(docker ps -aq)**: Remove all non running containers
* **docker rm $(docker ps -aq) -f**: Remove all containers, running or not
* **docker exec -it [container] [BASH]**: Access container file system command prompt. [BASH] can be `/bin/bash` or `/bin/sh`
* **docker create nginx top**: Create a writeable container layer over the specified image and prepares it for running the specified command.
* **docker logs [container]**: Fetch the logs of the container.

#### Volumes
- **docker volume ls**: List all volumes
- **docker volume rm [volume]**: Remove volume [volume]
- **docker volume prune**: Remove all volumes


#### Cleaning the system

- **docker system df**: Show information on images, containers, volumes and build cache size
- **docker buildx prune -f**: Remove all cache
- **docker builder prune**: Remove all dangling build cache
- **docker system prune -a**: Delete all images, containers and cache

## Creating a nginx container

`docker run -d -p 8080:80 --name mynginx nginx` 

The above command does the following:

1. Create a **nginx** container in the background called **mynginx** by using the image **nginx**. If the image **nginx** is not already in our system, it will be downloaded automatically from Docker Hub.
2. Use port **8080** to access the website

To browse, visit **localhost:8080** on a web browser

To access the container's shell, issue:

`docker exec -it mynginx bash` or `sh`

Pages are served from `/usr/share/nginx/html` 

### Finding the IP of a container
`docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container_name_or_id`


## Building our own image

Providing we have these files in the directory where we are going to issue the build command:

* about.html
* contact.html
* index.html
* services.html

We create and edit the following file: `Dockerfile` 

```dockerfile 
FROM nginx:latest
WORKDIR /usr/share/nginx/html
COPY . .
```

And now we issue the build command:

`docker image build -t nginx-mywebsite .` 

### Multi-stage builds

Normal builds generate very big image sizes. To reduce the image size to a minimun, Multi-stage builds come to the rescue.

The examples below shows how to create a Dockerfile to build a Golang app in a Multi-stage fashion. The first Dockerfile is a normal build and the second Dockerfile is a Multi-stage build.

*Normal build*

```dockerfile
FROM golang:1.22.5

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY . ./

RUN GO111MODULE=on CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -mod=readonly -v -o main . 

COPY /static ./static/

CMD ["/main"]
```

*Multi-stage build*

```dockerfile
FROM golang:1.22.5 AS build-stage

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY . ./

RUN GO111MODULE=on CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -mod=readonly -v -o main . 

# Deploy the application binary into a lean image
FROM gcr.io/distroless/static
COPY --from=build-stage /app /

COPY /static ./static/

CMD ["/main"]
```

To build the image, issue: `docker image build -t golang-image .` 


### Pushing our new created image to Docker Hub

``` 
docker tag nginx-mywebsite nginx-mywebsite:v1
docker tag nginx-mywebsite <dockerhubusername>/<nginx-mywebsite>|<fooimage>
docker login
docker push <dockerhubusername>/nginx-mywebsite
docker logout
```

**Note**: It takes 24-48 hours for Docker Hub to index the image


## Volumes

### Volumes for development use

* Dockerfile:
```dockerfile
FROM php:7.2-apache
COPY src/ /var/www/html/
EXPOSE 80
```
**home/user/mywebsite:~$**: `docker build -t myimage .`

The above command does the following:
* Build an image of an apache server with php
* Copy the content of `/home/user/mywebsite/src/` into the the apache `/var/www/html/` serving directory
* Expose port 80 so it can be reached

Next we run:

`docker run -d -p 8080:80 -v /home/user/mywebsite/src/:/var/www/html --name mywebsite myimage` 

The above command does the following:

* Create a **nginx** container in the background called **mywebsite**
* Use port 8080 to access the website
* Mount the container directory `/var/www/html/` on to local directory `/home/user/mywebsite/src/` where the source code of the site is held. 
* We can now create and edit **index.php** and other files and directories directly in `/home/user/mywebsite/src/`, and it will update automatically.
* We can visit the website on **localhost:8080**

### Delete unused or lost volumes
```
$ docker volume rm $(docker volume ls -qf dangling=true)
$ docker volume ls -qf dangling=true | xargs -r docker volume rm
```

## Docker compose

```
docker-compose up -d
docker-compose down
docker-compose down --volumes
```

### Scaling

Scaling services with *docker-compose* is as simple as:

```
docker-compose up -d --scale myservice=3
```

# Tips and Hints

* If there's a need to run Docker containers in production without Kubernetes, use the `--init` flag upon `docker run` . This injects a `PID 1` process, which handles its terminated children correctly, into the container to run.

* There are four important files that acts as contracts between deverlopers and sys admins:
  + **Dockerfile**: To build or application image
  + **docker-compose.yml**: To execute our application
  + **Dockerfile.test**: To build our application test image
  + **docker-test.yml**: Test our application
