

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

* Create a DockerFile to enrich previous image

```DockerFile
FROM is:apigw

# SET Environment Variable
ENV SERVERLIST_DATASTORE ["apigateway1:9340","apigateway2:9340","apigateway3:9340"]
ENV TERRACOTTA_SERVER_URL "terracotta1:9510","terracotta2:9510"

# Clustering IS
RUN echo "wrapper.java.additional.xx=-Dtest.cluster.withDerby=true" >> /sag/profiles/IS_default/configuration/custom_wrapper.conf

# Connectivity to Terracotta TSA
ADD ./terracotta-license.key /sag/Terracotta/terracotta-license.key
RUN echo "watt.server.cluster.aware=true" >> /sag/IntegrationServer/instances/default/config/server.cnf
RUN echo "watt.server.cluster.name=APIGatewayTSAcluster" >> /sag/IntegrationServer/instances/default/config/server.cnf
RUN echo "watt.server.cluster.tsaURLs=terracotta1:9510" >> /sag/IntegrationServer/instances/default/config/server.cnf
RUN echo "watt.server.terracotta.license.path=/sag/Terracotta/terracotta-license.key" >> /sag/IntegrationServer/instances/default/config/server.cnf

# Clustering DataStore
# http://localhost:9240/_cluster/health?pretty=true
ADD ./elasticsearch.yml /sag/EventDataStore/config/elasticsearch.yml

EXPOSE 9240
EXPOSE 9340
```

* Sample Test with links between APIGW and Terraccota

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
* Multiple Gateway change elasticSearch

```shell
# Link Containers
network.host: 0.0.0.0
http.port: 9240
transport.tcp.port: 9340
node.master: true
discovery.zen.ping.multicast.enabled: false
discovery.zen.ping.unicast.hosts: ${SERVERLIST_DATASTORE}
```

* Full Test with Docker Compose

```yaml
# Link Containers
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


