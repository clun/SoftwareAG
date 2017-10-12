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

### Installation

* [Installation de docker sur CentOS](https://docs.docker.com/engine/installation/linux/docker-ce/centos/#install-docker-ce)

* Hyper-V (Windows)

```shell
# Disable hyper-V on Windows
bcdedit /set hypervisorlaunchtype off

# Enable hyper-V on Windows
bcdedit /set hypervisorlaunchtype auto 
```

<a name="service"/>

### Working with Docker service (Linux)

```shell
# START docker service
sudo service docker start
sudo systemctl start docker

# STOP docker service
sudo service docker stop
sudo systemctl stop docker

# Get history docker
sudo systemctl status docker.service -a
```

<a name="dockerfile"/>

### Working with DockerFile

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

### Working with Docker Images

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

### Working with Containers

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

### Working with Docker Volume

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
# Login to target Docker Hub (to pull)
docker login -u <username> -p <password> https://frpresales.webmethodscloud.com/

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

<img src="http://www.softwareag.com/corporate/images/logo_sag_150_28_tcm16-134073.jpg" height="40px" />

## Integration Server

```shell
# (1) Creating DockerFile for IS only
sudo ./is_container.sh createDockerfile

# (2) Build Image based on dockerfile created before
sudo ./is_container.sh build

# (3) Check image creation
sudo docker images | grep micro 
sudo docker inspect --format='{{range $p, $conf := .Config.ExposedPorts}} {{$p}} {{end}}' is:micro

# (3) Start a container to see if OK
sudo docker run -d -p 5555:5555 --name testIS is:micro /sag/profiles/IS_default/bin/startup.sh
sudo docker ps
```

## API Gateway

* Locate installation of APIGATEWAY on the system (`/opt/sag/`) and look into `./IntegrationServer/docker`
```shell
# (1) Creating DockerFile for IS only
sudo ./is_container.sh createDockerfile

# (2) Build Image based on dockerfile created before
sudo ./is_container.sh build

# (3) Create DockerFile for API GW
sudo ./apigw_container.sh createDockerfile

# (4) Build Image based on dockerfile created before
sudo ./apigw_container.sh build

# (5) Check Ports (optional)
sudo docker inspect --format='{{range $p, $conf := .Config.ExposedPorts}} {{$p}} {{end}}' is:apigw

# (6) Run Api Gateway
sudo docker run -d -p 5555:5555 -p 9072:9072 --name apigw is:apigw

# (7) Stop Api Gateway
sudo docker stop -t90 apigw
```

## Configuring an API Gateway Docker Container Cluster

* Operation listed before in "ApiGateway" should be executed before those one

```shell

# Link Containers

# A) Start a standAlone TSA
docker run --name terracotta1 -p 9510:9510 -d terracotta/terracotta-server-oss:4.3.4
# B) Start APIGW with link to TSA
docker run --name apigateway1 -p 5555:5555 -p 9072:9072 --link terracotta1:terracotta1 -d is:apigw
# C) Connect to APIGW and test communication
docker exec -it <containerID> bash
$ping terracotta1
```

# (2) Adapt IntegrationServer Configuration
SoftwareAG\IntegrationServer\instances\default\config\server.cnf as described in the cluster configuration section.

# (3) Adapt the wrapper configuration
SoftwareAG\profiles\IS_default\configuration\custom_wrapper.conf) as described in the cluster configuration section.

# (4) Adapt the Elastic Search Configuration
SoftwareAG\EventDataStore\config\elasticsearch.yml)

# (5) Docker Networks between containers

# (6) Docker Compose

```

## Lift n shift

```shell

# (1) Creating DockerFile for IS only
sudo ./is_container.sh createDockerfile

# (2) Build Image based on dockerfile created before
sudo ./is_container.sh build -Dimage.name=sagcel:integ_agent

# (3) Create DockerFile for adding packages of themicroservices
sudo ./is_container.sh createPackageDockerfile -Dimage.name=sagcel:integ_agent -Dpackage.list=MicroServiceHello

# (4) Build Image based on dockerfile created before (sagcel:integ_agent)
sudo ./is_container.sh buildPackage -Dimage.name=sagcel:hello_on_integ_agent

# (5) Upload from integration Cloud
sudo ./is_container.sh pushImage -Duser=<login_integration_cloud> -Dserver=frpresales.webmethodscloud.com -Dstage.name=development -Dsubdomain.name=frpresales -Dimage.name=sagcel:hello_on_integ_agent
```

<img src="https://avatars2.githubusercontent.com/u/4680389?v=4&s=200" height="40px" />

## Installation

```shell
docker run --name tc-server -p 9510:9510 -d terracotta/terracotta-server-oss:4.3.4


```

<img src="https://static-www.elastic.co/assets/blt6050efb80ceabd47/elastic-logo%20(2).svg?q=994" height="40px" />

## Install ElasticSearch

```shell

# Installation
docker pull docker.elastic.co/elasticsearch/elasticsearch:5.6.3

# Running
docker run -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:5.6.3
```



