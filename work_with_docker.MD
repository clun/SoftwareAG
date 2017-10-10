
This file would be used as note to list Docker commands in different environments

# General Docker

* [Installation de docker sur CentOS](https://docs.docker.com/engine/installation/linux/docker-ce/centos/#install-docker-ce)

* Utilities commands

```sh
# START docker service
sudo service docker start
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
* Login to target Docker Hub (to pull)
```sh
sudo docker images
```

Pull from DockerHub
docker pull server/repository/



# Lift n shift

# Dockerization
