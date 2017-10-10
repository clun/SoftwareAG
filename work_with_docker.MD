
Those notes are reminder to ease usage of Docker in General but also its implementation with SoftwareAG products.

<img src="https://www.docker.com/sites/default/files/mono_vertical_large.png" height="100px" />

## Basic Commands

* [Installation de docker sur CentOS](https://docs.docker.com/engine/installation/linux/docker-ce/centos/#install-docker-ce)

* Utilities commands

```shell
# START docker service
sudo service docker start
OR
sudo systemctl start docker

# List images available
sudo docker images

# Remove an image from repository
sudo docker rmi -f repoName:tagName

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

# Commit changes to a new image
sudo docker commit <containerID> <imageName>

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

# (5) Run Api Gateway
sudo docker run -d -p 5555:5555 -p 9072:9072 --name apigw is:apigw

```


## Lift n shift

```shell

```
