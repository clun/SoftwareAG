<img src="http://www.softwareag.com/corporate/images/logo_sag_150_28_tcm16-134073.jpg" height="40px" />

Coming with all SAG distribution 9.12+ is embedded a tool to dockerize your environment. In further elements we will stand by `${SAG_INSTALL}` folder where product are installed like `C:/SoftwareAG` for Windows and `/opt/softwareag` for Linux.

- [Create a Base Image](#baseimage)
- [Create an Image for your packages](#yourimage)
- [Lift'n Shift](#lift)

<a name="baseimage"/>

## Create a base image

- Run the `createDockerFile` command to create the docker fil

```shell
sudo ${SAG_INSTALL}/IntegrationServer/docker/is_container.sh createDockerfile
```

- Build Image based on dockerfile

```shell
sudo ${SAG_INSTALL}/IntegrationServer/docker/is_container.sh build
```

-  Check image creation

```shell
sudo docker images | grep micro 
sudo docker inspect --format='{{range $p, $conf := .Config.ExposedPorts}} {{$p}} {{end}}' is:micro
```

- Start a container to see if OK

```shell
sudo docker run -d -p 5555:5555 --name testIS is:micro /sag/profiles/IS_default/bin/startup.sh
sudo docker ps
```
<a name="yourimage"/>

## Build Custom image



<a name="lift"/>

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
