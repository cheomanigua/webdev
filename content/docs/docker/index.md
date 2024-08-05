---
title: "Docker"
description: "Docker main concepts and reference"
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

### Creating a nginx container

`docker run -d -p 8080:80 --name mynginx nginx` 

To browse, visit **localhost:8080** on a web browser

To access the container's shell, issue:

`docker exec -it mynginx bash` or `sh`

Pages are served from `/usr/share/nginx/html` 

### Finding the IP of a container
`docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container_name_or_id`

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

## Swarm

- Start a Swarm cluster master node
```
docker swarm init --advertise-addr [IP_ADDRESS]
```

Copy the token


- Add a node in the Swarm cluster

From a new machine, issue:
```
docker swarm join --token [the_copied_token] [IP_ADDRESS_OF_MASTER_NODE]:2377
```

- See all nodes in the cluster
```
docker node ls
```

- Create/Deploy a service/application in the Swarm cluster
```
docker service create --name [NAME] --publish published=8080,target=80 --replicas 2 httpd 
```

- See running servcies
```
docker sevice ls
```

- See specific service information
```
docker service ps [NAME]
```

- Scale a servcie
```
docker service scale [NAME]=3
```

- Update a service with a new/old version
```
docker service update --image redis:3.0.7 redis
```

- Delete Swarm machine
```
rm -rf /var/lib/docker/swarm
systemctl restart docker
```

# Deploy docker image to Google Cloud Run


## Push local image to Google Artifact Registry

- Quick start: [https://cloud.google.com/artifact-registry/docs/docker/store-docker-container-images](https://cloud.google.com/artifact-registry/docs/docker/store-docker-container-images)

- Overview: [https://cloud.google.com/artifact-registry/docs/docker](https://cloud.google.com/artifact-registry/docs/docker)

### Set defaults

```
$ gcloud config set project [project-name]
```

### Push local docker image to Google Cloud Artifact Registry

1. Enable Artifact Registry API [[docs](https://cloud.google.com/sdk/gcloud/reference/services/enable)]
```
$ gcloud services enable artifactregistry.googleapis.com
```

2. Create an artifacts repository named **test** to store docker images [[docs](https://cloud.google.com/sdk/gcloud/reference/artifacts/repositories/create)]
```
$ gcloud artifacts repositories create test --repository-format=docker
```
3. Create a service account called **dockerpusher** that can push Docker images from our local machine to Google Cloud Artifact Registry [[docs](https://cloud.google.com/sdk/gcloud/reference/iam/service-accounts/create)]
```
$ gcloud iam service-accounts create dockerpusher --description="In charge of pushing docker images to Artifact Registry" --display-name="Docker Pusher"
```

4. Give **dockerpusher** the role of *Artifact Registry Writer* [[docs](https://cloud.google.com/sdk/gcloud/reference/iam/service-accounts/add-iam-policy-binding)]
``` bash
$ gcloud projects add-iam-policy-binding beach-walks-azure --member="serviceAccount:dockerpusher@beach-walks-azure.iam.gserviceaccount.com" --role="roles/artifactregistry.writer"
```
5. Create a service account key file for **dockerpusher** to indentify it on other platforms (in this case Docker) [[docs](https://cloud.google.com/iam/docs/keys-create-delete#iam-service-account-keys-create-gcloud)]
```
$ gcloud iam service-accounts keys create ~/dockerpusher-private-key.json --iam-account=dockerpusher@beach-walks-azure.iam.gserviceaccount.com
```

6. Authenticating to Artifact Registry for Docker for a service account (in this case **dockerpusher**) using '*gcloud CLI credential helper*' method [[docs](https://cloud.google.com/artifact-registry/docs/docker/authentication#gcloud-helper)]
```
$ gcloud auth activate-service-account dockerpusher@beach-walks-azure.iam.gserviceaccount.com --key-file=~/dockerpusher-private-key.json
```
Note that you can also authenticate for a user gmail account with the '*gcloud CLI credential helper*' method by using `$ gcloud auth login`

7. Authenticating to a repository using '*gcloud CLI credential helper*' method [[docs](https://cloud.google.com/artifact-registry/docs/docker/pushing-and-pulling#auth)]

- If your `~/.docker/config.json` file doe not exist, create one and add curly braces
```
$ echo "{}" >  $HOME/.docker/config.json
```
- and then add *us-central1* to config.json:
```
gcloud auth configure-docker us-central1-docker.pkg.dev
```

8. Tag and push image
```
$ docker tag go-app us-central1-docker.pkg.dev/beach-walks-azure/test/
$ docker push us-central1-docker.pkg.dev/beach-walks-azure/test/go-app:v1
$ gcloud artifacts repositories describe test
$ gcloud artifacts docker images list us-central1-docker.pkg.dev/beach-walks-azure/test/go-app --include-tags
```

References:
- [Create and delete service account keys](https://cloud.google.com/iam/docs/keys-create-delete#creating)
- [Configure authentication to Artifact Registry for Docker](https://cloud.google.com/artifact-registry/docs/docker/authentication#standalone-helper)
- [Push and pull images](https://cloud.google.com/artifact-registry/docs/docker/pushing-and-pulling)

## Deploy image to Cloud Run

[https://cloud.google.com/run/docs/quickstarts/deploy-container](https://cloud.google.com/run/docs/quickstarts/deploy-container)

# Tips and Hints

* If there's a need to run Docker containers in production without Kubernetes, use the `--init` flag upon `docker run` . This injects a `PID 1` process, which handles its terminated children correctly, into the container to run.

* There are four important files that acts as contracts between deverlopers and sys admins:
  + **Dockerfile**: To build or application image
  + **docker-compose.yml**: To execute our application
  + **Dockerfile.test**: To build our application test image
  + **docker-test.yml**: Test our application
