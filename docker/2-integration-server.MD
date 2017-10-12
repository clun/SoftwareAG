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
