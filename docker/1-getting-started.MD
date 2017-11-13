<img src="https://www.docker.com/sites/default/files/mono_vertical_large.png" height="100px" />

---
Welcome !! , this small getting-started guide stands as cheatsheet in order to start using Docker (samples are associated to SoftwareAG products).

- [Installation](#install) : install on both linux and windows
- [Docker Service](#service) : start and stop service on linux
- [Docker File](#dockerfile) : instruction file to build images
- [Docker Images](#image) : repository of images on local machine
- [Docker Containers](#container) : instanciate images into containers
- [Docker Volumes](#volume) : add persistent storage as external volume
- [Docker Network](#network) : make containers communicate to each other
- [Docker Hub](#hub) : publish your images to others
- [Docker Compose](#compose) : run several containers with a single line

<a name="install"/>

## Installation

* [Docker installation on CentOS](https://docs.docker.com/engine/installation/linux/docker-ce/centos/#install-docker-ce)

* [Docker installation on Windows](https://docs.docker.com/docker-for-windows/install/)

* **Hyper-V** *(hypervisor required for docker)* must be enabled in Windows machine. As it is not possible to run virtual machine (VMWare) when this flag is enabled you should probably have to enable and disable it frequently. In PowerShell, as Administrator run the following command and restart Windows.

```shell
# Disable hyper-V on Windows
bcdedit /set hypervisorlaunchtype off

# Enable hyper-V on Windows
bcdedit /set hypervisorlaunchtype auto 
```

<a name="service"/>

## Working with Docker service

Docker is a command line tools but the service behind should be started or the Windows Application should be started.

* Start and Stop Docker Service

```shell
# START docker service
sudo service docker start
sudo systemctl start docker

# STOP docker service
sudo service docker stop
sudo systemctl stop docker

# Get information on service
sudo systemctl status docker.service -a
```

* Once started you can information of your environment

```shell
# Get Version
docker version

# Get info (containers, images, settings)
docker info
```

<a name="dockerfile"/>

## Working with DockerFile

* Docker file are like makefile and is a list of instruction to build an image. Your start from an existing image like an OS with the `FROM`keyword.

```shell
# Basic install of couchdb
# This will move the couchdb http server to port 8101 so adjust the port for your needs. 
ARG VERSION=latest
FROM busybox:$VERSION
MAINTAINER Clunven

#Append item to files
RUN echo "deb http://us.archive.ubuntu.com/ubuntu/ precise universe" >> /etc/apt/sources.list

# COPY OR IMPORT FILES
COPY server.cnf /tmp/targetFolder

# Execute command to prepare
RUN apt-get -y update
RUN /bin/bash -c 'source $HOME/.bashrc; echo $HOME'

# Define environment variable
ENV MYVARNAME MYVALUE

# Expose ports
EXPOSE 8101

# Commands at startup
ENTRYPOINT ["/usr/sbin/apache2"]
# Optional parameters
CMD ["/usr/local/bin/couchdb"]
```
* Build an image from a DockerFile [documentation](https://docs.docker.com/engine/reference/commandline/build/)

```shell
build -f DockerFile.txt -t repo:tag
```

<a name="image"/>

## Working with Docker Images

```shell
# List images available
sudo docker images

# Export image as TARBALL
sudo docker save -o /tmp/target.tar repo:tag

# Import image from TARBALL
sudo docker load -i /tmp/target.tar

# Remove an image from repository (should be stop)
# Option -f is optional if the image has several tags.
sudo docker rmi -f repoName:tagName

# Rename image
sudo docker tag image:tagName image:newTag
sudo docker tag sag/apigateway:10.1 tag sag/apigateway-clustered:10.1
```

To understand Docker images lifecycle you can use : 

<img src="https://blog.octo.com/wp-content/uploads/2014/01/docker-stages.png" />

<a name="container"/>

## Working with Containers

```shell
# Check running containers
sudo docker ps
sudo docker ps -l (option to see last)
sudo docker ps -a (option to see all)

# Check installation (with hello-world image)
sudo docker run hello-world

# Check Logs of tc-server
docker logs -f tc-server

# Connect SSH to a running container (if linux)
sudo docker exec -it <container_ID> bash

# Start a container
# -d is for detach
# -i is for interactive
# -t is for interactive
# -m is for memory eg:10214M
# --memory-reservation 200M
# -e "var=value"
sudo docker run -d -p 5555:5555 --name <containerName> <imageName>:<tag>

# Stop a container
sudo docker stop -t90 containerX

# Kill a container
sudo docker kill -t90 containerX

# Remove an (not running)  from repository
sudo docker rm <containerID>

# List all containers ID
docker ps -aq

# List all exited containers ID (to clean up)
docker ps -aq -f status=exited

# Remove containers (linux)
docker rm `docker ps -aq -f status=exited`

# Commit a container as a new image when changing data
docker commit -a=clunven -m="Adding sample stuff" -p=true d92088bedb2a terracotta-hello:1.0

```
<a name="volume"/>

## Working with Docker Volume

```shell
# create a volume
docker volume create --name eventDataStore1

# List Volumes
docker volume ls

# Inspect
docker volume inspect eventDataStore1

# Start Container with a volume
docker run -d -p 5555:5555 -p 9072:9072 -v eventDataStore1:/sag/EventDataStore/data/SAG_EventDataStore/ --name apigateway  is:apigw
```

<a name="network"/>

## Working with Docker Network

```shell
# List networks
docker network ls

# See network detail (172.17.0.1 default gateway, 172.17.0.X default IP)
docker network inspect bridge

# 'link' leverage on default 'bridge'
docker run -d --name redis1 redis
docker run -d --link redis1:redis1 --name container2 busybox

# Create user-defined network
docker network create --driver bridge apigwcluster

# Run a container using defined network
docker run -d --name=c1 -h=c1 --network=apigwcluster softwareag/terracotta-server:4.3.4
docker run -d --name=c2 -h=c2 --network=apigwcluster softwareag/terracotta-server:4.3.4
docker exec -it 1c4e06a69e27 bash
$ping c1 ==> OK
$ping c2 ==> OK
```

<a name="hub"/>

## Working with dockerHub

```shell
# Login to target Docker Hub (to pull)
docker login -u <username> -p <password> XXX

# TAG
sudo docker  tag repositoryName:tagName urldockerhub/repositoryName:tagName

# PUSH
sudo docker push urldockerhub/repositoryName:tagName

# PULL
sudo docker push urldockerhub/repositoryName:tagName
```

<a name="compose"/>

## Working with Docker Compose

Docker compose is a tool to build small clustering solutions [Documentation](https://docs.docker.com/compose/)

* Run a compose file
```shell
docker-compose up
```

* Create sample `docker-compose.yml` file
```yml
version: '3'

networks:
  apigw-cluster:
  driver: overlay

services:
  
  # API GW Node 1
  apigateway1:
    image: is:apigw
    networks:
     - apigw-cluster
    ports:
     - "5555:5555"
     - "9072:9072"
     - "9073:9073"
  
  # API GW Node 2
  apigateway2:
    image: is:apigw
    networks:
     - apigw-cluster
    ports:
     - "15555:5555"
     - "19072:9072"
     - "19073:9073"
  
  # API GW Node 3
  apigateway3:
    image: is:apigw
    networks:
     - apigw-cluster
    ports:
     - "25555:5555"
     - "29072:9072"
     - "29073:9073"
    
  # TSA Server 1
  terracotta1:
    hostname: terracotta1
    image: terracotta/terracotta-server-oss:4.3.4
    environment:
     - TC_SERVER1=terracotta1
     - TC_SERVER2=terracotta2
    networks:
     - apigw-cluster
    ports:
     - "9540:9540"

  # TSA Server 2
  terracotta2:
    hostname: terracotta2
    image: terracotta/terracotta-server-oss:4.3.4
    environment:
     - TC_SERVER1=terracotta1
     - TC_SERVER2=terracotta2
    networks:
     - apigw-cluster
    ports:
     - "19540:9540"
```
