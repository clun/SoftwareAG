<img src="https://www.docker.com/sites/default/files/mono_vertical_large.png" height="100px" />

---
Those file stands as cheatsheet in order to start using Docker properly.

- [Installation](#install)
- [Docker Service](#service)
- [Docker File](#dockerfile)
- [Docker Images](#image)
- [Docker Volumes](#volume)
- [Docker Containers](#container)
- [Docker Network](#network)
- [Docker Hub](#hub)

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

## Working with Docker service (Linux)

Docker is a command line tools but the service behind should be started or the Windows Application should be started.

* Linux : Start and Stop Docker Service

```shell
# START docker service
sudo service docker start
sudo systemctl start docker

# STOP docker service
sudo service docker stop
sudo systemctl stop docker
```

* Once started you can information of your environment

```shell
# Get Version
docker version

# Get info (containers, images, settings)
docker info

# <img src="http://ff4j.org/external-pics/icon-linux.png" height="20px"/> Get history docker
sudo systemctl status docker.service -a
```

<a name="dockerfile"/>

## Working with DockerFile

* Sample File
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

* Create an image from DockerFile [doc](https://docs.docker.com/engine/reference/commandline/build/)
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

# Remove an image from repository
sudo docker rmi -f repoName:tagName

# Commit changes to a new image
sudo docker commit <containerID> <imageName>

```

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

# Go as SSH
sudo docker exec -it <container_ID> bash

# Start a container
# -d is for detach
# -i is fot interactive
# -t is fot interactive
sudo docker run -d -p 5555:5555 --name <containerName> <imageName>:<tag>

# Stop a container
sudo docker stop -t90 containerX

# Kill a container
sudo docker kill -t90 containerX

# Remove an (not running)  from repository
sudo docker rm <containerID>
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

<a name="hub"/>

## Working with dockerHub

```shell

# FOR WINDOWS & INSECURE REGISTRIES
# Please edit the 


# Login to target Docker Hub (to pull)
docker login -u <username> -p <password> XXX

# TAG
sudo docker  tag repositoryName:tagName urldockerhub/repositoryName:tagName

# PUSH
sudo docker push urldockerhub/repositoryName:tagName

# PULL
sudo docker push urldockerhub/repositoryName:tagName
```

<a name="network"/>

## Working with Docker Network

```shell
# List networks
docker network ls

# See bridge (172.17.0.1 default gateway, 172.17.0.X default IP)
docker network inspect bridge

# Use default 'bridge' on leverage on 'link'
docker run -d --name redis1 redis
docker run -d --link redis1:redis1 --name container2 busybox

# Create user-defined network 
docker network create --driver bridge myNetwork

# Run docker using target network
docker run --network=myNetwork -itd --name=container3 busybox
```
