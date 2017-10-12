<img src="https://www.docker.com/sites/default/files/mono_vertical_large.png" height="100px" />

---
Those file stands as cheatsheet in order to start using Docker properly.

- [Installation](#install)
- [Docker Service](#service)
- [Docker File](#dockerfile)
- [Docker Images](#image)
- [Docker Containers](#container)


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

```shell

```

<a name="image"/>

### Working with Docker Images

```shell
# List images available
sudo docker images

# Remove an image from repository
sudo docker rmi -f repoName:tagName

# Commit changes to a new image
sudo docker commit <containerID> <imageName>
```

<a name="container"/>

### Working with Containers

```shell
# Remove an (not running)  from repository
sudo docker rm <containerID>

# Check installation (with hello-world image)
sudo docker run hello-world

# Check running containers
sudo docker ps
sudo docker ps -l (option to see last)
sudo docker ps -a (option to see all)

# Detach from Container
Escape sequence Ctrl-p + Ctrl-q.

# Attach to Container
sudo docker attach <container_ID> 


# Start a container
sudo docker run -d -p 5555:5555 --name <containerName> <imageName>:<tag>

# Stop a container
sudo docker stop -t90 containerX

# Kill a container
sudo docker kill -t90 containerX
```

## Work with a dockerHub

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

# (1) Create a Docker container for Terracotta TSA
//TODO

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

## Using SAG DockerHub (Private)
