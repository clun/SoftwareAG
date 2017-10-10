
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

# Check installation (with hello-world image)
sudo docker run hello-world

# Check running containers
sudo docker ps

# Stop a container
sudo docker stop -t90 containerX
```

## Work with a dockerHub

```shell
# Login to target Docker Hub (to pull)
docker login -u <username> -p <password> https://frpresales.webmethodscloud.com/

# PULL
sudo docker pull repositoryName:tagName

# PUSH
sudo docker push repositoryName:tagName
```

<img src="http://www.softwareag.com/corporate/images/logo_sag_150_28_tcm16-134073.jpg" height="40px" />

## API Gateway

## Lift n shift
